---
title: "Web Scraping: My Garmin Sleep Data"
date: 2019-12-04
tags: [scraping]
header:
    image: "/images/garmin-scraping/garmin-banner.jpg"
excerpt: "JSON sleep data files were obtained from May 2017 through the present.  These files provide the date and time for when I fell asleep and woke up each evening.  They also give the duration for how long I was in deep sleep, light sleep, awake (during a sleep event) and total sleep time."
---

## Environment
Python 3.8 with: Pandas, Numpy, Itertools, Datetime, JSON, RE, Pytz, Requests, Selenium, Seleniumwire, Brotli

A conda environment for data science was created, and all of the programming was implemented in python scripts.  All of the associated files have been placed in my [project repository](https://github.com/buckeye17/garmin-sleep-scrape) on Github, and the README.md file provides instructions for how to use the scripts.

## Summary
The goal of this project was to collect all of my personal sleep data from Garmin's website.  Garmin does not provide means of easily dowloading many days of data, so web scraping techniques were required to get the data.

JSON sleep data files were obtained from May 2017 through the present.  These files provide the date and time for when I fell asleep and woke up each evening.  They also give the duration for how long I was in deep sleep, light sleep, awake (during a sleep event) and total sleep time. 

## Discussion
Github repositories were searched for Python code that might already accomplish the stated goal.  Several solutions were found, but all them had become obsolete.  Attempts were made to repair broken code which had been developed less than 2 years ago, but this effort failed.  Therefore a new script was developed based on examples for scraping github and stackoverflow.

The greatest challenge in this project was successfully logging in to [connect.garmin.com](https://connect.garmin.com/modern/).  Navigating to the sign-in html form elements and properly submitting the user input proved difficult.  Ultimately, the Selenium package provided the solution to this problem.  

Next, code was written to scrape the webpges for each day's sleep data.  This was relatively easy to implement but was abandoned because Garmin only provides the amount of sleep for each day.  The most importnt data for my interest was the time when I fell asleep and woke up.

Based on previous github searches, developers showed that JSON files could be obtained from Garmin, which seemed more descriptive than what their website provides via html.  Further searching revealed this [repository](https://github.com/kristjanr/my-quantified-sleep), which obtains JSON sleep data from Garmin.  This code was well documented and after making some improvements and corrections, it accomplished the stated goal.

The biggest improvement was incorporate the Selenium and Seleniumwire packages.  Originally a user would need to login to [connect.garmin.com](https://connect.garmin.com/modern/) and then use the developer mode in their web browser to get request headers and cookies, then hard code these into the python scripts.  If the Garmin browser sessions expired, this would need to be repeated.  Using the aforementioned packages, the user no longer has to use a browser.  Selenium will log the user in and Seleniumwire will get all of the headers.

Other significant alterations include:
* Removing Brotli decompression of the Garmin server response.  Apparently Garmin no longer uses compression, or it is possible that the underlying packages have changed their behavior such that Brotli decompression is performed automatically once the Brotli package is installed.
* Breaking requests into 32-day periods (longer requests will cause an error)
* Writing the transformed data to a csv file using the Pandas package 

## What Could Be Improved?
Beginning on July 1st, 2019 the Garmin sleep webpage provides a time series plot of when the user was in light sleep, deep sleep or awake.  Scraping this time series data could provide additional insights.  Also, it would be intersting to obtain other, non-sleep data such as heart rate data during the day correlates with sleep patterns.