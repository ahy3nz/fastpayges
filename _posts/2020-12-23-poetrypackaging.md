---
title: Poetry and Docker
toc: false
layout: post
categories: [data science]
---

What is [poetry](https://python-poetry.org/) and where does this fit in the python software/DS ecosystem? And some beginner forays into docker.

To skip the reading and jump to the code, go to [this repo](https://github.com/ahy3nz/poetry-demo)

# Personal opinions motivating this work

The data science world is large.
Data science is kind of like an intersection of statistics/math, subject matter, software engineering, algorithms, and all the collaboration/teamwork that comes with a job.
Starting out, you definitely cannot be expected to have a mastery over everything, but at least some minimal competencies and capacity to learn (this basically applies to all jobs).

I'm about 11 months into technically being labeled a "data scientist" and I've observed that each data scientist ends up cultivating their own sets of skills they find valuable and/or interesting -- generalist/specialist is basically skilling up however you want.
Seeing the work of other data scientists has built up a long laundry list of things I'd like to learn but don't have the business-hours to devote because of more-pressing project demands.
Among these is this concept of packaging and building "applications".
For a graduate student, your "application" might be a codebase and set of functions that others can reliably and consistently use in their own hacky codes.
For a software engineer, your "application" might be deployed onto some cloud server, where the code needs to be self-sufficient and robust, listening for input, processing this input, and pumping out some output without hands on a keyboard.
For a data scientist, you may eventually need to think about how an application gets deployed, from consistency of functions and numerical accuracy to considering the entire technical stack involved.
Day-to-day, I think consistency of functions and numerical accuracy are generally kept front-of-mind with unit tests or and mainly because you're always thinking of the mathematical model.

If you're a little more software-savvy, you'll think about your python environment, using conda or something to control your python software dependencies, your software build, and any compilation that has to happen.
Since I'm on socially-distanced, self-quarantined holiday, this is a great time to do some learning

# Poetry

## "Dependency" hell, an introduction

Most software depends on other software, and if the dependencies change some core functionality, then your own software may no longer function as intended.
To resolve this, you venture through ["dependency hell"](https://en.wikipedia.org/wiki/Dependency_hell) to figure out whose code broke your code, and how to fix this.

Data scientists like to use python virtual environments to ensure dependencies are compatible and runnable.
Some like to use pip and venv, which is fine for installing packages, but only recently will pip attempt to address dependency resolution.
Conda is also very popular for managing software packages, compiling software, and resolving software dependencies.

## What does poetry do?
A new contestant, [poetry](https://python-poetry.org/) finds itself in some python packaging and dependency conversations like "oh I've heard of poetry but never really tried it".
Poetry helps manage the python package dependencies for a given software, with a simple CLI to add and update new package dependencies.
Poetry generally involves the binary (available on conda and pip), but interacts with your package via two files, the `poetry.lock` and `pyproject.toml`.
If someone gives you those files, you should be able to build your own compatible python environment.
In tandem, the two specify the necessary dependncies for your project, with the former pinning dependencies and the latter floating dependencies.
Poetry also has some convenient functions for compiling source distributions and wheels so you can distribute this code on somewhere like pypi (but it doesn't look like there's any mention of conda recipes).

## What about docker?
Docker provides a lot of virtualization and environment control so you can put together an entire tech stack just for your application to run on a bare-bones, nothing-installed server somewhere.
This comes in the form of a dockerfile, which like a set of instructions on how to build your container.
For an early career data scientist, that's probably all you need to know.
Software engineers deal with this all the time, and data scientists eventually dip their toes here as a model/project comes to maturity.

You can learn a lot about dockerfiles by reading them and writing your own, so take a look at the repo linked at the beginning of this post.
In general, it kind of resembles a lot of shell commands.
Getting conda to work with docker comes with some sticking points:

- `conda` commands within each layer won't work unless you run the shell script that comes with conda, so you have to remember to run that script throughout the dockerfile
- Note the use of the `entrypoint.sh` file, which becomes the final script that is executed when you call `docker run`. Observe the necessary `chmod` to make it executable, and note the `conda.sh` command even inside the `entrypoint.sh` file if you want the container to run some code within a conda environment.
- `docker run -it poetry /bin/bash` if you want to open an interactive shell session to the container, running commands/codes inside the docker container like you would an SSH session.
- Technically, since you have absolute control over the image, you might not need the virtual environment for small python packages. As the packages get more complex and package builds become more complicated, it becomes easier to let conda handle the package management rather than try to correctly install everything in a dockerfile


If you envision running lots of python code or calculations on cloud servers, docker containers and python environments are the sorts of tech that make it happen (and if you and your proejct are up for it, container-orchestration and workflow tools) 

# Bare bones example
I've documented my experiences in this [sandbox for using docker and poetry](https://github.com/ahy3nz/poetry-demo).
There are a lot of tutorials on the internet, so I won't bother here.
But, for a data scientist versed in python environments, this repo showcases how to build your docker images for conda/poetry/python.
For a "real" industrial application, things will likely get messier as the environments and software stack get more complex, but this is a decent start for an amateur.

