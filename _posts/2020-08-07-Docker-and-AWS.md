---
title: "Docker-izing a Dash App & Deploying to AWS"
date: 2020-05-14
tags: [docker, AWS, web development]
header:
    image: "/images/docker-and-aws/Docker_AWS_Banner.png"
excerpt: "The goal of this project was to docker-ize the COVID-19 web app made with Dash.  This docker image was to be deployed on AWS."
---
# Summary
Changes were made to docker-ize the original Dash web app and to deploy it to AWS Elastic Beanstalk.  This deployment reduced my operating cost from $250/month to $10/month, without any noticeable sacrifice in performance.  Lastly, this deployment fully automated the task of pushing new data to the web app.

The new app location can be found here: http://seecovid.eba-ishvxpm4.us-east-1.elasticbeanstalk.com/

The latest code is still available on Github: https://github.com/buckeye17/seecovid

# Introduction
The idea for this project arose after a Data Science interview I had in which the hiring manager mentioned that they like to use Docker containers to provide reproducibility in their projects.  I had heard about Docker but never used it.  So this project was meant to get my hands dirty with Docker.

In addition, this interview mentioned the use of cloud platforms like AWS (Amazon Web Services) and Azure.  Here again, I had knew a little bit about these platforms, but hadn't used them.  Having already planned to docker-ize my web app, I assumed deploying it on a cloud platform shouldn't be too hard because these containers are often used for this exact purpose.  I chose AWS because it is currently the most popular platform.

An additional hope for this AWS deployment was to bring down the cost of my web app.  After initial deployment on the Heroku platform, I had found that it sometimes ran into high RAM utilization, occasionally exceeding 1GB, leading to system errors.  To avoid unresponsive server states, I upgraded the dyno type to a professional version (offering 2GB of RAM) which cost $250/month.

# Docker & Windows 10
Docker's purpose is to container-ize applications for the OS they were developed on.  If the application was developed in a Windows environment, then the resulting Docker container will need to run on Windows machines.  Since most web and cloud development is hosted in Linux environments, Docker containers are usually based on a Linux kernel.  This presented a challenge for my project because all of my data science work is done on a Windows machine.

Fortunately this challenge was easily resolved by a recent feature release for Windows 10 called WSL2 (Windows Subsystem for Linux 2).  This feature enables full Linux operating systems to run on the Windows NT kernel.  To be clear, "full" means 100% compatibility.  The full bash terminal is available through a Windows terminal, and a remote desktop session can give graphical interaction with the Linux OS.

In addition, VS Code (my preferred IDE) and Docker for Windows both integrate well with WSL2.  In the end, all of my development files remained in their original directory on my C drive, but I was able to build and run Docker images for these files within Ubuntu!

# Docker-izing Dash
The task of getting Dash running within a Docker container was pretty easy.  A requirements.txt file from the required pip virtual environment was needed, as well as a simple Dockerfile file.  A few modifications to the Python application were also needed.  One of these was to change the host website IP address.  By default it ran on http://127.0.0.1 which is not accessible from outside a container, so it was moved to http://0.0.0.0.  Lastly, file paths were adapted to the container environment.

The bigger hassle in making the final Docker container was to reduce the size of the image.  Initial images were 1.5GB!  Many techniques were attempted to reduce this as much as possible, but most either failed to make a difference or failed to build.  The important successful changes were to use the python-slim base image and to prevent pip install from caching unnecessary files.  This brought the final image size to around 650MB, which includes 150MB of data.

# Deploying to AWS
Deploying a Docker web app turned out to be the most difficult task in this project.  There are plenty of tutorials for how to deploy a Docker container on AWS, but they usually failed to acknowledge the work needed to handle front end tasks like scaling, load balancing or domain registration.  Further difficulty came from addressing the abundance of service options, permissions and configuration settings.

The web app was successfully launched using EC2 as well as Fargate, but they could only be accessed via an IP address.  Setting up load balancing looked to be a difficult undertaking, so these were abandoned for Elastic Beanstalk, which automatically takes care of these front end tasks.  After researching EC2 instance types, the t3small type was chosen because it provides 2GB of RAM and processor speed is meant to be idle most of the time but burst when a user visits the site.  The great news is that this setup should cost about $10/month!  Heroku would charge for time while the app was dormant (not having any visitors for hours), while the t3 instance type is much more flexible about turning resources on/off as needed.

The original Heroku app had worked by embedding all of the app data in the deployed environment.  This meant that whenever new data was available, a new deployment of the app was required.  This crude deployment method was improved by adding some code to the Python application which will automatically retrieve the latest COVID-19 data from an S3 bucket whenever the app starts up.  In addition, the extract_transform notebook will automatically update the S3 bucket files when it finishes processing new data and will restart the Elastic Beanstalk web app instances, forcing them to download new copies of the data.  Updating the website can now be done with a single click on my laptop!

# What Could be Improved?
I'd like to re-incorporate the heat map animation feature, but something needs to done to reduce the amount of data involved in the animation to continue using the plotly interactive heatmap.  Another option would be to produce custom GIF files, but this would takeaway all interactivity with the plot.