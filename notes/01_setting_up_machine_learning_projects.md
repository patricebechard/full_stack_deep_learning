## 01 - Setting Up Machine Learning Projects

Module Overview :

1. Lifecycle (How to think about all of the activities in an ML project)
2. Prioritizing projects (Assessing feasability and impact)
3. Archetypes (Main categories of ML projects, and implications for project management)
4. Metrics (How to pick a single number to optimize)
5. Baselines (How to know if model is performing well)

## Overview

> According to a 2019 report, 85% of AI projects fail to deliver their promise to business. Why do so many projects fail?

Summary :
* ML is still research, therefore it is really challenging to aim for 100% success rate
* Many ML projects are technically infeasible or poorly scoped
* Many ML projects never make the leap into production
* Many ML projects have unclear success criteria
* Many ML projects are poorly managed

Running case study related to pose estimation. Why is this useful?
* let's say you have a robot who catches objects, you want to know where the object is and what angle it has.

## Lifecycle

1. Planning and project setup
    - decide to work on pose estimation
    - determine requirements and goals
    - allocate resources
    - etc.
2. Data Collection and labeling
    - Collect training objects
    - Set up sensors (e.g. cameras)
    - capture images of objects
    - annotate with ground truth (how?)
3. Training and Debugging
    - Implement baseline in OpenCV
    - Find SoTA model & reproduce
    - Debug implementation
    - Improve model for specific task
4. Deployment and Testing
    - Pilot in grasping system in the lab
    - Write tests to prevent regressions
    - Roll out in production

You can always go back to the previous step during the project lifecycle. It is not linear.

E.g. 
* during data collection phase
    - maybe too hard to get data
    - easier to label a different task (e.g. hard to annotate pose, easier to annotate per-pixel segmentation)
* during training
    - we're overfitting, so we need more data (go back to data)
    - maybe labeling is unreliable (go back to data)
    - maybe task is too hard (go back to project planning)
    - maybe requirements tradeoff with each other, need to revisit to see which are most important
* During deployment and testing
    - doesnt work in the lab, keep on improving accuracy of model (training phase)
    - Fix data mismatch between training data and data seen in deployment (data phase)
    - collect more data (data phase)
    - mine hard cases (data phase)
    - metrics picked don't work (revisit first step)
    - performance in the real world isn't great. Revisit requirements (e.g. speed or more accuracy)


Cross Project infrastructure is also important
* strong teams
* strong infrastructure to enable teams

What else do you need to know?
* What is SoTA
    - understand what is possible
    - know what to try next
* They will introduce interesting research directions

## Prioritizing

> Assessing the feasability and impact of your project

Key points for prioritizing projects
1. High impact ML problems
2. Feasability of ML projects

You can represent it as a grid of impact vs feasability (e.g. cost)

Priority should be high feasability & high impact

1. Where can you take advantage of cheap prediction?
2. Where can you automate complicated manual processes?

### The Economics of AI
1. AI reduces cost of prediction
2. Prediction is central for decision making
3. Cheap prediction means
    1. Predictions will be everywhere
    2. Even in problems where it was too expensive before (e.g. for most people, hiring a driver)
4. Implication : Look for projects where cheap prediction will have a huge business impact

### Software 2.0
> Gradient descent can write better code than you.
* Traditional software : explicit instructions (python, cpp, ...)
* software 2.0 : humans specify goals instead of rules, and algorithms searches for a program that works
* programmers work with datasets compiled via optimization
* Why? Works better, more general, computational advantages
* Implication : look for complicated rule-based software where we can learn the rules instead of programming them

### Cost drivers :
* Problem difficulty
    - good published work on similar problems?
    - compute needed for training
    - compute available for deployment
* Accuracy requirements
    - how costly are wrong predictions
    - how frequent does the system need to be right to be useful?
* Data availability (most important one)
    - How hard is it to acquire data?
    - How expensive is data labeling?
    - How much data will be needed?

Why are accuracy requirements so important?
* direct driver of project cost.
* ML projects cost tend to scale super-linearly in the accuracy part

### What's still hard in ML?
* Pretty much anything that a normal person can do in 1 second can be done with AI
* ex.
    - recognizing content of image
    - understanding speech
    - translating speech
    - grasp objects
    - etc.
* counter ex
    - expressing feelings
    - understanding humor / sarcasm
    - in-hand manipulation
    - generalizing to new scenarios
    - etc.

Other stuff
* unsupervised learning
* reinforcement learning
* both show promise in limited domain where tons of data and compute are available

(true for anything except supervised learning)

### What's still hard in supervised learning?
* answering questions
* summarizing text
* predicting video
* building 3d models
* real world speech recognition
* resisting adversarial examples
* doing math
* solving word puzzles
* Bongard problems (visual analogy problems)
* etc.

### What types of problems are hard?
* output is complex
    - high dimentional, ambiguous output (3d reconstruction, video prediction, dialog systems)
* reliability is required
    - high precision tasks, robustness (out of distribution failing safely, robustness to adversarial attacks, high precision pose estimation)
* generalization required
    - out of distribution data
    - reasoning, planning, causality
    - ex. self-driving edge cases, self-driving control, small data

### Why is pose estimation interesting?
* impact 
    - goal is grasping, requires reliable pose estimation
    - traditional robot pipeline with heuristics is slow, brittle, great candidate for software 2.o
* feasability
    - data availability (easy data to collect, labeling could be difficult)
    - accuracy requirements (high accuracy required, low cost of failure...)
    - problem difficulty (small published results exists, but necessitate adaptation)

### Key points
1. To find high-impact ML problems, look for complex parts of pipeline and places where cheap prediction is valuable
2. cost of ML projects is promarily driven by data availability, but accuracy requirements are also important


How to estimate cost of DL projects ?
* depends on a lotof things

How to evaluate cost of wrong prediction?
* domain specific

## Archetypes

> The main category of ML projects, and the implications for project management

1. Improve an existing process
    - ex. 
        + improve code completion for an IDE
        + build a customized recommendation system
        + Build a better video game AI
    - key questions
        + do models truly improve performance?
        + does performance improvement generate business value?
        + do performance improvements lead to a data flywheel?
2. Augment a manual process
    - ex.
        + Turn sketches into slides
        + email auto-completion
        + help a radiologist do their job faster
    - key questions
        + how good does the system need to be to be useful?
        + how can you collect enough data to make it that good?
3. Automate a manual process
    - ex.
        + full self-driving
        + automated customer support
        + automated web design
    - key questions
        + what is an acceptable failure rate?
        + how can you guarantee that the model will be better than that?
        + how inexpensively can we label data from the system? (improve model based on mistakes it made, so we need to see)

### Data Flywheel
* More data -> More users -> Better model ->

Flyloop can break
* do we have a data loop? can we automatically collect data (and maybe even labels) from users?
* More data to better model (it's our job)
* better model to better user (do better predictions make product better?)

Different project archetyles life in different places in the impact vs feasability graph. 
* Lowest impact is improving an existing process
* higher impact is automating process fully, but less feasible. Make it better with great product design.

Product design can reduce need for accuracy whe we try to augment a manual process

Make process more feasible by adding humans in the loop or limit scope.

## Metrics

> How to pick a single number to optimize

Key points
1. Real world is messy, we care about a lot of things
2. However, ML systems work best when optimizing a single number
3. As a result, we need to pick a formula for combining metrics
4. This formula can and will change

### Review of accuracy, precision, recall

* accuracy = correct / total
* precision = TP / (TP+FP)
* recall = TP / (TP/TN)

Which model is based using these metrics?

* We can average simply, or weighted average, or other (e.g. mAP, )
    - best model will change based on which metric we use

Thresholding metrics:
* choose which metric to threshold
    - domain judgement (which metric can you engineer around)
    - which metric are least sensitive to model choice
    - which metric are closest to desirable value
* Choosing threshold values
    - domain judgement
    - how well does the baseline do?
    - how important is the metric right now?

Example : metric for pose estimation
* Enumerate requirements
    - downstream goal is real-time robotic grasping
    - position error < 1cm
    - angular error < 5*
    - must run in 100ms to work real time
* How are we doing right now?
    - position error between 0.75 and 1.25cm
    - angular error around 60*
    - inference time approx 300ms
* compare current performance to requirements
    - prioritize angular error
    - threshold values that are close to good (1cm position)
    - ignore runtime for now
* revisit metrics as numbers improve


## Baselines

> How to know if your model is performing well?

Key points
1. Baselines give a lower bound on expected model performance
2. The tighter the lower bound, the more useful the baseline (e.g. published results, carefully tuned pipelines, human baselines)

### Why are baselines important?
* same model, different baselines might mean different next steps

### Where to look for baselines?

External baselines
* business / engineering requirements
* published results (make sure it's fair!)

Internal baselines
* Scripted baselines (e.g. opencv, rule-based)
* simple ML baselines (e.g. bow, linear regression)
* human performance

### How to create good human baselines?
* random people (low quality baseline, easy to collect)
* ensemble of random people (better)
* domain experts (more costly)
* deep domain of experts
* mixture of experts (!!!)

You should go with the higher quality that can be augmented. We should also make them focus on the hardest cases

Where should we skip if we are short in time (tight deadline)
* mistake to skip the baseline, because it makes debugging process more efficient
* but we can skip out on how long we create that baseline
