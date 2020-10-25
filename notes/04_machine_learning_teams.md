# 04 - Machine Learning Teams

> Here we explore ML roles, types of organizations, best management practices, and hiring.

## Overview

> Why is running a Machine Learning team hard?

* Running any technical team is hard...
    - Hiring great people
    - Managing and developing those people
    - Managing your team's ourput and making sure your vectors are aligned
    - Making good long-term technical choices & managing technical debt
    - Managing expectations from leadership

* ... And ML adds complexity
    - ML talent is expensive and scarce
    - ML teams have a diverse set of roles
    - Projects have unclear timelines and high uncertainty
    - The field is moving fast and ML is the "high-interest credit card of technical debt"
    - Leadership often doesn't understand AI

Module overview
* Roles
    - ML-related roles and the skills they require
* Organisations
    - How ML teams are organized and how they fit into the broader organization
* Managing
    - How to manage a ML team
* Hiring
    - How to hire ML engineers. How to get hired.


## Roles

Common ML roles on a team
* ML product manager
* DevOps
* Data Engineer
* ML engineer
* ML researcher / ML scientist
* Data scientist

### Breakdown of job function by role

* ML product manager
    -  job fct : work with ML team, business, users, data owners to prioritize
    - Work product : design docs, wireframes, work plans
    - Using tools like Jira, etc
* DevOps Engineers
    - Deploy and monitor production systems
    - work product : deployed products
    - using tools like AWS
* Data engineers
    - build data pipelines, aggregation, storage, monitoring
    - Work product : distributed system
    - tools used : hadoop, kafka, airflow
* ML Engineer
    - Train and deploy prediction models
    - work product : prediction system running on real data
    - using tools like tensorflow, docker
* ML Researcher
    - Train prediction models (often forward looking or not production critical)
    - work product : prediction model and report describing it
    - using tools like tensorflow, pytorch, jupyter
* Data Scientist
    - Blanket term used to describe all of the above in some orgs, means answering business questions using analytics
    - work product : prediction model or report
    - tools : sql, excel, jupyter, pandas, sklearn, tensorflow

### What skills are needed for the role?

* ML PM : low ML skills, low engineering skills, high communication skills
* ML DevOps : high engineering skills, low ML skills, low communication
* data engineer is a bit less engineering, but a bit more ml
* ML engineer is way more ML, a bit less engineering
* data scientist need low engineering skills, medium ML skills. COmmunication is more important here. Wide range of backgrounds (bachelors in DS or PhD in science + training in DS...)
* ML researcher need a lot of ML skills, some engineering


## Team Structure

> How ML teams are organized and how they fit into the broader organization

Lessons learned
* No consensus yet on the right way to structure a ML team
* This lecture : taxonomy of best practices for different organizational maturity levels

### Archetypes

* Nascent / Ad-Hoc ML
    - What it looks like
        + No one is doing ML, or ML is done on an ad-hoc basis.
        + Little ML expertise in-house
    - Example organizations
        + most small-medium businesses
        + less technology-forward large companies (education, logistics, etc)
    - Advantages
        + Often low-hanging fruit for ML
    - Disadvantages
        + Little support for ML projects, difficult to hire and retain good talent

* ML R&D
    - What it looks like
        + ML efforts are centered in the R&D arm of the organization
        + Often hire researchers / PhDs & write papers
    - Example organisations
        + large oil & gas companies, manufacturing, telecom companies
    - Advantages
        + Often can hire experienced researchers
        + Can work on long-term business priorities & big wins
    - Disadvantages
        + Difficult to get data
        + Rarely translates into actual business value, so usually the amount of investment remains small

* ML embedded into business / product teams
    - What it looks like
        + Certain product teams of business units have ML experties alongside their software or analytics talent
        + ML reports up to the team's engineering lead or tech lead
    - Example organisations
        + software / technology companies
        + financial services companies
    - Advantages
        + ML improvements are likely to lead to business value
        + Tight feedback cycle between idea and product improvement
    - Disadvantages
        + Hard to hire and develop top talent
        + Access to resources (data and compute) can lag
        + ML project cycles conflict with engineering management
        + Long-term projects can be hard to justify

* Independend ML Function
    - What it looks like
        + ML division reporting to senior leadership (often CEO)
        + ML PMs work with MLRs, MLEs, and customers to build ML into products
        + Teams sometimes publish long-term research
    - Example organisations
        + Large financial services companies
    - Advantages
        + Talent diversity allows to hire and train top practitioners
        + Senior leaders can marshal data / compute resources
        + Can invest in tooling, practices, and culture around ML development
    - Disadvantages
        + Model handoffs to lines of business can be challenging. Users need to buy-in and be educated on model use
        + Feedback cycles can be slow

* ML-First Organization
    - What it looks like
        + CEO Buy-in
        + ML division working on challenging, long-term projects
        + ML expertise in every line of business focussing on quick wins and working with central ML division
    - Example organisations
        + Large tech companies
        + ML-focused startups
    - Advantages
        + Best data access (data thinking permeates the org)
        + Recruiting ML team works on hardest problems
        + Easiest deployment: product teams understand ML
    - Disadvantages
        + Hard to implement
        + Challenging & expensive to recruit enough people
        + Culturally difficult to embed ML thinking everywhere

### ML team structures - Design choices

Key questions
* Software engineering vs research
    - To what extent is the ML team responsible for building or integrating with software?
    - How important are software engineering skills on the team?
* Data ownership
    - How much control does the ML team have over data collection, warehousing, labeling and pipeline?
* Model ownership
    - Is the ML team responsible for deploying models into production?
    - Who maintains deployed models?


This level depends on the archetype of the business.

* ML R&D
    - software engineering vs research
        + research prioritized over SWE skills
        + Researcher-SWE collaboration lacking
    - Data ownership
        + ML team has no control over 
        + ML team typically will not have data engineering component
    - Model ownership
        + Models are rarely deployed into production

* Embedded ML
    - software engineering vs research
        + SWE skills prioritized over research skills
        + Often, all researchers need strong SWE as everyone expected to deploy
    - Data ownership
        + ML team generally does not own data production / mgmt
        + Work with data engineers to build pipelines
    - Model ownership
        + ML engineers own the models that they deploy into production


* ML Function
    - software engineering vs research
        + Each team has a strong mix of SWE and research skills
        + SWE and researchers work closely together within team
    - Data ownership
        + ML team has a voice in data governance discussions
        + ML team has strong internal data engineering functions
    - Model ownership
        + ML team hands off models to user, but is responsible for maintaing them

* ML First
    - software engineering vs research
        + Different teams are more or less research oriented
        + research teams collaborate closely with SWE teams
    - Data ownership
        + ML team often owns company-wide data infrastructure
    - Model ownership
        + ML teams hands of models to user, who operates and maintains them


## Managing Projects

It's hard to tell in advance how hard or easy something is.

* ML progress is nonlinear
    - Very common for projects to stall for weeks or longer
    - In early stages, difficult to plan project because unclear what will work
    - As a result, estimating project timelines is extremely difficult
    - i.e. production ML is still somewhere between *research* and *engineering*
* There are cultural gaps between research and engineering
    - Different values, backgrounds, goals, norms
    - In toxic cultures, the two sides often don't value one another
* Leaders often don't understand it

### How to mage ML teams better
* Do ML project planning probabilistically
    - from : one task after another and exact planning
    - to : we include uncertainty in the estimates

* Attempt a portfolio of approaches
    - If one of the researchy approaches is not working, you can try another one. No dealbreaker here.
* Measure progress based on inputs, not results
* Have researchers and engineers work together
* Get end-to-end pipelines together quickly to demonstrate quick wins
* Educate leadership on ML timeline uncertainty

### Resources for educating execs
* https://a16z.com/2016/06/10/ai-deep-learning-machines/
* Pieter Abbeel upcoming AI strategy class
    - https://emeritus-executive.berkeley.edu/artificial-intelligence

## Hiring

> How to source ML talent? How to interview ML candidates? How to find a job as a ML practitioner?

Outline
* the AI Talent Gap
    - How many people know how to build AI systems?
        - 5000 actively publishing research
        - 10000 estimated nb of people with right skillset
        - 22000 phd educated ai researchers
        - 90k upper bound on nb of ppl
        - 200k - 300k nb of ai researchers/practitioners (Tencent)
        - 3.6M nb of software developers in the US
        - 18.2M SWE in the world
    - Fierce competition for AI talent

* Sourcing
    - we can source ML PMs, DevOps, Data Engineers from  demonstrated interest in AI
    - ML Eng, ML Researchers is the difficult one
    - hire them the wrong way : too complicated perfect background
    - the right way
        + hire for software engineering skills, interest in ML and desire to learn. Train to do ML
        + Go more junior. Most undergrad CS students graduates have ML experience
        + Be more specific about what you need. Not every ML engineer needs to do DevOps
    - Hire ML Researchers
        + Look for quality of publications, not quantity(e.g. originality of ideas, quality of execution)
        + Look for researchers with an eye for working on important problems (not trendy problems...)
        + look for researchers with experience outside of academia
        + hire people from adjacent fields (physics, stats, math)
        + hire people outside of PhDs.
    - sources
        + linkedin, recruiters, on-campus recruiting
        + monitor arxiv and top conferences to flag first authors of cool papers
        + look for good reimplementations of papers you like
        + attend ML research conferences (NeurIPS, ICLR, ICML)

### How to attract people
* What practitioners want
    - working with cutting edge tools and techniques
    - build skills / knowledge in an exciting field
    - work with excellent people
    - work on interesting datasets
    - do work that matters
* How to make company stand out
    - Work on research oriented projects, Publicize them. Empower people to try new tools
    - Build team culture around learning (reading groups, learning day, professional development budget, conference budget)
    - Hire high profile people. Help best people build their profile through publishing blogs and papers
    - Sell uniqueness of your dataset in recruiting materials
    - sell the mission of company and potential impact of ML on that mission. Work on projects that have a tangible impact today

### Interviewing
* Hire for strengths
* meet a minimum bar everywhere else
* validate hypotheses on candidate's strenghts
    - researchers : make sure they can think creatively about new ML problems, probe how thoughtful they were about previous projects
    - engineers : ake sure they are great generalist SWEs
* minimum bar on weaker areas
    - researchers : test SWE knowledge and ability to write good code
    - SWEs: test ML knowledge

### ML Interviews
* Much less well-defined than software engineering interviews
* common type of assessment
    - culture fit and background
    - whiteboard coding (similar to SWE)
    - pair coding (similar to SWE)
    - Pair debugging (often ML-specific code)
    - math puzzles (e.g. with linear algebra)
    - take-home ML project
    - applied ML (e.g. explain how you'd solve this problem with ML)
    - previous ML project (e.g. probing on what you tried, why things did or did not work)
    - ml theory (bias/variance tradeoff, overfitting, underfitting, understanding of specific algorithms)


