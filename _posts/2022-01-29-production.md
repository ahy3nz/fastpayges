---
toc: true
layout: post
description: A primer for computational grad students
categories: [data science]
title: What does "deploying to production" mean?
---

This will be an attempt to reconcile a grad student's computational work experience with that of an industry professional.
Hopefully, this might serve as a primer for computational grad students transitioning to data science or engineering work, and maybe shed some perspective on what "deploying to production" might mean in a variety of setups.

Read the post for storytelling, jump to the end if you want the summary.


# The computational grad student experience

Let's say your research involves some sort of calculations - simulation, optimization, etc.
Not only are you writing your own code to run the calculations or analyze the calculations, you have to run them on some sort of non-local compute (probably some sort of high performance computing setup).
You probably depend on some additional codes that you do not write yourself.
You generally choose/specify the systems of interest you will be calculating.

## Production runs and deploying to production

From a molecular simulation background, there is a specific term "production run" which describes a specific subset of your calculations - those that are actually being used for downstream consumption (read: the part you're actually analyzing).
The parts of the simulation that aren't "production runs", while useful, basically get discarded by anyone else unless you're doing some debugging.

For a lot of other situations, having code or (machine learning) models deployed to production means they're doing meaningful work. 
Something (or someone) is using your code or relying on your ML model to make predictions.

So the important thing here is that whatever you produced, it gets used by someone (or something) else. 
If code is written in the middle of a forest and no one is there to use it, is the code in production? No

## Writing/updating software you use

Hopefully any code you/your lab uses will be committed to some sort of version control like github.
Maybe you also use some sort of unit testing to validate logical correctness (the code works as you expect).
The next step would be uploading it to some sort of public repository of software, like pypi or conda.
You also make sure the code compiles and runs on different architectures (windows, linux, etc.) or software versions (python 3.8, 3.9, etc.)

In some cases, just the success of uploading these code binaries to a public repository might be considered having deployed to production.
At this point, others can readily use and abuse your code.

## Getting software to run on different computing environments

You might have some calculations running on non-local compute (HPC or cloud).
Not only did you just update a bug in the code, but you want to make sure all your current, ongoing operations are using this code.

Sure you wrote v2.0, but you actually need to start using it for calculations or analysis.

If it's some sort of analysis code, it might be as simple as running a `conda update` on your python environment.
If it's some sort of compiled code, there might additional work to get the software updated and compiled on your computing environment, including any dependencies.

Regardless, in this situation, deployed to production means that you updated your code and the resultant data you're generating is based on this newer code.

### Caveat

Because you updated your code from v1.0 to v2.0, you may need to reconcile the resultant change in your data generated from v1.0 versus v2.0.
Does this affect any downstream calculations if the schema or data distributions have changed?
What downstream consumers are affected by this update?

To be honest, most grad students don't worry about this because they are the only consumers of their own data.
If you updated your code, you can simply re-run all of your old calculations.
In more complex processes where you aren't in control of everything, there will likely be a blast radius from your code/model updates.

## Updating any automated reporting

This part is a little separate from the code updating paradigm.

When you are generating data, you want to summarize the copious results as they roll in.
In this case, you might be deploying a dashboard (or other reporting tool) into production.


# The industry data experience

Let's say you are some data analyst/engineer/scientist at a chemistry company, maybe one that allows people to [submit designs for molecules](https://covid.postera.ai/covid).
Those molecules will then be subjected to some sort of calculations (retrosynthesis, structure optimization, docking, free energy etc).
Maybe your company is fancy and wants a machine learning model that runs inference to predict its binding affinity to your target of interest.


## Reconciling the different levels of computation

Your machine learning model can return binding affinities in milliseconds, but the free energy calculation will take hours.
So if your service is the near-real time binding affinity predictions (from the ML model), your "absolute truth" verification comes from the free energy calculation, or maybe some bench science experiments, which then serve to update the model.

(I have no affiliation to postera and have no insider information, this is purely a made-up scenario)

## Writing/updating any software you use

Let's say you're part of the team maintaining your company's free energy code (setting up free energy calcs or the engine that does the free energy simulations).
Your team might make an update to your free energy code to be more compatible with certain chemistries.
You make sure the code passes unit tests, passes some sort of linting, passes on multiple hardware/computing environments, and gets the "LGTM" for you to merge it to your code's main branch.

To deploy into production, you might make a release on your git repo or you might upload binaries to an internal artifactory, pypi, conda, or something similar.
Regardless, if you're on this "backend" team, you deploy your code by releasing updates and notifying your downstream consumers.
These downstream consumers might be the more-applied scientists who take the code and study particular systems of interest.

And if any of the downstream consumers are having trouble with this update, you hopefully have someone "on-call" to respond.

At this point, submitted-compounds will be studied using free energy code v2.0, no longer v1.0

## Long, complex, and intricate pipelines

Let's say your company has a lot of business logic that starts when someone submits a compound:

- Log the compound into a database of submissions, with various metadata (time submitted, location tagging)

- If the compound is heavier than 500 Da, send the compound through a retrosynthesis step to help the bench scientists devise a synthesis reaction

- If you have a partnership with company A and a scientist from company A submits a compound, you additionally do some additional ADME evaluations against company A's internal database of compounds

- You do a literature search to find all DOIs that reference this molecule

- If a submitted compound contains heavy elements, you immediately discard the compound

- If a compound has isomers, run the workflow on all isomers

The list can go on. 
If someone at the company says "oh well sometimes we have X situations where we do A, and Y situations where we do B", then your business logic spawns a new head
The larger the company, the crazier this web can get.

These situations don't necessarily require any fancy calculations, just loads and loads of automated processing.
If this is your domain as an engineer, deploying code into production could involve: 

- Writing code that implements this logic

- Updating your code as upstream dependencies change

- Making sure your compute uses the new code

- If you have to introduce a new cloud resource, handling the setup and deployment of this resource into your stack

- If you have to introduce a new cloud resource, ensuring the resource has all the permissions to operate with the other resources in the pipeline


## Writing/updating the machine learning model you use

Let's say you're part of the team maintaining the binding affinity model.
In light of new binding data (from free energy calculations or bench studies), you decide to update your model.
This might manifest as just a model tuning job or maybe completely rearchitecting your model.
Regardless, you do your R&D to fix up the model.

Just like code, you release your model binary onto some online repository.
However, like code pipelines, you also need to think about the computational stack that's running the code or model (automated inference) 

One of the allures of employing machine learning models is the ability to make decisions/judgments in real-time without any handholding from a human.
(Digression, this is much easier in business settings than science settings because science is much harder to understand and harder to evaluate without humans).
One of the parts of "real-time" means automated compute that regularly takes input and spits out numbers, like on some sort of cloud resource.

In this case, deploying your machine learning model into production means re-deploying your stack as painlessly as possible for you and consumers.
Submitted compounds will be evaluated according to model v2.0, not v1.0.

Depending on your company's infrastructure, this might be as easy as pointing your service to use the newer model binary from your company's model registry.
In other situations, it might require managing an entire stack.
With managing these resources, this could cover things like microservices, hardware, operating system, software, dependencies, security, networking, traffic, load balancing, etc.

## What if we don't have a machine learning model, or are just testing out some machine learning?

This prototyping stage is extremely common since most management isn't super familiar with machine learning.
And even if they are, they're not sure how to apply it to whatever line of business they're in.
And this might be because management hasn't precisely identified the classification or regression problems in their problem space, especially those whose logic is simple enough for a model to emulate.

Or, management isn't comfortable with the kind of risk introduced with a machine learning model.
Fundamentally, the ML model helps automate predictions or decisions, taking that responsibility away from a human and into a microservice.
If the responsibility is taken away from a human, what happens if the model goes wrong? 
You might wake up the next day with an office space-esque debacle.
Do you lose money? Consumer trust? Break laws? Crash some internal infrastructure?
This is the stuff you don't learn in grad school.

Digression aside, many teams are experimenting with machine learning, seeing what they can do with a logistic regression or a random forest model.
At this stage, no one or no thing is consuming the outputs of this model.
The relevant "output" is the lessons learned from creating the model, or maybe interesting statistical trends derived from your model.
Maybe the output is just the demonstration that you _could_ build a model.

Building models such that you can glean insight from analyzing coefficients or derived statistical trends might not necessarily be considered deploying a model into production, but rather model prototyping, R&D, and analysis in the hopes that, eventually, the model will be used to do something in an automated fashion.


## Automated reporting

Maybe you're part of a business intelligence team trying to analyze patterns in the submitted compounds, like

- What time does our service exhibit peak load?

- Are there regions that submit certain moieites or compounds?

In these situations, the analysis is fairly manual, but you can build tools to facilitate this, like weekly formatted reports for leadership to discuss or a dashboard summarizing metrics.
Regardless, this work is designed to equip decisionmakers with the relevant information to make a decision.

In this case, you are deploying dashboards into production for someone to look at.
Devising new metrics means updating your dashboard application and then making sure your servers are showing the new dashboard.
The deployed dashboard will regularly (and automatically) update with new data as they roll in.

By looking at these dashboard metrics, product management teams find ideas for new tools or improvements for the models, spilling into the sections mentioned above.

# Summarizing deployment

I kind of ad libbed most of this post, but I realized some prevalent themes about deployment:

- Yes it covers making your code robust and usable by others, from code correctness to shareability to software dependency management

- But it also includes the relevant computational resources needed to run said code
    
    - For grad students, the only "microservice" is the computer cluster that runs your calculations. The ability to SSH into the cluster makes management much easier.
    
    - In industry, you have to worry about the zoo of microservices: setting up the resource, getting them to run your codes, and getting the services to work with each other. If you are utilizing a ton of different virtual computers, you can no longer rely on SSH-ing into every one of them.
    
- Machine learning engineering falls alongside other software to handle data processing, but with more behind-the-scenes R&D. The deployment considerations remain (usability, shareability, compatability with microservices, computational stack).

- Deploying something into production means building a code/tool/model and that gets consumed by other software/microservices/humans

    - If it's a library of tools, it might be uploaded to a repository for others to use (this is code someone else may end up using)
    
    - If it's code to accomplish a task, it gets deployed as a microservice to run somewhat autonomously (this is both code + resource stack)
    
    - If it's a machine learning model, it also gets deployed as a microservice to perform autonomous inferencing (with the occasional tuneup)
    
## Post script

This comes a couple years in industry, a couple years after a grad student decided to blindingly jump into the data science industry and hop around a few companies.
It'll be interesting to see how this perspective changes with a few more war stories.