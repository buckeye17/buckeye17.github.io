---
title: "Supervised ML: Don't Overfit! II"
date: 2019-11-18
tags: [supervised, classification]
header:
    image: "/images/dont-overfit/overfit3.jpg"
excerpt: "The most surprising result in this analysis was that Logistic Regression nearly matches the best ensemble, despite the fact that it wasn't hypertuned."
---

## Environment
Python 3.8 with: Sklearn, Matplotlib, Seaborn, Graphviz, Vecstack, Pandas, Numpy, Scipy, Random, os, itertools

## Introduction
The goal of this [Kaggle competition](https://www.kaggle.com/c/dont-overfit-ii/overview) was to predict the binary classification of 19,750 items given data for 250 items.  Data for 300 features were provided. A conda environment for data science was created, and all of the programming was implemented in a Jupyter notebook.  All of the associated files have been placed in my [project repository](https://github.com/buckeye17/dont-overfit-ii) on Github, and the README.md file provides instructions for reproducing my results.

## Findings
Fourteen models were generated in this analysis, six of which were ensembles based on eight base models.  The ensemble techniques provided only a small improvement on the best base models. Models were evaluated using the metric ROC AUC because this is the metric used to score submissions.  Most of the models have their ROC curve plotted below.  ROC is a curve which represents how often a model predicts false positives (X-axis) and true positives (Y-axis) as the threshold for classification is altered.  Hence it can rank various models independent of threshold.  ROC AUC is the measure of Area Under Curve of the ROC curve.  The greater AUC is, the better the model is at distinguishing beteen false positives and true positives.
<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/ROC-curves.png" alt="Plot of ROC Curves for All Models">

The following table gives the 5-fold mean ROC AUC score for every model created in this analysis.  Most of these models were hypertuned using GridSearchCV.  The most surprising result in this analysis was that Logistic Regression nearly matches the best ensemble, despite the fact that it wasn't hypertuned.

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/Model-Scores-Tbl3.png" alt="Model Scores Table">

## Methodology Highlights
### Data Cleaning
No missing values exist in the datasets provided.  All feature data is continuous and is already standardized.  Therefore no cleaning was required.

### Exploratory Data Analysis
First, the data was explored with the aim of feature reduction.  Principal Component Analysis (PCA) was used to discern some features were more important than others.  The plot below indicates that all features have equal importance because the plot forms a stright line.  If some features were more important, then the line would resemble a montonically decreasing function (concave down).

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/Feature-PCA.png" alt="Feature PCA Plot">

Kernel density plots were also overlayed for all 300 features.  The underlying feature data utilizes both training and test datasets.  In addition, the mean and standard deviation were derived from the entire feature population and the resulting normal distribution was plotted in black.  This shows that all features are approximately Gaussian.

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/Feature-Kernel-Densities.png" alt="Feature Kernel Densities Plot">

Lastly, feature correlations matrix was plotted as shown below.  Since no correlations were found between features, no features were eliminated in subsequent analysis.  Note, since PCA had indicated that all features were equally important, this correlation plot was expected to show all features as independent.

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/Feature-Correlations.png" alt="Feature Correlations Matrix Plot">

### Modeling
The following eight base models were created (bold font indicates the model was hypertuned): Logistic Regression, Support Vector Classification (SVC) with a linear kernel, SVC with a polynomial kernel, **SVC with a radial basis function kernel**, **K-Nearest Neighbors**, **Decision Tree**, **Stochastic Gradient Descent** and **Gaussian Naive Bayes**.  All models were cross validated with 5-folds and scored based on the 5-fold mean ROC AUC score.

Next, six ensemble models were constructed and all were hypertuned: Random Forest, ADA Boost based on Decision Tree models, Gradient Boost, Soft Voting based on 9 models, ADA Boost Stacked on top of 9 models, Random Forest stacked on top of 9 models, Random Forest stacked on the 3 most important models from the aforemntioned set of 9.  These models were also cross validated with 5-folds and scored based on the 5-fold mean ROC AUC score.

Prior to building ensembles relying extant base models, the base models were checked for whether their predicted probabilities were correlated.  The plot shows that most models do not have strong correlation (< 0.8).

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/Base-Model-Corr.png" alt="Base Model Correlations Matrix Plot">

Lastly, after the stacked models were completed, feature importance was plotted to determine which base models were most important.  The plots below give the base model importance when ADA Boost and Random Forest is stacked on top.  Since Random Forest depended heavily on three base models, a similar model was created which only relied on those three most important models.  This last model is named "Ensemble - Stack with RF 2" in the model score table above.  It slightly improved the performance of its predecessor.

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/ADA-Stack-Model-Importance.png" alt="Base Model Importance for Stack with ADA Boost">

<img src="{{ site.url }}{{ site.baseurl }}/images/dont-overfit/RF-Stack-Model-Importance.png" alt="Base Model Importance for Stack with ADA Boost">

## What Could Be Improved?
Reading some of the notebooks and discussion threads on the Kaggle competition website, feature importance was examined using partial dependence, the ELI5 package and the SHAP package. All of these are defined in this [Kaggle notebook](https://www.kaggle.com/mjbahmani/tutorial-on-ensemble-learning-don-t-overfit).

Also, alternate metrics could be added to the model evaluation.  The Wikipedia page for [ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) mentions that ROC AUC has a few shortcomings: it can be noisy, it ignores performance in specific threshold regions and only half of the measure is useful (<0.5 means it is worse than a random selection).  Alterntive metrics are: Informedness, Markedness, Matthews correlation coefficient, Certainty, Gini coefficient and Total Operating Characteristic.

And lastly, evaluating distances between observations in hyperspace can be a good method of identifying patterns in target behavior. While K nearest relies on euclidean distance, other measures of distance could be considered.