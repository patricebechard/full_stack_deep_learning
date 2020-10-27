# 06 - Testing and Deployment

> It is useful to break down the different systems and tests necessary for a successful ML project.

## Project Structure

> What are the different components of a machine learning system?

### Prediction system

Here we use the training / val data for everything

* Process input data
* Construct networks with trained weights
* Make predictions

### Training system

Used to by the prediction system. We also use the training / val data here.

* Process data
* Run experiments
* Manage results

### Serving System

Here we use production data

* Serves predictions
* Scale to demand

### Functionality Tests

Used by prediction system

* Tests used by prediction system on a few important examples
* Run in <5 minutes (during development)
* Catches code regressions

### Validation Tests

Used by prediction system

* Test prediction system on a validation set
* Start from processed data
* Runs in < 1 hour (every code push)
* Catches model regressions

### Training system tests

Used by training system
* Test full training pipeline
* Start from raw data
* runs in < 1 day (nightly)
* Catches upstream regressions

### Monitoring

For production data

* Alert to downtime, errors, distribution shifts
* Catches service and data regressions

## ML Test Score

From a Google paper

> The ML Test Score : A Rubric for ML Production Readiness and Technical Debt Reduction

This is an exhaustive framework/checklist from practitioners at Google.

### Traditional software

* Code
    - unit tests
    - integration tests
* Running system
    - System monitoring

### Machine Learning Software

Much more complicated

More tests
* data tests
* data skew tests
* data monitoring
* ML infrastructure tests
* Model tests
* Unit tests
* integration tests
* system monitoring

 ### Data tests
 * Feature expectations are captured in a schema
 * all features are benificial
 * No feature's cost is too much
 * Data pipeline has appropriate privacy controls
 * New features can be added quickly
 * All input feature code is tested

 ### Model Tests
 * Model specs are reviewed and submitted
 * Offline and online metric correlate
 * All hyperparameters have been tuned
 * The impact of model staleness is known
 * A simpler model is not better
 * Model quality is sufficient on important data slices
 * The model is tested for considerations of inclusion

 ### ML Infrastructure Tests
 * Training is reproducible
 * Model specs are unit tested
 * The ML pipeline is Integration tested
 * Model quality is validated before serving
 * Model is debuggable
 * Models are canaried before serving
 * Serving models can be rolled back

 ### Monitoring Tests
 * Dependency changes result in notification
 * Data invariants hold for inputs
 * Training and serving are not skewed
 * Models are not too stale
 * Models are numerically stable
 * Computing performance has not regressed
 * Prediction quality has not regressed

 ### Scoring the Test

if 0 : more of a research project than a production model

if >5 : exceptional levels of automated testing and monitoring

They did analysis at google to see the scores based on teams. Average was <1 , so there's a lot of room to grow.

## CI / Testing

* Unit / Integration tests
    - Tests for individual module functionality and for the whole system
* Continuous integration
    - Tests are run every time new code is pushed to the repository before updated model is deployed
* SaaS for CI
    - CircleCI, Travis, Jenkins, Buildkite
* Containerization (via Docker)
    - A self-enclosed environment for running the tests

### CircleCI, Travis, etc.
* SaaS for continuous integration
* Integrates with repo, every push kicks off a cloud job
* Job can be defined as commands in a Docker container, and can store results for later review
* No GPUs
* CircleCI has a free plan

### Jenkins / Buildkite

* Nice option for running CI on your own hardware, in the cloud, or mixed
* Good option for a scheduled training test (can use your GPUs)
* Very flexible

### Docker

* Before proceeding, we have to understand Docker better
    - Docker vs VM
    - Dockerfile and layer-based images
    - DockerHub and the ecosystem
    - Kubernetes and other orchestrators

No OS -> Lightweight container

How do VMs work?
* server -> host OS -> hypervisor -> VM layer with guest os, bins, libs, apps

How do Docker work?
* Server -> Host os -> Docker engine -> bins, libs, apps...

Lightweight -> Heavy use
* Can spin up a container for every discrete task
* for example a webapp might have 4 containers
    - web server
    - database
    - job queue
    - worker


* **Dockerfile** : The way we specify docker images.

#### Strong ecosystem
* we can build on top of what others have done, starting from python image that already exists, and then continue building on top of it. People can also build on top of what we do, ...

* Images are easy to find, modify and contribute back to DockerHub
* Private images easy to store in the same place

Really been growing over the last couple of years

### Container Orchestration

* Distributing containers onto underlying BMs or bare metal
* Kubernetes is the open-source winner, but cloud providers have good offerings, too.



## Web Deployment

### Terminology
* **Inference** : Making predictions (not training)
* **Model Serving** : ML-specific deployment, usually focused on optimizing GPU usage

### Web Deployment

* REST API
    - Serving predictions in response to canonically-formatted HTTP requests
    - The web server is running and calling the prediction system
* Options
    - Deploying code to VMs, scaling by adding instances
    - Deploying code as containers, scale via orchestration
    - Deploy code as a "serverless function"
    - Deploy via a model serving solution

### Ways to process

* Deploy code to own bare metal (not way to do it)
* Deploy code to cloud instances (more robust)

Reason : Scale
* you won't be able to scale fast enough if you have everything locally

### Deploying code to cloud instances
Old school way
* Each instance has to be provisioned with dependencies and app code
* Number of instances is managed manually or via auto-scaling
* Load balancer sends user traffic to the instances
* **CONS** :
    - Provisioning can be brittle
    - Paying for instances even when not using them (auto-scaling does help)

### Best way to deploy (2019)
* Deploy Docker containers to cloud instances

* App code and dependencies are packaged into Docker containers
* Kubernetes or alternative orchestrates containers (DB / workers / web servers / etc.)
* **CONS** :
    - Still managing your own servers and paying for uptime, not compute-time

### Maybe the future : Deploy Serverless Functions

* App code and dependencies are packaged into .zip files, with a single entry point function
* AWS Lambda (or Google Cloud Functions, or Azure Functions) manages everything else
    - instant scaling to 10k+ requests per second
    - load balancing
    - etc.
* Only pay for compute-time
* **CONS** :
    - Entire deployment package has to fit within 500MB, <5min execution, <3GB memory (on AWS Lambda)
    - Limited to CPU execution

* Canarying
    - Easy to have two versions of Lambda functions in production, and start sending low volume traffic to one
* Rollback
    - Easy to switch back to the previous version of the function

### Model Serving

* Web deployment options specialized for machine learning models
    - Tensorflow Serving (Google)
    - Model Server for MXNet (Amazon)
    - Clipper (Berkeley RISE Lab)
    - SaaS solutions like Algorithmia

* The most important feature is batching requests for GPU inference

#### Tensorflow serving
* Able to serve TF predictions at high throughput
* Used by Google and their all-in-one ML offerings
* overkill unless you know you need to use GPU for inference

#### MXNet Model Server
From Amazon

Clipper is another example, algorithmia also...

### Takeaway
* If you are doing CPU inference, you can get away with scaling by launching more servers, or going serverless
    - Dream is deploying Docker as easily as Lambda
    - Next best thing is either Lambda or Docker, depending on your model's needs
* If using GPU inference, things like TF Serving and Clipper become useful by adaptive batching, etc.


## Monitoring

* Monitoring low level system status, higher level KPIs, ...

* Alarms for when things go wrong, and records for tuning things
* Cloud providers have decent monitoring solutions
* Anything that can be logged can be monitored (i.e. data skew)

### Data Distribution Monitoring
* Underserved need!
* Domino DataLab gives a good way to do this

### Closing the Flywheel
* Important to monitor the *business uses* of the model, not just its own statistics (how users interact with the model)
* Important to be able to controbute failures back to dataset

## Hardware / Mobile

### Problems

* Embedded and mobile frameworks are less fully featured than full PyTorch / Tensorflow
    - Have to be careful with architecture
    - Interchange format

* Embedded and mobile devices have little memory and slow/expensive compute
    - Have to reduce network size / quantize weights / distill knowledge

### Tensorflow Lite
* Not all models will work, but much better than Tensorflow Mobile used to be
    - Pick a model (pick new model or retrain existing one)
    - Convert (convert a tf model into compressed flat buffer with TF lite converter)
    - Deploy (take compressed .tflite file and load it into a mobile or embedded device)
    - Optimize (quantize by converting 32-bit floats to more efficient 8-bit integers or run on GPU)

### PyTorch Mobile
* Optional Quantization
* TorchScript : static execution graph
* Android : MAVEN pytorch android
* IOS : Cocoapods libtorch

### Interchange Format
* Open Neural Network Exchange (ONNX) : open-source format for deep learning models
* The dream is to mix different frameworks, such as frameworks that are good for development (PyTorch) don't also have to be good at inference (Caffe2)

### Other
* CoreML (Apple)
    - inference only
* ML Kit (Google)
    - either via API or on-device
    - offers pretrained models or can upload via tf lite
* Fritz

### Embedded devices

NVIDIA for Embedded


### Reduce Network Size

MobileNets
* Different way of thinking about CNNs
* Separating out spatial step and channel-wise step for CNNs

### Distill Knowledge

* First, train a large "teacher" network on the data directly
* Next, train a smaller "student" network to match the "teacher" network's prediction distribution

Interesting Case Study : DistilBERT