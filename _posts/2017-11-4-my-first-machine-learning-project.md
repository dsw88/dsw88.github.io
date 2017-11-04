---
layout: post
title:  "My First Machine Learning Project"
date:   2017-11-04 08:00:00
categories: machinelearning scikitlearn python
---
# I'm trying machine learning, just like everyone else!
I've been dabbling in machine learning lately, trying to learn a bit on the side. I've just been accepted into a masters program for Computer Science at BYU, and I'm interested in possibly pursuing a course of study in machine learning.

Getting into the field has been pretty daunting at first. There's a lot of things to brush up on in math, and I apparently didn't take enough statistics classes in school, because I'm having to learn some new concepts as I go.

# My first project!
StackOverflow released their [2017 developer survey](https://insights.stackoverflow.com/survey/2017) results, and those results included salary data for many of the respondents. I thought it would be fun to try out some machine learning algorithms on that data set to estimate salaries. 

So I made [this project](https://github.com/dsw88/salary-estimator), which is a set of some Jupyter notebooks and the data sets. The "Data Preparation" notebook is where I selected the data and feature subsets to use, and the "Linear Regression" notebook is where I ran the Linear Regression algorithm on scikit-learn to train the model.

My first attempt to train the model failed miserably. I don't remember the score, but it was pretty bad...
 
I sat back and thought about why the model was failing. I realized that some of the features I had chosen didn't seem to actually have a very high correlation with salary, so they were probably messing things up. In addition, I had multiple features that didn't seem to have enough data for the many different categorical classes in them.

So I pared back my feature set, including restricting my model to just U.S. salaries (since it had the most respondents). After doing that, the model trained much better, giving me what seemed a more reasonable score. You can check out the [notebook on GitHub](https://github.com/dsw88/salary-estimator/blob/master/notebooks/Linear%20Regression.ipynb) to see the model training and the score given on the test set.

# I have a lot of work to do...
This project gave me some great initial experience using tools like Pandas and Scikit-Learn. I'm excited to keep doing more little learning projects here and there.

I'm a total newb, so I'm sure I've made tons of mistakes. But you have to start somewhere! I enjoyed this project, and am very interested to keep learning more about machine learning.