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


Install the prerequisite packages.

apt-get update
apt-get install -y protobuf-compiler python-pil python-lxml python-pip python-dev git
pip install Flask==0.12.2 WTForms==2.1 Flask_WTF==0.14.2 Werkzeug==0.12.2
pip install --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.1.0-cp27-none-linux_x86_64.whl
Install the Object Detection API library.

cd /opt
git clone https://github.com/tensorflow/models
cd models/research
protoc object_detection/protos/*.proto --python_out=.
Download the pre-trained model binaries by running the following commands.

mkdir -p /opt/graph_def
cd /tmp
for model in \
  ssd_mobilenet_v1_coco_11_06_2017 \
  ssd_inception_v2_coco_11_06_2017 \
  rfcn_resnet101_coco_11_06_2017 \
  faster_rcnn_resnet101_coco_11_06_2017 \
  faster_rcnn_inception_resnet_v2_atrous_coco_11_06_2017
do \
  curl -OL http://download.tensorflow.org/models/object_detection/$model.tar.gz
  tar -xzf $model.tar.gz $model/frozen_inference_graph.pb
  cp -a $model /opt/graph_def/
done
Choose a model for the web application to use. For example, to select faster_rcnn_resnet101_coco_11_06_2017, enter the following command:

ln -sf /opt/graph_def/faster_rcnn_resnet101_coco_11_06_2017/frozen_inference_graph.pb /opt/graph_def/frozen_inference_graph.pb
Install and launch the web application

Install the application.

cd $HOME
git clone https://github.com/GoogleCloudPlatform/tensorflow-object-detection-example
cp -a tensorflow-object-detection-example/object_detection_app /opt/
cp /opt/object_detection_app/object-detection.service /etc/systemd/system/
systemctl daemon-reload
The application provides a simple user authentication mechanism. You can change the username and password by modifying the /opt/object_detection_app/decorator.py file.

USERNAME = 'username'
PASSWORD = 'passw0rd'
Launch the application.

systemctl enable object-detection
systemctl start object-detection
systemctl status object-detection
The last command outputs the application status, as in the following example:

● object-detection.service - Object Detection API Demo
   Loaded: loaded (/opt/object_detection_app/object-detection.service; linked)
   Active: active (running) since Wed 2017-06-21 05:34:10 UTC; 22s ago
  Process: 7122 ExecStop=/bin/kill -TERM $MAINPID (code=exited, status=0/SUCCESS)
 Main PID: 7125 (app.py)
   CGroup: /system.slice/object-detection.service
           └─7125 /usr/bin/python /opt/object_detection_app/app.py

Jun 21 05:34:10 object-detection systemd[1]: Started Object Detection API Demo.
Jun 21 05:34:26 object-detection app.py[7125]: 2017-06-2105:34:26.518736: W tensorflow/core/platform/cpu_fe...ons.
Jun 21 05:34:26 object-detection app.py[7125]: 2017-06-2105:34:26.518790: W tensorflow/core/platform/cpu_fe...ons.
Jun 21 05:34:26 object-detection app.py[7125]: 2017-06-2105:34:26.518795: W tensorflow/core/platform/cpu_fe...ons.
Jun 21 05:34:26 object-detection app.py[7125]: * Running on http://0.0.0.0:80/ (Press CTRL+C to quit)
Hint: Some lines were ellipsized, use -l to show in full.

The application loads the model binary immediately after launch. It will take a minute to start serving requests from clients. You'll see the message Running on http://0.0.0.0:80/ (Press CTRL+C to quit) when it's ready.
