## Intro
The purpose of this repo is to showcase my learning experience with tensorflow. I followed the image classification retraining tutorial found here https://www.tensorflow.org/tutorials/image_retraining.
I attempted to retrain the final layer of the Inception v3 model against images of chest xrays with and without signs of tuberculosis and then test the newly retrained model.

I'm going to preface this by mentoning that I am not a software developer, or data scientist. However, I am curious and interested in the field of machine learning - especiialy its applications to healthcare. That's why I decided to take a stab at doing some machine learning with tensorflow.  

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
Having a properly labelled, large, representative dataset is essential when using this method. 
Early on I stumbled accros a great collection of labelled chest xrays. These images that were classified as abnormal or normal based on observed manifestations of tuberculosis. The images are provided by the U.S. National Library of Medicine and can be found here https://ceb.nlm.nih.gov/repositories/tuberculosis-chest-x-ray-image-data-sets/. The images are seperated into two datasets: the Montgomery County chest X-ray set (MC) and the Shenzhen chest X-ray set. I used only the Shenzhen set in my model. you can read more about the datasets here https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4256233/.

I downloaded the Shenzhen images and converted them all from pngs to JPEG format. I seperated the abnormal and normal images into two folders named abnormal and normal (abnormal images end in 1, normal images end in 0). I then put the abnormal and normal folders into a parent folder called lungs.

## Model Re-training 
Once I had tensorflow installed and the images prepared I proceeded to retrain the model. 

First I built the retrainer from within the tensorflow source directory

`$ cd tensorflow`
`bazel build tensorflow/examples/image_retraining:retrain`

Then I ran the retrainer against my chest xray images. 

`bazel-bin/tensorflow/examples/image_retraining/retrain --image_dir /Users/hali/lungs`

This process is known as transfer learning. It essentially allows us to leverage the knoweldge of the pre-trained Inception model (trained against the ImageNet database) and apply it to our specific labelled dataset.

Retraining is broken down into steps. In each step 
