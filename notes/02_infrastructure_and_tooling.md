# 02 - Infrastructure and Tooling (Sergey Karayev)

## Overview

> What are the components of a machine learning system?

Dream
1. Provide Data
2. Get optimal prediction system as scalable API or mobile app

Reality
1. Aggregate, clean, store, label and version data
2. Write and debug model code
3. Provision computer resources
4. Run experiments, storing results
5. test and deploy model
6. Monitor predictions and close the data flywheel loop

Google paper 
* Machine Learning : The High-Interest Credit Card of Technical Debt

Machine learning is only a small part of all the code base.

### Getting to the goal

To do with Data
* hard drive or cloud?
* How is it stored?
* what databases?
* How to process it?
* How to label it
* How to version it

To do with Development / Training / Evaluation
* Local or cloud based
* Software engineering tools to use?
* Resource management?
* What frameworks to use for ML and DL?
* How and what to do with multiple GPUs
* How to store experiments management
* How to tune hyperparameters?

Deployment
* CLoud or embedded system or mobile?
* Continuous integration and testing (what tool to use?)
* Deployed on the web?
* How to monitor predictions?
* Interchange between platforms?
* how to distil network to deploy on different platforms?

ALL-IN-ONE methods offered by some solutions to reduce number of questions we need to ask ourselves.

## Software Engineering

> What are the good software engineering practices for Machine Learning developers?

### Programming Language

* Python, **because of the libraries**
    - clear winner in scientific and data computing

### Editors

* Vim, Emacs
* Jupyter (for data science, but maybe not for software development)
* VS Code (one of the good ones)
* PyCharm (what people use to have full IDE for python)

VSCode makes for a very nice Python experience with
* built-in git staging and diff
* peek documentation through editor
* open whole projects remotely
* lint code as you write

#### Linters and Type Hints
* Whatever code style rules can be codified should be.
* Static analysis can catch some bugs
* Static type checking both documents code and catches bugs

#### Jupyter Notebooks
* Have become fundamental to data science
    - great as the "first draft" of a project
    - Jeremy Howard from fast.ai good to learn from
* Difficult to make scalable, reproducible, well tested

#### Problems with Notebooks
1. Hard to version (might be good as additional documentation tool)
2. Notebook "IDE" is primitive
3. Very hard to test
4. Out-of-order execution artifacts
5. Hard to run long or distributed tasks

### Streamlit

* New, but great at fulfilling a common ML need, interactive applets

Normal pipeline
1. explore jupyter notebook
2. copy-paste in python script
3. write flask app, think about http requrests, ...
4. investigate (shit we need more features! let's go back)

Streamlit
1. sprinkle a few api calls into existing python script
2. show off results directly in there


VSCode extensions that are useful
* Python extension
* Extension for Notebooks

Recommended code styles or numpy stypes?
* to be discussed later

## Computing and GPUs

### Compute Needs

* Development
    - Function
        + Writing code
        + Debugging models
        + Looking at results
    - Desiderate
        + quickly compile models and run training
        + Nice-to-have : use GUI
    - Solutions
        + Desktop with 1-4 GPUs
        + cloud instance with 1-4 GPUs

* Training/Evaluation
    - Functions
        + Model architecture / hparam search
        + training large models
    - Desiderata
        + Easy to launch experiments and review results
    - Solutions
        + Desktop with 4 GPUs
        + Private cluster of GPU machines
        + Cloud cluster of GPU instances

### Why Compute Matters?

TO some degree, there is a trend where most advancements come from additional compute power. (cnns, transformers, ...)

BUT! Creativity also matters!
* recent results that use modest amount of compute (attention is all you need, GANs, word2vec, VAEs...)


### GPU Basics
* NVIDIA has been the only game in town
* Google TPUs are the fastest option (on GCP)
* Intel is trying with Nervana NNPs and AMD also

New NVIDIA architecture every year 
* kepler -> maxwell -> pascal -> volta -> turing
* server version, then "enthusiast", then consumed
* for business only supposed to use server cards
* RAM (should fit meaningful batches of your model)
* 32bit vs tensor tflops
    - tensor cores are specifically for DL operations
    - Good for CNN / Transformer models

#### Kepler / Maxwell
* 2-4x slower than Pascal/Volta
* Hardware : don't buy, too old
* cloud k80s are cheap (providers are stuck with it since they bought them)


#### Pascal / Volta
* Hardware : 1080ti still good if buying used, especially for rnns
* cloud : P100 is a mid-range option

#### Turing
* preferred choice right now due to 16bit mixed precision support
* hardware (2080ti 1.3 as fast as 1080ti, 2x faster in 16bit)
* V100 is fastest right now (not true anymore)

#### CLoud providers
* AWS, GCP, MS Azure are heavyweights
* THey are all similar
    - aws most expensive
    - gcp lets you connect GPUs to any instance, and has TPUs
    - Azure reportedly has bad user experience
* Startups are Paperspace, Lambda Labs

#### On-Prem Options
* Build your own
* Buy prebuild (e.g. lambda labs)

* Quiet PC with 64gb and 4x 2080ti about 7k$
    - Going beyond 4 gpus is painful
* Pre-build is probably best way to go for businesses
    - Nvidia
    - Lambda Labs also (20% more than building yourself)

#### Cost Analysis
* Quad PC vs Quad core?
    - if running at all times, it takes about 5 weeks for GPUs to repay themselves vs cloud
* For quick experiment (quad pc vs spot instances)
    - time savings by parallelizing compute on multiple instances
* In practice
    - when scaling a large lab, might become tedious to always add computers to the cluster
    - devops is simpler on cloud
* for startups
    - development
        + build or buy quad pc
    - training/experiments
        + use same 4x gpu pc until architecture is dialed
        + when running many experiments, buy shared server machines or cloud instances
* for companies
    - Development
        + buy 4x turing architecture PC per ML scientist
        + or let them use V100 instances
    - training / evaluation
        + use cloud instances with proper provisioning and handling of failures


## Resource Management

* Function
    - Multiple people
    - Multiple multiple gpus/machines
    - running different environments
* Goal
    - Easy to launch a batch of experiments with proper dependencies
* Solutions
    - spreadsheet
    - python scripts
    - SLURM
    - Docker + Kubernetes
    - Software specialized for ML use cases

### Spreadsheets
* users reserve what they need (really low tech and awful, still common)

### Scripts or SLURM
* problem solved : allocate free resources to programs
* can be scripted pretty easily
* even better : use old-school cluster job scueduler

### Docker + Kubernetes
* docker
    - docker is a way to package up an entire dependency stack in a lgihter-than-a-vm package
    - we will use it more in labs
* Kubernetes 
    - is a way to run many docker containers on top of a cluster

* KubeFlow
    - open source project from Google
    - spawn and manage jupyter notebooks
    - manage multi-step workflows (e.g. 64 cores for preprocessing, and then 2 gpus for training the model)
    - plugins for hparam tuning and model tuning

* Polyaxon also exists

## Frameworks and Distributed Training

### Frameworks

Different deep learning frameworks. Some are good for production, others are good for development, we would want the best of both worlds

* Caffe (2013) good for production, not much for development
    - need to compute the backwards pass before
    - good for production because in CPP
* TF (2015)
    - meta-programming way of writing model
    - coding the graph, and then executing
    - not really pythonic
* Keras (2015)
    - easier to develop DL models
    - on top of tensorflow
* PyTorch (2017)
    - really good for development, a bit less for production because executing python code
    - used by most starting projects today
* unless you have a good reason, use TF/Keras or PyTorch
    - both are converging to same ideal point (TF + eager execution, PyTorch + optimized execution graph TorchScript)
* Fast.ai library worth starting with for immediate best practices

### Distributed Training

* Using multiple GPUs and/or machines to train single model
* Makes sense if model is large or data is large

* Data Parallelism
    - if iteration time is too long, try training in data parallel regime
    - for convolutions, expect 1.9x to 3.5x speed up for 2/4 gpus
    - workers on each gpu, then shared model states updates the weights of each model
* Model Parallelism
    - Necessary when model does not fit on a single GPU
    - Some of the weights live on one gpu, others fit on other GPU
    - Introduces a complexity and usually not worth it
    - better to buy larger GPUs with more memory

* Data parallelism is easy in TF or PT also
* different ways to make these things easier
    - Ray
    - Pytorch Lightning
    - Huggingface Transformers Trainer
    - Horovod (from Uber) for distributed training with different platforms


## Experiment Management

* Even running one experiment at a time, we can lose track of which code, parameters, dataset generated which model and which metric

* Tensorboard
    - fine for a single experiment
    - gets difficult to manage many experiments
* Losswise
    - every time you train, git commit automatically, will track loss, metrics, time to train
* Comet.ml
    - similar idea
* Weights and Biases
    - what is going to be used in lab 3
* MLFlow tracking
    - open source plaform for whole ML lifecycle
    - made by DataBricks

## Hyperparameter Tuning

* Useful to have software that helps you search over hyper parameter settings

* Could be as simple as being able to provide range of learning rate, number of layers, ...
* Hyperopt useful to do this
    - Hyperas is a wrapper for hyperopt and Keras
    - algorithms : random search, tree of Parzen...
* Sigopt
    - hyperparameter tuning as a service
* Ray
    - has a Tune library
    - chooses scalable SOTA algorithms to tune hyperparameters
    - hyperband is a paper about these
* W&B is also a platform that offers that

## All-in-one Solutions

* Single system for everything
    - development (hosted notebook)
    - scaling experiments to many machines (sometimes even provisioning)
    - tracking experiments and versioning models
    - deploying models
    - modeling performance

* First approach to do this : FBLearner Flow
* Michaelangelo from Uber
* TFX, from google, became kind of the backbone for GCP
* Amazon SageMaker
* Neptune does that also
* Floyd

a bunch of different platforms...

* Domino Data Lab
    - one place for data science tools, apps, results, models, and knowledge
    - provides compute
    - deploy rest apis
    - track experiments
    - monitor predictions
    - publish applets
    - monitor spending
    - everything in one place

