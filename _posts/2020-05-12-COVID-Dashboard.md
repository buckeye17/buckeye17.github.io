---
title: "Full Stack Development: COVID Web App"
date: 2020-05-12
tags: [dashboard, web development]
header:
    image: "/images/covid-dashboard/Heatmap_Banner_3x1.jpg"
excerpt: "The goal of this project was to deploy a dashboard which visualizes Johns Hopkins data for worldwide COVID-19 infections, providing a lot of a customization for how the user wants to view the data."
---

## Environment
Python 3.6 with: Dash, Plotly, Dash Bootstrap Components, Pandas, Matplotlib, Numpy, Datetime, JSON, os

A conda environment for the web app was created, and all of the programming was implemented in python scripts.  All of the associated files have been placed in my [project repository](https://github.com/buckeye17/sleepwithdash) on Github, and the `README.md` file provides instructions for how to use the scripts and deploy the web app to Heroku or locally on a Windows machine.

## Introduction
Having often used Johns Hopkins' [COVID dashboard](https://github.com/CSSEGISandData/COVID-19/tree/master/csse_covid_19_data/csse_covid_19_daily_reports), I was impressed that it gave access to COVID-19 data worldwide with a simple interface.  But it left me wanting in a few respects:
1. COVID-related history curves for various regions and for various case counts (like confirmed or deaths) could not be plotted together.
2. The bubble map makes it difficult to see and compare plotted values.  A heat map would be better suited for this purpose.
3. Per Capita values aren't available.

The goal of my dashboard was to provide a very flexible interface so that users can alter the plotted values as they desire.  In addition, I investigated whether simple projections could be made from the months of data.  But after completing a few models, I felt it would be irresponsible to make novice projections.  Instead, I decided to make sandboxes where a basic epidemiology model could be explored. A screenshot of the finished [dashboard](https://sleepwithdash.herokuapp.com/) is provided below.

[![]({{ site.url }}{{ site.baseurl }}/images/covid-dashboard/screenshot.png)](https://sleepwithdash.herokuapp.com/)

## Discussion
### Data
There are three kinds of data used in this app:
1. Johns Hopkins' COVID-19 data
2. Population data to enable per capita calculations
3. Geo JSON data to define the boundaries of geographical regions used in the heat maps

Links to all of these data sources have been provided in the "Links & References" tab within the app.

Most of the data wrangling effort was required to clean the raw data from Johns Hopkins.  The COVID data utilized in this app is made taken from csv files created for each day since January 22, 2020.  These files were read into Pandas dataframes, then appended together into a single dataframe.  Many inconsistencies in the data had to be resolved, which include:
* Names, such as "Bahammas", "The Bahammas" and "Bahamas, The"
* US data was initially reported by city, but beginning on March 21 it has been reported by county.  This was resolved by subsuming city data into the state level aggregate.
* Cruise ships and Wuhan evacuees were defined as though they were states/provinces.  These were instead subsumed at the national level aggregate. 
* Small oversees territories were mentioned as states, such as French Guiana for France.  These territories were also subsumed at the national level aggregate.

After those types of inconsistencies were resolved, there were duplicate rows such that for a given region and date, there were multiple values in the dataframe.  These duplicate rows were summed into single rows.

Another form of wrangling was to undertake all calculations from basic values.  Since updating the figures in itself is a slow/expensive process, deriving all necessary values ahead of time will mitigate the app's slow performance.  The calculations include deriving active cases (= confirmed - recovered - deaths), new cases for each date based on cumulative case counts for each date, per capita values and new cases for each date per capita.  Furthermore, where counties, states or provinces were defined, the rows were also aggregated into new rows representing higher level values (county rows into state rows, state rows into country rows).

Lastly, the dataframe variables were modified from Objects and float64 data types to smaller types, such as categorical variables, uint16 & float16.  This reduces the resulting dataframe pickle file size from 250MB to 27MB.  Some workarounds were needed, such as to avoid exceeding precision limits by multiplying values for new cases per date per capita by 1 million.  Another workaround needed was to correct rows where active cases were calculated to be negative because (recovered + deaths) > confirmed.  Without setting the minimum to zero, the unsigned integer would otherwise rollover into an enormous number.

The above data wrangling is accomplished entirely in a Jupyter notebook [<-- INSERT LINK HERE] to make data interrogation easy.  Each time the notebook runs, it automatically downloads all of the available Johns Hopkins csv files from Github and creates a single, clean dataframe that is saved as a pickle file.

The data wrangling tasks were to find population data sources (i.e. the US Census Bureau or Wikipedia) and Geo JSON files.  With each of the data sources (11 in total), effort was required to adjust either these data sources or Johns Hopkins data so that each of the regions could be matched across the datasets.  Population data wrangling was in this notebook [<-- INSERT LINK HERE].  Note that the population dataframes produced by this notebook are inputs into the Johns Hopkins notebook described above.  Most Geo JSON data wrangling was done on the Johns Hopkins region names as opposed to modifying the JSON files, except for the Chinese provinces files.  In this case, the province names were in Chinese characters, so the JSON file was modified.

### Modeling

### Dashboard Development
Ultimately, ~3,400 lines of Python were written (including comments and whitespace) in the full pipeline to produce this dashboard.  It was developed using the packages [plotly\|Dash](https://plot.ly/dash/) and [Dash Bootstrap Components](https://dash-bootstrap-components.opensource.faculty.ai/).  Dash enables developers to deploy [plotly](https://plot.ly/python/) interactive figures to a server as a Flask app.  Dash Bootstrap Components extends Dash's html capabilities with the popular html [Bootstrap](https://getbootstrap.com/) framework, which is designed to, "build responsive, mobile-first projects on the web."  Dash Bootstrap Components also supports 20 [Bootswatch](https://bootswatch.com/) CSS themes to produce different website styles.

### Dashboard Deployment
Lastly, the dashboard was deployed on the [Heroku platform](https://www.heroku.com/), which provides hosting services spanning from free personal accounts to enterprise accounts.  Deployment is accomplished by a command line tool which enables Git repositories to be pushed to Heroku.  A custom environment is then built and the web service is launched.  All of the tools above enabled a public web-based dashboard to be deployed using nothing but Python and git.

Based on my experience deploying a Heroku app previously, I knew that the app's memory would be ephemeral, meaning that once it is deployed, the data cannot be updated in any permanent manner.  I had hoped to avoid this by storing my daily-updated Pandas pickle files on my Google Drive, but this was unsuccessful because I could not complete the authentication with Google in the Heroku headless environment.  For, I will re-deploy my app daily to keep the data up to date.

## Challenges
The following list is meant to highlight the various challenges I encountered in this project as well as to share some of my solutions.
1. Improving my web-scraping code so that data which had already been downloaded wouldn't be downloaded again.  Without this, each update would take more than 10 minutes to scrape all of the Garmin and sunset/sunrise data.
2. Extensive date & time manipulations were needed so that the proper date was referenced for each night of sleep and the time of day was properly represented based on timezone and daylight savings.  In conjunction with the datetime package, this effort demonstrated Panda's powerful date & time capabilities using the `.dt` accessor.
3. Merging old Microsoft CSV data with Garmin in a consistent manner.  In addition to addressing the same date & time concerns as with the Garmin data, this also required identifying and filtering out naps, since Microsoft captured these but Garmin did not.
4. Downloading sunrise and sunset times for each date in the dataset based on Indianapolis' latitude and longitude from the API at [https://sunrise-sunset.org/api](https://sunrise-sunset.org/api).  This required forming the proper html request and wrangling the json response with more date & time manipulations.
5. The desired interactive plots ultimately required ~650 lines of Python code.  While I had some previous experience with plotly in R, this was still a big undertaking.  This required understanding how subplots are combined, defining custom hover info for data points, defining shaded areas of a plot and transforming data so that smoothed data trends could be plotted.
6. The Dash html layout and callback structures ultimately also required ~650 lines of Python code.  These packages (Dash and Dash Bootstrap Components) were totally new to me. It was difficult to construe how callback functions should be arranged because an Dash app element can only be updated via a single callback function.  For example, this meant that all aspects of a plot had to be updated within a single callback, taking all possible triggers at once.
7. Incorporating the data sync capability into the dashbaord was desired because it would be burdensome to manually get new data and push a new version of the dashbard to Heroku.  Since the update process is slow, a simple log of the progress was desired.  This presented similar technical challenges as when updating a plot, since the log window couldn't be modified by multiple callbacks and it couldn't be modified in the middle of a callback.  Therefore, several html elements were introduced inside the window, one for each process step.  Also the update script was broken into step functions so that the completion of each step could update the log.  Lastly, in order to indicate the overall progress, a progress bar was added.  Since the bar cannot be modified by each step's callback, a polling callback was introduced, which polls the step elements every second to derive the overall progress.
8. Deploying this app to Heroku also entailed several challenges since I had never attempted a similar deployment before.  Firstly, many attempts to deploy the app produced failed builds.  Local development was done in conda on Windows, but the deployed build uses pip on Linux.  Several build packs were tried to utilize a conda environment definition, but this was unsuccessful because it produced environments (AKA slugs) that were ~1GB in size.  Heroku limits slug size to 500MB.  It turned out that the earlier failed builds using pip + `requirements.txt` was failing because the package versions from the local conda environment were incompatible in the Linux + pip system.  By deleting the version definition of all packages from `requirements.txt`, the app successfully built.

Other deployment issues included modifying the Python code in order run in a server environment.  This was discovered with the help of the plotly\|Dash community forum [here](https://community.plot.ly/t/deploying-dash-to-heroku/33022/12).  Also, getting Selenium to run on Heroku involved special build packs and a few additional modifications to my Python code.

## What Could Be Improved?
Selenium runs with rare issues in my local conda environment, but it is finicky when running on Heroku.  It frequently fails, with various causes.  The only reason Selenium is needed is to get a valid session header to append with the json data request.  Unfortunately, there doesn't seem to be a work around to this.

Heroku works by creating ephemeral copies of the deployed application for each user browsing session.  Since all of the data is stored within this environment, any updates to the data are lost once the user session ends.  To avoid loss of data, it should be stored in a separate cloud location that supports updating.

The app supports filtering "off days", which are meant to represent nights where I didn't have work on the following morning.  This includes weekends, holidays and PTO.  Without access to my previous employer's records, I don't have an easy method for getting all of my PTO dates.  This could be improved with research in my email and Facebook accounts.  For now, many actual PTO days are assumed to be working days.