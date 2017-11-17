## Background
The field of medical diagnostics is massive. Money and time is poured into solutions to make diagnosis for 
## Intro
The purpose of this repo is to showcase my learning experience with tensorflow. I followed the image classification retraining tutorial found here https://www.tensorflow.org/tutorials/image_retraining.
I attempted to retrain the final layer of the Inception v3 model against images of chest xrays with and without signs of tuberculosis and then test the newly retrained model.

## Why

## Installation
The first thing I did was install tensorflow. This can be done following the installation instructions here. https://www.tensorflow.org/install/install_sources.
I chose to clone the tensorflow repository like this 

`$ git clone https://github.com/tensorflow/tensorflow` 

I also needed to install python, bazel, and other dependencies using pip. I did so on my Mac 

`$ sudo pip install six numpy wheel`

In order for bazel to work properly, I needed to configure my project like this 

`$ cd tensorflow`

`$ ./configure`

There are various ways to install tensorflow and instructions depend on what machine you're using. Follow the tensorflow docs closely so that you don't get lost like I did initially.

## Dataset Prep
Early in my exploration of applications for machine learning I found a great collection of labelled chest xrays. These images that were classified as abnormal or normal based on observed manifestations of tuberculosis. The images are provided by the U.S. National Library of Medicine and can be found here https://ceb.nlm.nih.gov/repositories/tuberculosis-chest-x-ray-image-data-sets/. The images are seperated into two datasets: the Montgomery County chest X-ray set (MC) and the Shenzhen chest X-ray set. I used only the Shenzhen set in my model. you can read more about the datasets here https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4256233/.

I downloaded the Shenzhen images and converted them all from pngs to JPEG format. I seperated the abnormal and normal images into two folders named abnormal and normal (abnormal images end in 1, normal images end in 0). I then put the abnormal and normal folders into a parent folder called lungs.

## Model Re-training 



