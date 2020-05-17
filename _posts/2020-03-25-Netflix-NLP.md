---
title: "Tensorflow 2: NLP for Netflix Data"
date: 2020-03-25
tags: [deep learning, nlp]
header:
    image: "/images/tensorflow/Tensorflow_Banner.jpg"
excerpt: "The goal of this project was to get my hands dirty using Tensorflow 2 in an NLP project."
---

## Environment
Python 3.6 with: Tensorflow (v2.1), Sklearn, Pandas, Matplotlib, Numpy, Datetime

All development for this project was done with Google Colab notebooks.  I made this choice for a few reasons:
1. Colab notebooks provide hardware acceleration for free.  I don't have a PC that is supported with Tensorflow hardware acceleration, so Colab saved me significant time by keeping runtimes down.
2. Sharing my work through Colab is far easier than using Github.
3. It gave me experience with Colab, a technology I had only used for my Coursera classes on Tensorflow.

Links to my Colab notebooks will be provided below.

## Introduction
The goal of this project was to get my hand dirty using Tensorflow, having recently completed Coursera courses for Tensorflow.  I decided to find an NLP (natural language processing) project, ultimately selecting a Kaggle dataset to predict the genres of movies and TV shows based on their descriptions.  Since multiple genres are permitted for each item, this is a multi-label classification problem.  When it comes to modeling, I wanted to experiment with building my own complete neural network and compare it with popular pre-trained models like BERT.

## Data
This project uses a common Kaggle dataset: [Netflix Movies and TV Shows (Version 3)](https://www.kaggle.com/shivamb/netflix-shows).  The csv file contains data for movies and TV shows that were available on Netflix in 2019.  The fields utilized in this project are `listed_in` and `description`.  `listed_in` provides the genres which the item was categorized under.  There are 42 possible genres, and each item may have multiple genres assigned to it.  `description` is a free text field which describes the basic storyline of the of the item.  In total there are 6,234 items in the dataset.

This data was read into a Pandas dataframe and the two fields are isolated.  This data is reshaped into a new dataframe which resembles a one-hot-encoder array, where each column represents whether a genre was assigned to each item.  This dataframe also contains the description of each item.  Lastly, this dataframe is converted into a Tensorflow tensor and split into training and test tensors.

## Modeling
The first modeling approach to this project was to construct a neural network from scratch.  This begins with tokenizing the description text, which converts the text into a list of arbitrary integers (each integer representing a word).  The neural networks used here will not accommodate inputs of arbitrary length when batched together, so each tensor element is padded to make all elements equal in length to th longest element.



## Challenges


## What Could Be Improved?