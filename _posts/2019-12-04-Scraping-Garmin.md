---
title: "Web Scraping: My Garmin Sleep Data"
date: 2019-12-05
tags: [scraping]
header:
    image: "/images/garmin-scraping/garmin-banner.jpg"
excerpt: "JSON sleep data files were obtained from May 2017 through the present.  These files provide the date and time for when I fell asleep and woke up each evening.  They also give the duration for how long I was in deep sleep, light sleep, awake (during a sleep event) and total sleep time."
---

## Environment
Python 3.7 with: Pandas, Numpy, Itertools, Datetime, JSON, RE, Pytz, Requests, Selenium, Seleniumwire, Brotli

A conda environment for data science was created, and all of the programming was implemented in python scripts.  All of the associated files have been placed in my [project repository](https://github.com/buckeye17/garmin-scraping) on Github, and the README.md file provides instructions for how to use the scripts.

## Overview
The goal of this project was to collect all of my personal sleep data from Garmin's website.  Garmin does not provide means of easily dowloading your data, so web scraping techniques were required to get the data.  My ultimate goal with this data is to create a web-based dashboard to visualize my sleep tendencies.  This will be the focus of my next project.

JSON files with sleep data were obtained from May 2017 through the present.  These files provide the date and time for when I fell asleep and woke up each evening.  They also give the duration for how long I was in deep sleep, light sleep, awake (during a sleep event) and total sleep time. 

## Discussion
Github repositories were searched for Python code that might already accomplish the stated goal.  Several solutions were found, but all of them were obsolete.  Attempts were made to repair broken code which had been active less than 2 years ago, but this effort failed.  Therefore a new script (`scrape_garmin_sleep.py`) was developed based on examples for scraping github and stackoverflow.

The greatest challenge in this project was successfully logging in to [connect.garmin.com](https://connect.garmin.com/modern/) with Python.  Navigating to the sign-in html form elements and properly submitting the user input proved difficult.  Ultimately, the Selenium package provided the solution to this problem.  

Next, code was written to scrape the webpges for each day's sleep data.  This was relatively easy to implement but was abandoned because Garmin only provides the duration of sleep for each day.  The most importnt data for my interest was the time of day when I fell asleep and woke up.

Based on previous github searches, other developers had showed that JSON files could be obtained from Garmin which were more descriptive than what their website provides via html.  Further searching revealed this Python [repository](https://github.com/kristjanr/my-quantified-sleep), which obtains JSON sleep data from Garmin.  This code was well documented and after making some improvements and corrections, it accomplished my stated goal.

The biggest improvement I made was to incorporate the Selenium and Seleniumwire packages.  Originally a user would need to login to [connect.garmin.com](https://connect.garmin.com/modern/) and then use the developer tools in their web browser to get html request headers and cookies, then hard code these into the Python scripts.  If the Garmin browser session expired, this process would need to be repeated.  Using the aforementioned packages, the user no longer has to use a browser.  Selenium will log the user in and Seleniumwire will get all of the headers.

Other significant alterations include:
* Removing Brotli decompression of the Garmin server response.  Apparently Garmin no longer uses compression, or perhaps the underlying packages have changed their behavior such that Brotli decompression is performed automatically once the Brotli package is installed.
* Breaking requests into 32-day periods (longer requests will cause an error)
* Writing the transformed data to a csv file using the Pandas package 

## What Could Be Improved?
Sleep data on or after July 1st, 2019 can be seen as a time series plot describing when the user was in light sleep, deep sleep or awake.  Scraping this time series data could provide additional insights.  Also, it would be intersting to obtain other, non-sleep data such as for heart rate during the day.  This could provide interesting correlations with sleep patterns.