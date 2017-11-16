### Creating an Object Detection Application Using TensorFlow in Data Science VM in Azure
This tutorial describes how to install and run an object detection application. The application uses TensorFlow and other public API libraries to detect multiple objects in an uploaded image.

## Objectives

This is a basic tutorial designed to familiarize you with TensorFlow applications. When you are finished, you should be able to:

* Create a Azure Data Science virtual machine (VM) 
* Install the Object Detection API library. 
* Install and launch an object detection web application.
* Launch Jupyter Notebook in DSVM to check the app.
* Test the web application with uploaded images.

## TensorFlow architecture overview

The object detection application uses the following components:

* [TensorFlow](https://www.tensorflow.org/), the open source deep learning framework 
* [Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)
* [Pre-trained object detection models](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md)
