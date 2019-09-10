---
layout: default
title: Ground Truth
---
# -- Ground Truth
Once we have the dataset, composed by 459 tweets, we had to label it in order to build the ground truth.
We have divided it in three parts and each one of them tried to classify the tweets using the label given by Google. The work was so time consuming, but necessary to test our hand-craft classifier.
This process has been assisted by a script written by us in Python, which takes the dataset in JSON as input and then tweet-by-tweet, with the assistance of another script that suggest some tags given some entities, we were able to classify them without any problems (if you are interested on it, check out [here](https://sergiopicca.github.io/wir_project/pages/ground)).
The output produced is a CSV file, which is better for readability.
