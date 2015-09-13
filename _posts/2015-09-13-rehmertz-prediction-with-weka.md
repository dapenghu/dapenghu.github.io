---
layout: post
title: "Rehmertz Prediction with Weka"
description: ""
category: Data Mining
tags: [Data Mining,Weka,EMBA]
---
{% include JB/setup %}

# Introduction
[TECHMARK-Maximizing-ROI](/assets/TECHMARK-Maximizing-ROI.pdf)

<!--
##Rehmertz 2  
![Rehmertz 2](/assets/Rehmertz-2.png "Rehmertz 2")

##Rehmertz 3
![Rehmertz 1-3](/assets/Rehmertz-1-3.png)
-->

# Weka Installation on Mac
Download [Weka disk image for OS X](http://prdownloads.sourceforge.net/weka/weka-3-6-13-oracle-jvm.dmg) and install it.

### Install libsvm
By default Weka doesn't has libsvm library. In order to use SVM classification algorithm, we have to install the libsvm jar package manually. 

Install libsvm.jar step by step:

- Download [libsvm](http://www.csie.ntu.edu.tw/~cjlin/cgi-bin/libsvm.cgi?+http://www.csie.ntu.edu.tw/~cjlin/libsvm+zip), and save to a local directory.
- Unzip the libsvm-x.xx.zip file. 
- Copy the jar file `java/libsvm.jar` to the directory `/Applications/weka-3-6-13-oracle-jvm.app/Contents/Java/Classes`. This directory is in the classpath of Weka
- Go to directory `/Applications/weka-3-6-13-oracle-jvm.app/Contents/Java/Classes`, extract the libsvm.jar by the command `jar xf libsvm.jar`

# Create Data Set

## Training Data Set
For Rehmertz 2, create training data file [rehmertz-2-training.arff](/assets/Rehmertz-2-training.arff).

    %
    % Rehmertz Training Data
    % Reference to the table-1 "实验室中的模拟试验 - 雷墨磁2"
    %
    @relation Rehmertz

    @attribute torque-diff numeric
    @attribute resistance-diff numeric
    @attribute cost numeric
    @attribute result {pass, fail}

    @data
    1.0,0.4,0.18,fail
    0.7,0.9,1.15,pass
    4.0,4.0,4.00,fail
    4.0,4.0,6.00,pass

For Rehmertz 1 and Rehmertz 3, create training data file [rehmertz-1-3-training.arff](Rehmertz-1-3-training.arff).

    %
    % Rehmertz Training Data
    % Reference to the table-2 "实验室中的模拟试验 - 雷墨磁1和雷墨磁3"
    %
    @relation Rehmertz

    @attribute torque-diff numeric
    @attribute resistance-diff numeric
    @attribute cost numeric
    @attribute result {pass, fail}

    @data
    1.0,0.4,0.4,fail
    0.7,0.9,1.1,pass
    4.0,4.0,4.00,fail
    4.0,4.0,6.00,pass
    0.7,0.8,0.75,fail
    1.4,1.0,1.25,pass


## Test Data Set
Create testing data file [rehmertz-test.arff](Rehmertz-2-test.arff).

    %
    % Rehmertz Testing Data
    %
    @relation Rehmertz

    @attribute torque-diff numeric
    @attribute resistance-diff numeric
    @attribute cost numeric
    @attribute result {pass, fail}

    @data
    3,3,0.2,pass
    3,3,0.4,pass
    3,3,0.6,pass
    3,3,0.8,pass
    3,3,1.0,pass
    3,3,1.2,pass
    3,3,1.4,pass
    3,3,1.6,pass
    3,3,1.8,pass
    3,3,2.0,pass
    3,3,2.2,pass
    ... ...
    3,3,8.0,pass


# Detail Steps

## Load traing data set
- Open Weka GUI and click the "Explorer"

    ![Weka_GUI_Chooser](/assets/Weka_GUI_Chooser.png)

- In the "Weka Explorer" Window, select "Preprocess" tab, and click the `Open File` button and select the file `rehmertz-2-training.arff`. The the training data for Rehmertz 2 is loaded.

    ![Weka_GUI_Chooser](/assets/Weka_preprocess.jpg)

## Select Classification Algorithm
- Select the `Classify` tab in the "Weka Explorer" Window, and click `choose` button in Classifier area

    ![Weka_GUI_Chooser](/assets/Weka_choose_classifier.jpg)
    
<!--
### Select the classification algorithm `libSVM` 

![Weka_GUI_Chooser](/assets/libsvm.png)
-->
### Select the classification algorithm `logistic` 

![Weka_GUI_Chooser](/assets/logistic.png)

## Supply test data file
- Under the same `Classify` tab, in the `Test options` area, select `Supplied test set` and click the `Set`-> `Open file...` to the load the file `rehmertz-test.arff`.

![Weka_GUI_Chooser](/assets/supplied-test-set.png)

![Weka_GUI_Chooser](/assets/select-test-data.png)

## Select Classification Options
- Click `More options` in the `Test options` area. In the `Classifier evaluation options` window, select `Output predictions` option, and set `Output additional attributes` to 3.

![Weka_GUI_Chooser](/assets/classification-options.png)

## Run Classfication and Check Result

- Confirm the `(Nom) result` is selected as target. Then click `start`

- The execution result is shown 

![Weka_GUI_Chooser](/assets/logistic-result.png)


