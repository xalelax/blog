---
layout: post
title:  "Time Series Nested Cross-Validation with scikit-learn"
labels: [machine learning, statistics]
categories: Blog
---

In this short post, I will show how to perform nested cross-validation on time series data with the scikit-learn 
function [TimeSeriesSplit](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.TimeSeriesSplit.html);
this function by default just splits the data into time-ordered Train/Test sets, but we will see that it is easy to bring a Cross-Validation set into the picture. I will also show how this procedure interacts with the `cv` argument that many models in scikit-learn use to perform cross-validation. 

## Introduction

In applying machine learning to time series datasets, one needs to be especially careful to some of their peculiarities. In particular, the test set should be chosen "in the future" of the training set, otherwise some information might leak from the training set to the training set, and this is a huge issue (look-ahead bias). For the same reason, the CV set should be in the future of the training set too.

Thus, it seems reasonable to perform the train/CV/test split of the data while keeping the time order of the dataset (i.e., without shuffling the dataset); for instance, if one has a time series of daily measurements taken during 2018, the training set could start on 01/01 and end on 31/08, the CV set could be from 01/09 to 31/10, and the test set from 01/11 to 31/12.
This removes the above-mentioned problem but it is still a suboptimal choice: since the test and cv sets were chosen arbitrarily, they might not be representative of the whole dataset, thus possibly inducing bias on the model and on error estimates. 

A more robust solution is to perform an operation analogous to k-fold cross-validation, but in a time-ordered way. We can picture this operation as follows:

![Train/CV/Test](/assets/pics/nestedcv/traincv.svg){: .center-image}

By training and tuning the model on the Train/CV set of each fold, and averaging the errors on the Test sets, we can obtain an almost unbiased estimate of the error ([Varma and Simon 2006](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC1397873/)).

## Sample Implementation

Let's start by generating a DataFrame indexed by datetime objects, with some random features X and labels y:


```python
import pandas as pd
import numpy as np

nRows = 365

df = pd.DataFrame(np.random.randint(0,5,size=(nRows, 2)), 
                  columns = ["X","y"], 
                  index=pd.date_range("20180101", periods=nRows))

with pd.option_context('display.max_rows',8):
    print(df)
```

Output:

                X  y
    2018-01-01  0  4
    2018-01-02  1  4
    2018-01-03  0  2
    2018-01-04  0  4
    ...        .. ..
    2018-12-28  2  0
    2018-12-29  2  4
    2018-12-30  1  3
    2018-12-31  4  4
    
    [365 rows x 2 columns]
    

In order to perform the nested cross-validation described above with Sklearn, we just need to write:


```python
from sklearn.model_selection import TimeSeriesSplit

n_splits = 3 #Number of train/cv/test folds

trainTestSplit = TimeSeriesSplit(n_splits+1).split(df)
next(trainTestSplit) #Skip the first fold

for trainCvIndices, testIndices in trainTestSplit:
    # First, we split Train + CV and Test
    XTrainCv, yTrainCv = df.iloc[trainCvIndices,0], df.iloc[trainCvIndices,1]
    XTest, yTest       = df.iloc[testIndices,0]   , df.iloc[testIndices,1]
    
    # Then, we build a list of the form [ ( [...Train Indices...], [...CV Indices...]  )]
    testLength = len(XTest)
    trainCvSplit = [(list(range(trainCvIndices[0],trainCvIndices[-testLength])),
                     list(range(trainCvIndices[-testLength],trainCvIndices[-1]+1)))]
    
    # Printing date ranges
    print("Training interval: "        , XTrainCv.index[0].date(), "--", XTrainCv.index[-testLength-1].date(),
          ", Cv: "           , XTrainCv.index[-testLength].date(), "--", XTrainCv.index[-1].date(),
          ", Test: "                      , XTest.index[0].date(), "--", XTest.index[-1].date())
```

Output:

    Training interval:  2018-01-01 -- 2018-03-14 , Cv:  2018-03-15 -- 2018-05-26 , Test:  2018-05-27 -- 2018-08-07
    Training interval:  2018-01-01 -- 2018-05-26 , Cv:  2018-05-27 -- 2018-08-07 , Test:  2018-08-08 -- 2018-10-19
    Training interval:  2018-01-01 -- 2018-08-07 , Cv:  2018-08-08 -- 2018-10-19 , Test:  2018-10-20 -- 2018-12-31
    

Notice how we had to skip the first fold given by TimeSeriesSplit, and also how the test set for a fold becomes the CV set in the following one, just as we wanted.


The list we stored in `trainCvSplit` can be passed directly as the cv argument of many sklearn functions, which will use it to perform cross-validation; for instance, if inside the for loop we insert


```python
modelCV = LassoCV(cv = trainCvSplit).fit(XTrainCv,yTrainCv)
```

we will obtain an object which performs Lasso regression; upon calling the .fit() method several models will be trained on the test set we defined, and the one which performs best on the CV set we introduced will be selected.