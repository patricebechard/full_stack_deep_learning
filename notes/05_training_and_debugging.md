# 05 - Training and Debugging

## Overview

> Why is deep learning troubleshooting hard?

### Why talk about DL troubleshooting?

> Debugging : first it doesn't compile. Then doesn't link. Then segfaults. Then gives all zeros. Then gives wrong answer. Then only maybe works.

Common sentiment among practitioners
* 80-90% of time debugging and tuning
* 10-20% deriving math or implementing things

### Why is DL troubleshooting so hard?

Suppose you can't reproduce a result
* WHy is performance worse?
    - Maybe implementation bugs (most DL bugs are invisible)
    - Hyperparameter choices (models are sensitive to hparams, e.g. learning rate, weight initialization)
    - Data/Model fit
    - Dataset construction (we should spend time on this)

### Strategy for DL troubleshooting

1. Start Simple
2. Implement and debug
3. Evaluate
    - if meets requirements, go to 6
    - if not, go to 4 or 5
4. Tune hyperparameters
    - go to 3
5. Improve model/data
    - go to 2
6. Meets requirements (you're done!)

## Start Simple

Steps
1. Choose a simple architecture
2. Use sensible defaults
3. Normalize inputs
4. Simplify the problem

### Choose a Simple Architecture

#### Demystifying architecture selection
Images
* start here : LeNet-like architecture
* consider using this later : ResNet
Sequences
* start here : LSTM with one hidden layer (or temporal convs)
* consider using this later : Attention model or WaveNet-like model
Other
* start here : Fully connected neural net with one hidden layer
* consider using this later : problem-dependent

#### Dealing with multiple input modalities

1. Map each into a lower dimensional feature space
    - e.g. map images through CNN, sequences through LSTMs, 
2. Concatenate representations
3. Pass through fully connected layers to output

### Use sensible defaults

Recommended network / optimizer defaults
* Optimizer : Adam optimizer with lr=3e-4
* Activations : relu (FC and Conv models), tanh (LSTMs)
* Initialization: He et. al. normal (relu), Glorot normal (tanh)
* Regularization : None
* Data normalization : None

### Normalize inputs

* Subtract mean and divide by variance
* for images, fine to scale values to [0,1] or [-0.5, 0.5] (e.g. by dividing by 255)

### Simplifying the problem

* Start with a small training set (~10k examples)
* Use a fixed number of objects, classes, image size, etc.
* Create a simpler synthetic training set

## Debug

Implement bug-free DL models
1. Get your model to run
2. Overfit a single batch
3. Compare to a known result

### Preview : five most common DL bugs
1. Incorrect shapes for your tensors
    - Can fail silently! (e.g. accidental broadcasting : x.shape = (None,), y.shape=(None, 1, (x+y).shape = (None, None)))
2. Pre-processing inputs incorrectly
    - e.g. forgetting to normalize, or too much pre-processing
3. Incorrect input to your loss function
    - e.g. softmaxed outputs to a loss that expects logits
4. Forgot to set up a train mode for the net correctly
    - E.g. toggling train/eval, controlling batch norm dependencies
5. Numerical instability (inf/NaN)
    - Often stems from using an exp, log, or div operation

### General advice for implementing model
* Lightweight implementation
    - Minimum possible new lines of code for v1
    - Rule of thumb : <200 lines of code
    - (tested infrastructure components are fine)

* Use off-the-shelf components
    - e.g. keras
    - tf.layers.dense(...) instead of tf.nn.relu(tf.matmul(W, x))
    - tf.losses.cross_entropy(...) instead of writing out the exp

* Build complicated data pipelines later
    - start with a dataset you can load into memory

### Get your model to run

> Step through in debugger & watch out for shape, casting, and OOM errors

Common issues:
* shape mismatch
    - step thgough model creation and inference in a debugger
* casting issue
    - same
* OOM issues
    - scale back memory intensive operations one by one
* Other
    - Standard debugging toolkit (Stack Overflow + interactive debugger)

#### Debuggers for DL code
Pytorch :
    - easy, use `ipdb`
Tensorflow:
    - trickier
    - option 1 :
        + step through graph creation
    - option 2 :
        + step into training loop
    - option 3 :
        + use `tfdb`
        + stops execution at each `sess.run(...)` and lets you inspect
    
See slides for other common errors

### Overfit a single batch

> Look for corrupted data, over-regularization, broadcasting errors

Common issues
* Error goes up
    - flipped signed somewhere for loss fct / gradient
    - learning rate too high
    - softmax taken over wrong dimension
* Error explodes
    - Numerical issue. Check all exp, log, and div operations
    - high learning rate
* Error oscillates
    - Data or labels are corrupted (e.g. zeroed, incorrectly shuffled, or preprocessed incorrectly)
    - learning rate is too high
* Error plateaus
    - Learning rate too low
    - gradient not flowing through the whole model
    - too much regularization
    - incorrect input to loss fct (e.g. softmax instead of logits, accidently add relu on output)
    - data or labels corrupted


### Compare to a known result

> Keep iterating until model performs up to expectations

More useful : official model implementation evaluated on similar dataset to yours
* we can walk through code line by line and ensure we have same output
* ensure performance is up to par with expectations

A bit less useful : official model implementation evaluated on benchmark (e.g. mnist)

A bit less useful : unofficial model implementation
* be careful! most models online have bugs

Compare to results in paper

Result of our models on a benchmark dataset

Result form a simpler model on your data

Super simple baselines (e.g. avg of outputs or linear regression)


## Evaluate

Evaluate using the *bias-variance* decomposition.

Review :
* let's say we have a human level performance on a given task
* We have the training curve that approaches that performance
* Validation error is a bit higher than that
* and test error is also a bit higher than validation

Bias-variance decomposition tells us the decomposition between
* irreducible error (data labeled by humans, so we shouldn't be able to beat it for example)
* avoidable bias (e.g. underfitting) (difference between irreducible error and train error)
* Variance (overfitting) (different between train error and validation error)
* Validation set overfitting (difference between val error and test error)

SUmmary :

> Test error = irreducible error + bias + variance + val overfitting

This assumes train, val and test all come from the same distribution. What if not?

### Handling distribution shift
* train data is pedestrian distribution during day
* test data is during the night!

Solution : Use two validation sets
* One sampled from training distribution
* one from the test distribution

That can give us a new curve (train validation error) allowing us to compute some **distribution shift**.

What do we do based on the situation? (e.g. we are underfitting and overfitting at the same time on the datasets we deal with)

### Summary 

> test error = irreducible error + bias + variance + distribution shift + val overfitting


## Improve

Prioritizing improvements (applied b-v)
1. Address under-fitting
2. Address over-fitting
3. Address distribution shift
4. re-balance datasets (if applicable)

### Addressing underfitting (i.e. reducing bias)
Try first to try later...
1. Make model bigger (add layers, more units per layers)
2. Reduce regularization
3. Error analysis
4. Choose a different model architecture (closer to sota, e.g. from LeNet to ResNet)
5. Tune hyperparameters (e.g. learning rate)
6. Add features

### Addressing overfitting (i.e reduce variance)
Try first to try later...
1. Add more training data (if possible)
2. Add normalization (e.g. batch norm, layer norm)
3. Add data augmentation
4. Increase regularization (e.g. Dropout, L2, weight decay)
5. Error analysis
6. Choose a different (closer to SOTA) model architecture
7. Tune hyperparameters
8. Early Stopping
9. Remove features
10. Reduce model size

Last 3 are not recommended...

### Addressing Distribution Shift
Try first to try last...
1. Analyze test-val set errors & collect more training data to compensate
2. Analyze test-val set errors & synthesize more training data to compensate
3. Apply domain adaptation techniques to training and test distributions

### Error analysis
Look at actual examples!

* error types we can define (e.g. difficult to see pedestrians in some images, reflection in windshield, not enough data in some settings...)

"Clustering" there errors in different types, we can prioritize which type of error can be solved more quickly based on available solutions and difficulty of problem.

### Domain adaptation

* What is it?
    - Techniques to train on "source" distribution and generalize to another "target" using only unlabeled data or limited labeled data
* When should you consider using it?
    - Access to labeled data from test distribution is limited
    - Access to relatively similar data distribution is plentiful

### Types of domain adaptation
* supervised
    - use when limited data from target domain
    - ex techniques
        + fine-tuning a pretrained model
        + adding target data to train set
* unsupervised
    - use when lots of unlabeled data from target domain
    - ex techniques
        + correlation alignment (CORAL)
        + domain confusion
        + CycleGAN

### Re-balance dataset (if applicable)

* If val looks significantly better than test, you overfit to the val set
* this happens with small val sets or lots of hyperparameter tuning
* When it does, recollect val data

## Tuning Hyperparameters

Hyperparameter optimization. WHat are the choices?
* Network : Resnet
    - how many layers
    - weight initialization
    - kernel size
    - etc.
* Optimizer : Adam
    - batch size?
    - learning rate?
    - beta1, beta2, epsilon?
* Regularization
    - ...

Some models are more sensitive to some hyperparameters than others
* Depends on choice of model
* rule of thumb only
* sensitifity is relative to default values
    - e.g. if you are using all-zeros weights initialization or vanilla sgd, changing the defaults will make a big difference

Most sensible hparams normally
* learning rate (and schedule)
* layer size
* loss fct

less important but still
* weight initialization
* model depth
* layer params (e.g. kernel size)
* weight regularization

### Method 1 : manual hyperparameter optimization
* how it works
    - understand algorithm
    - train and evaluate model
    - guess better hparam value and reevaluate
    - can be combined with other methods
* advantages
    - for a skilled practitioner, may require least computation to get good result
* disadvantages
    - requires detailed understanding of algorithm
    - time consuming

### Method 2 : Grid search
* how it works
    - define range of hyperparameters you want to tune
    - try every combination
* advantages
    - super simple to implement
    - can produce good results
* disadvantages
    - not very efficient (need to train on all cross-combos of hparams)
    - may require prior knowledge about parameters to get good results

### Method 3 : random search
* how it works
    - select ranges of hparams but sample random points from that range
* advantages
    - easy to implement
    - often produces better results than grid search
* Disadvantages
    - not very interpretable
    - may require prior knowledge about parameters to get good results

### Method 4 : coarse-to-fine searches
* how it works
    - random search
    - select best performers
    - resample
* advantages
    - can narrow on very high performing hyperparameters
    - most used method in practice
* disadvantages
    - somewhat manual process

### Method 5 : Bayesian Optimization
* How it works (high level)
    - start with a prior estimate of parameter distributions
    - maintain probabilistic model of relationship between hparam choice and model performance
    - alternate between
        + train with hparam tha maximises improvements
        + using training results to update probabilistic model
* Advantages
    - generally most efficient hands-off way to choose hparams
* Disadvantages
    - difficult to implement from scratch
    - can be difficult to intrgrate with off the shelf tools

### Summary

* Coarse to fine random searches (easy to use, works well)
* move to bayesian optimization when we get comfortable with code base and problem

## Conclusion

* DL debugging is hard due to many competing sources of error
* To train bug-free DL models, we treat building our model as an iterative process
* The following steps can make the process easier and catch errors as early as possible

Steps
1. Choose simplest model and data possible
2. once model runs, overfit single batch, and reproduce known results
3. Apply the bias-variance decomposition to decide what to do next
4. Use coarse-to-fine random searchse to tune the model's hyperparameters
5. Make your model bigger if your model underfits and add more data and/or regularization if your model overfits

