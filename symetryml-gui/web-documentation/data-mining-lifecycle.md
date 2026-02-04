# Data Mining Lifecycle

## Overview

Data mining is about explaining the past and predicting the future using data analysis. While the term is relatively new, the process of using empirical data to gain further insight has been around long enough to produce a well-defined methodology that guides the development of such projects.

## Problem Definition

Defining a data-mining problem requires a complete understanding of a project’s objectives and requirements from a domain perspective, and then converting that knowledge into a data-mining problem definition with a preliminary plan designed to achieve the objectives. Data-mining projects are often structured around the specific needs of an industry sector or even tailored and built for a single organization.

A successful data-mining project starts from a well-defined question or need. For example, a business might want to increase the click-through rate (CTR) for its upcoming email campaign, or an insurance company might want to classify customers into different risk categories. These examples are precise enough to be formulated as a data-mining model with a well-defined objective.

The next step is to gather historical data and adjust it to fit our problem.

## Data Preparation

Data preparation involves constructing a dataset from one or more data sources to be used for exploration and modeling. Best practice requires starting with an initial sample dataset to get familiar with the data, to discover first insights into the data, and to have a good understanding of any possible data quality issues.

Data preparation is often a time-consuming process and heavily prone to errors. The old saying "garbage in, garbage out" is particularly applicable to those data-mining projects in which data gathered may contain invalid, out-of-range, or missing values. Analyzing data that has not been carefully screened for such problems can produce highly misleading results.

## Data Exploration

Data exploration is the process of describing data using statistical and visualization techniques. The data is explored in order to bring important aspects of it into focus for further analysis.

SymetryML provides functionality for both _univariate_ analysis and _multivariate_ analysis. Univariate analysis focuses on one attribute at a time and is an easy way to verify that the data has been imported correctly and is in line with the previous assumptions made by the analyst. Multivariate analysis, on the other hand, allows for the study of many attributes and the relationships between them. For example:

* Are certain attributes correlated?
* Do values of one attribute show statistical dependence on the values of another?

These questions can generally be answered with the help of multivariate analysis and will guide the attribute choice for the final model.

## Modeling

Predictive modeling uses historical data to predict the probability of an unknown event. If the unknown outcome is categorical in nature (for example, click vs. non-click on a banner ad), you are solving a _classification_ problem and will need to build a classification model. In other words, you are trying to classify a particular case into the most appropriate group.

A _regression_ model, on the other hand, attempts to predict the value of a continuous variable. An example of this could be the average value of a shopping cart on your site or average house price in a particular area. In both cases, you use a set of input attributes (that is, features and predictors) to model a particular dependent, target, variable.

_Sequence_ models attempt to describe the behaviour of sequence of events and model data that is temporal in nature. What is the next most likely state? How likely that a particular sequence would occur based on our model? These are the typical questions that sequence models attempt to answer.

_Anomaly_ models are unsupervised learners, they do not require the target attribute to be present. Instead, they assess the similarity of a particular record to a set of records on which the models was trained on. They excel in situations where the number of labeled cases is very small, or cases where the true label for the record is unknown. Instances which are drastically different from the ones the model was build with, will yield a higher anomaly score than those which are aligned with what the model has seen before.

_Clustering_ models attempt to group your data such that the instances within a particular group are more similar to each other than to their counterparts in other groups. These models are unsupervised and can be built in an online fashion.

## Evaluating Models

Model evaluation is an integral part of the model-development process. It aids in finding the best model that represents the process and in presenting how well the chosen model will work in predicting future outcomes.

A common approach to performing model evaluation is to separate the historical data into two parts.

* The first part, often the larger of the two, will serve as the training set. This part will be used to build the model.
* The remaining part, typically referred to as the test set, will be left out until the model-building process is complete.

After the model is ready, its performance can be evaluated with this test set. The outcomes for the test set are known in advance. Evaluating the model simply involves keeping a tally of the number of times it predicts the outcomes of the test set correctly.

## Deployment

The concept of deployment in predictive data mining refers to the application of a model for prediction of new data. Building a model is generally not the end of the project. Even if the purpose of the model is to increase knowledge of the data, the knowledge gained will need to be organized and presented in a way that can be used.

Depending on the requirements, the deployment phase can be as simple as generating a report or as complex as implementing a repeatable data-mining process. In many cases, it will be developers, not data analysts, who perform the deployment steps. However, even if analysts perform the deployment effort, the developers must understand up front what actions will be needed to use the created models.
