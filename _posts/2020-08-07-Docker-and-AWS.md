---
title: "Dockerizing a Dash App, Deploying to AWS"
date: 2020-05-14
tags: [docker, AWS, web development]
header:
    image: "/images/docker-and-aws/Docker_AWS_Banner.png"
excerpt: "The goal of this project was to docker-ize the COVID-19 web app made with Dash.  This docker image was to be deployed on AWS."
---

# Introduction
The idea for this project arose after a Data Science interview I had in which the hiring manager mentioned that they like use Docker containers to provide reproducibility for data science projects.  I had heard about Docker but never used it.  So this project was meant to get my hands dirty with Docker.

In addition, this interview mentioned the use of cloud platforms like AWS (Amazon Web Services) and Azure.  Here again, I had knew a little bit about these platforms, but hadn't used them.  Once I have docker-ized my web app, deploying it on a cloud platform shouldn't be too hard because these containers are meant to run on any machine.  I chose AWS because it is currently the most popular platform.

An additional hope for this AWS deployment was bring down the cost of my web app.  I had found that my Heroku app sometimes ran into high RAM utilization, occasionally exceeding 1GB.  To avoid these unresponsive server states, I upgraded the dyno type to a professional version (offering 2GB of RAM) which cost $250/month.

# Docker & Windows 10


# Docker-izing Dash


# Deploying to AWS


# What Could be Improved?
