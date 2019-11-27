---
title: "Machine Learning Project: Don't Overfit! II"
date: 2019-11-18
tags: [machine learning]
header:
    image: "/images/dont-overfit/overfit3.jpg"
excerpt: "A decision tree was found to give good predictive results, with an accuracy score of 77.5% on the test.csv dataset."
---

## Introduction
The goal of this [Kaggle competition](https://www.kaggle.com/c/dont-overfit-ii/overview) was to predict the binary classification of 19,750 items given data for 250 items.  Data for 300 features are provided. A conda environment for data science was created, and all of the programming was implemented in a Jupyter notebook.  All of the associated files have been placed in my [project repository](https://github.com/buckeye17/titanic) on Github, and the README.md provides instructions for reproducing my results.

## Findings
Fourteen models were generated in this analysis, six of which were ensembles based on eight base models.  The ensemble techniques provided only a small improvement on the best base models. Models were evaluated using the metric ROC AUC because this is the metric used to score submissions.  Most of the models have their ROC curve plotted below.  ROC is a curve which represents how often false positives (X axis) and true positives (Y axis) would occur as the threshold for classification is altered.  Hence it can rank various models independent of threshold.  ROC AUC is the measure of Area Under Curve of the ROC curve.  The greater AUC is, the better it will distinguish beteen false positives and true positives.
<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/ROC-curves.png" alt="Plot of ROC Curves for All Models">

The following table gives the 5-fold mean ROC AUC score for every model created in this analysis.  Most of these models were hypertuned either using using GridSearchCV.  The most surprising result in this analysis was that Logistic Regression nearly matches the best ensemble, despite the fact that it wasn't hypertuned.

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/Model-Scores-Tbl2.png" alt="Model Scores Table">


## Methodology Highlights
* Data Cleaning
    * No missing values exist in the datasets provided.  All feature data is continuous and is already standardized.  Hence no cleaning was required.
* Exploratory Data Analysis
    * First, the data was explored with the aim of feature reduction.
* Modeling
    * A decision tree classifier was applied, manually adjusting the max_depth parameter
    * The default measure of impurity (Gini) gave unbalanced leaf nodes, therefore entropy was used instead
    * 5 fold cross validation gave 81.6% mean accuracy on the train.csv dataset
    * The graphviv library was used to plot the decision tree
    * A random forest classifier consisting of decision trees was applied next, using random search to conduct hyperparameter tuning
    * 5 fold cross validation gave 83.2% mean accuracy on the train.csv dataset
    * Both the decision tree and random forest algorithms used the ROC AUC metric to guide decisions to avoid models that were overly biased by bias in the the train.csv survival rate

    ## Utilized Libraries
