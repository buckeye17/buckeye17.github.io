---
title: "Dashboard: Visualizing My Sleep Data"
date: 2020-01-05
tags: [dashboard, web development]
header:
    image: "/images/garmin-scraping/garmin-banner.jpg"
excerpt: "JSON sleep data files were obtained from May 2017 through the present.  These files provide the date and time for when I fell asleep and woke up each evening.  They also give the duration for how long I was in deep sleep, light sleep, awake (during a sleep event) and total sleep time."
---

## Environment
Python 3.7 with: Dash, Plotly, Dash Bootstrap Components, Pandas, Matplotlib, Numpy, Statsmodels, Itertools, Datetime, JSON, pytz, os, RE, Requests, Selenium, Seleniumwire, Brotli

A conda environment for the web app was created, and all of the programming was implemented in python scripts.  All of the associated files have been placed in my [project repository](https://github.com/buckeye17/sleepwithdash) on Github, and the `README.md` file provides instructions for how to use the scripts and deploy the web app to Heroku or locally on a Windows machine.

## Overview
A screenshot of the finished [dashboard](https://sleepwithdash.herokuapp.com/) is provided below.

[![]({{ site.url }}{{ site.baseurl }}/images/sleep-dashboard/screenshot.png)](https://sleepwithdash.herokuapp.com/)

The goal of this project was to deploy a dashboard which visualizes all of my sleep data from my smartwatches.  This includes data from my Microsoft Health band (2015 - 2017) and Garmin smartwatch (2017 - present).  The dashboard was also expected to get new data from Garmin automatically.  Lastly, this dashboard was to be deployed on a public server so my work could be shared and also so I could view my data in the future without running Python on my PC.  This project benefitted from my previous web scraping [project](https://buckeye17.github.io/Scraping-Garmin/).

The dashboard was developed using the packages [plotly\|Dash](https://plot.ly/dash/) and [Dash Bootstrap Components](https://dash-bootstrap-components.opensource.faculty.ai/).  Dash enables developers to deploy [plotly](https://plot.ly/python/) interactive figures to a server as a flask app.  Dash Bootstrap Components extends Dash's html capabilities with the very popular html [Bootstrap](https://getbootstrap.com/) framework, which is designed to, "build responsive, mobile-first projects on the web."  Dash Bootstrap Components also supports 20 [Bootswatch](https://bootswatch.com/) CSS themes.

Lastly, the dashboard was deployed on the [Heroku platform](https://www.heroku.com/).  It provides hosting services spanning from free personal accounts to enterprise accounts.  Deployment is accomplished by a command line tool which enables Git repositories to be pushed to Heroku.  A custom environment is then built and the web service is launched.  All of the tools above enabled a public web-based dashboard to be deployed using nothing but Python and git.

## Discussion
Ultimately, ~1,900 lines of Python were written to produce this dashboard.  The project entailed many challenges, including: 
1. Improving my web-scraping code so that data which had already been downloaded wouldn't be downloaded again.  Without this, each update would take more than 10 minutes to complete.
2. Doing many date & time manipulations so that the proper date was referenced for each night and the time of day was properly represented based on timezone and daylight savings.  In conjunction with the datetime package, this effort demonstrated Panda's powerful date & time capabilities using the `.dt` accessor.
3. Merging old Microsoft CSV data with Garmin in a consistent manner.  In addition to addressing the same date & time concerns with the Garmin data, this also required identifying and filtering out naps, since Microsoft captured these but Garmin did not.
4. Downloading sunrise and sunset times for each date in the dataset based on Indianapolis' latitude and longitude from the API at [https://sunrise-sunset.org/api](https://sunrise-sunset.org/api).  This required forming the proper html request and wrangling the json response with more date & time manipulations.
5. The desired interactive plots ultimately required ~650 lines of Python code.  While I had some previous experience with plotly in R, this was still a big undertaking.  This required understanding how subplots are combined, defining custom hover info for data points, defining shaded areas of a plot and transforming data so that smoothed data trends could be plotted.
6. The Dash html layout and callback structures ultimately also required ~650 lines of Python code.  These packages (Dash and Dash Bootstrap Components) were totally new to me. It was difficult to construe how callback functions should be arranged because an element can only be updated via a single callback function.  This meant that all aspects of a plot have to be updated within a single callback, taking all possible triggers at once.
7. Incorporating the data sync capability into the dashbaord was desired because it would be burdensome to manually get new data and push a new version of the dashbard to Heroku.  Since the update process is slow, a simple log of the progress was desired.  This presented similar technical challenges to updating the plot, since it couldn't be modified by multiple callbacks and it couldn't be modified in the middle of a callback.  Therefore, several html elements were introduced inside the window, one for each process step.  Also the update script was broken into step functions so that the completion of each step could update the log.  Lastly, in order to indicate the overall progress, a progress bar was added.  Since the bar cannot be modified by each step's callback, a polling callback was introduced, which polls the step elements every second to derive the overall progress.
8. Deploying this app to Heroku also entailed several challenges since I had never attempted a similar deployment before.  Firstly, many attempts to deploy resulted in failed attempts to build the app environment.  Local development was done in conda, but the deployed build uses pip in Linux.  Several build packs were tried to utilize a conda environment definition, but this ultimately failed because it produced environments (AKA slugs) that were ~1GB in size.  Heroku limits slug size to 500MB.  It turned out that using pip + `requirements.txt` was failing to build because the package versions from the local conda environment were incompatible in the Linux + pip system.  By deleting the version definition of all packages from `requirements.txt`, the app successfully built.  Also, the Python code needed to be modified in order run in a server environment.  This was discovered with the help of the plotly\|Dash community forum [here](https://community.plot.ly/t/deploying-dash-to-heroku/33022/12).  Lastly, getting selenium to run on Heroku involved special build packs and a few additional modifications to my Python code.

## What Could Be Improved?
Selenium runs with rare issues in my local conda environment, but it is finicky when running on Heroku.  It frequently fails, with various causes.  The only reason selenium is needed is to get a valid session header to append with the json data request.  Unfortunately, there doesn't seem to be a work around to this.

The overview tab in the app is slow to update when changes are made to the filters.  The code for these updates would benefit from optimization.

The app supports filtering "off days", which are meant to represent nights where I didn't have work on the following morning.  This includes weekends, holidays and PTO.  Without access to my previous employer's records, I don't have an easy method for getting all of my PTO dates.  This could be improved with research in my email and Facebook accounts.  For now, many actual PTO days are assumed to be working days.