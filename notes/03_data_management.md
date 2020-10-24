# 03 - Data Management

> Data Management concerns its storage, access, processing, versioning, and labeling.

## Overview

> One of the biggest failures I see in junior ML/CV engineers is a complete lack of interest in building data sets. While it is boring grunt work I think there is so much to be learned in putting together a dataset. It is like half the problem. - Katherine Scott

Complexity is mainly in the data, not in modeling.

### In this module :

1. Sources
    - Where training data comes from
2. Labeling
    - How to label proprietary data at scale
3. Storage
    - Data (images, sound files, etc) and metadata (labels, user activity) should be stored appropriately
4. Versioning
    - Dataset is updated through user activity or additional labeling, affects model
5. Processing
    - Aggregating and converting raw data and metadata


## Sources

* Most DL applications require lots of labeled data
    - Exceptions : RL, GANs, "semi-supervised" learning
* Publicly available datasets = No competitive advantage
    - But it can serve as a starting point

Usually : Spend $$$ and time to label data

Data flywheel : Enables rapid improvement with user labels

### Semi-supervised learning
* using parts of data to label other parts
    - ex. NLP
        + predict any part of the input for any other part
        + predict the *future* from the *past*
        + predict the future from the recent past
        + predict the future from the present
        + predict the top from the bottom
        + pretend there is a part of the input you don't know and predict that

### Image data augmentation
* Must do for training vision models
* Keras, fast.ai provides functions to do that
* hook into your Dataset class to do CPU workers in parallel to GPU training

### Other data augmentation
* Tabular
    - delete some cells to simulate missing data
* Text
    - No well established techniques, but can replace words with synonyms, replace order of things, backtranslation
* speech/video
    - change speed, insert pauses, ...

### Synthetic Data

* Underrated idea almost always worth starting with
    - ex. dropbox
    - this can get really deep! (e.g. Blender for CV applications)


## Labeling

1. User Interfaces
2. Sources of labor
3. Service companies

Standard set of features
* Bounding boxes, segmentations, keypoints, cuboids
* Set of applicable classes

Training annotators is crucial
* We want to have clear instructions on how to create e.g. bounding boxes
* Quality assurance is key (monitoring)

Sources of labor
* Hire own annotators, promote best ones to quality control
    - pros : secure, fast (once hired), less quality control needed
    - cons : expensive, slow to scale, admin overhead
* crowdsource (mechanical turk)
    - pros : cheaper, more scalable
    - cons : not secure, significant quality control effort required
* or full-service data labeling companies

### Service Companies
* Data labeling requires separate software stack, temporary labor and quality assurance needs. Makes sense to outsource.

* Dedicate several days to selecting the best one for you
    - label gold standard yourself
    - sales calls with several contenders, ask for work sample on same data
    - ensure agreement with gold standard

FigureEight is the original AI data labeling company, used by many. Other ones are Scale.ai, and ton of others (labelbox, supervisely, ...)

### Software

* Full-service data labeling is always pricy
* but some companies offer their software without labor
    - Prodigy (annotation tool for NLP and vision tasks, nice interface, ...)
    - Rasa for conversational agents

Conclusions
* outsource to full service company if you can afford it
* if not, then at least use existing software
* hiring part-time makes more sense than trying to make crowdsourcing work

## Storage

1. Building Blocks
    - Filesystem
    - Object Storage
    - Database
    - "Data Lake"
2. What goes where
3. Where to learn more

### Filesystem
* Foundational layer of storage
* Fundamental unit is a "file, which can be text or binary, is not versioned, and is easily overwritten.
* Can be as simple as a locally mounted disk containing all the files you need
* Can be networked (e.g. NFS)
    - Accessible over network by multiple machines
* Can be distributed (e.g. HDFS):
    - Stored and accessed over multiple machines
* Be mindful of access patterns! Can be fast, but not parallel.

### Object Storage
* An API over the filesystem. GET, PUT, DELETE files to a service, without worrying where they actually are stored
* Fundamental unit is an "object". Usually binary, but can also be text, image, sound file
* Versioning, redundancy can be build into the system
* Parallel, but not fast
* Amazon S3 is the canonical example
* For on-prem setup, Ceph is a good solution.

### Database
* Persistent, fast, scalable storage and retrieval of structured data.
* Mental model : everything is in RAM, but the DB software ensures that everything is logged to disk and never lost
* Fundamental unit is a "row": unique id, references other rows, values in columns
* Not for binary data, store references instead
* Usually for data that will be accessed again (not logs)
* Postgres is the right choice >90% of the time. Best-in-class SQL, great support for unstructured JSON, actively developed.

#### SQL
* SQL is the right interface for structured data
* If you avoid using it, you will eventually reinvent a slow and crappy subset of it

### "Data Lake"
* Unstructured aggregation of data from multiple sources (e.g. databases, logs, expensive data transformations)
* "Schema on read" : dump everything in, then transform for specific needs later
* Amazon Redshift is the go-to for this

### What goes where?
* Binary data (images, sound files, compressed texts) is stored as objects
* Metadata (labels, user activity) is stored in database
* If need features which are not obtainable from database (logs), set up data lake and a process to aggregate needed data
* At training time, copy the data that is needed onto filesystem (local or network). We want to minimize the distance between data and GPU!

#### Where to learn mode
* Designing Data-Intensive Applications (Martin Kleppmann) is a good reference)


## Versioning

4 levels of versioning
1. Unversioned
2. Versioned via snapshot at training time
3. Versioned as a mix of assets and code
4. Specialized data versioning solution

### Level 0 -> Unversioned

* Data lives are on filesystem/S3 and database
* Problem : deployments must be versioned, deployed machine learning models are part code, part data. If data is not versioned, deployed models are not versioned
* problem you will face : inability to get back to a previous level of performance

### Level 1 -> Versioned via snapshot
* Data is versioned by storing a snapshot of everything at training time
* This allows you to version deployed models, and to get back to past performance, but is super hacky
* Would be far better to be able to version data just as easily as code

### Level 2 -> Assets + Code
* Data is versioned as a mix of assets and code
* Heavy files stored in S3, with unique ids, Training data is stored as JSON or similar, referring to these ids and include relevant metadata (labels, user activity, etc.)
* JSON files can be big, but using git-lfs lets us store them just as easily as code
    - can improve further with "lazydata" : only sincing files that are needed
* The git signature + of the raw data file defines the version of the dataset
    - Often helpful to add timestamp

### Level 3 -> Specialised
* Specialized solutions for versioning data
* Avoid these until you can fully explain how they will improve the project
* Leaning solutions
    - DVC (open source, versions data and transformations)
    - Pachyderm
    - Dolt (simple solution for versioned databases that speaks SQL). See it as git for databases


## Processing

Motivational Example
* We have to train a photo popularity predictor every night
* For each photo, training data must include
    - metadata such as posting time, title, location (in db)
    - some features of the user, such as how many times they logged in today (need to compute from logs, ex)
    - Outputs of photo classifiers (content, style) (need to run classifiers)

Task dependencies may exist for the whole pipeline

* Some tasks can't be started until other tasks are finished
* Fini a task should *kick off* its dependencies


What can we do?
* Run everything from scratch. If things exist, no need to recompute
    - Using makefiles
    - limitations :
        + what if recomputation needs to depend on content, not on date?
        + What if dependencies are not files, but disparate programs and databases?
        + What if the work needs to be spread over multiple machines?
        + What if there are 100s of "Makefiles" all executing at the same time, with shared dependencies?

Airflow
* way to do data workflows
* In python
* you build a DAG to define dependencies
* Airflow does distributed work by queuing stuff, managing workers that pull from it, restarting jobs if they fail


Try to keep things simple until they happen
* Don't overengineer
* e.g. unix : powerful parallelism, streaming, highly optimize

