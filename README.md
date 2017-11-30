## Intro
The purpose of this repo is to showcase my learning experience with tensorflow. I followed the image classification retraining tutorial found here https://www.tensorflow.org/tutorials/image_retraining.
I attempted to retrain the final layer of the Inception v3 model against images of chest x-rays with and without signs of tuberculosis and then test the newly retrained model. Or in plain english, I wanted to see if I could diagnose tuberculosis with machine learning!

![alt text](https://github.com/Ha-san-ali/xray-ml-demo/blob/master/tf.png?raw=true "tensorflow")

I'm going to preface this by mentioning that I am not a developer, or data scientist. However, I am curious and interested in the field of machine learning - especially its applications to healthcare. 

## Installation
Rather than lead you astray with my own installation instructions, I recommend that anyone wanting to follow what I did use the official instructions from tensorflow https://www.tensorflow.org/install/install_sources.

## Dataset Prep
Having a properly labelled dataset is essential for this method of machine learning. 
Early on I stumbled across a great collection of labelled chest x-rays. These images were classified as either abnormal or normal based on observed manifestations of tuberculosis. The images are provided by the U.S. National Library of Medicine and can be found here https://ceb.nlm.nih.gov/repositories/tuberculosis-chest-x-ray-image-data-sets/. The images are separated into two datasets: the Montgomery County chest X-ray set and the Shenzhen chest X-ray set. I used only the Shenzhen set in my model. You can read more about the datasets here https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4256233/.

I downloaded the Shenzhen images and converted them all from pngs to JPEG format. I separated the abnormal and normal images into two folders (abnormal images end in 1, normal images end in 0). Then I put the abnormal and normal folders into a parent folder called lungs. I took out 50 images from the dataset (25 abnormal and 25 normal) for testing later on. 

![alt text](https://github.com/Ha-san-ali/xray-ml-demo/blob/master/sample_image_1.jpg?raw=true  "chest x-ray")

## Model Re-training 
Once I had tensorflow installed and the images prepared I proceeded to retrain the model. 

First I built the retrainer from within the tensorflow source directory. This took just over an hour on my four year old Mac. I did it in terminal with the following commands

`$ cd tensorflow`

`$ bazel build tensorflow/examples/image_retraining:retrain`

Then I ran the retrainer against my chest x-ray images. 

`$ bazel-bin/tensorflow/examples/image_retraining/retrain --image_dir /Users/hali/lungs`

` /Users/hali/lungs` is based on where I stored my images. Change it to suit your needs.

This process is known as transfer learning. It allows us to leverage the knowledge of the pre-trained Inception model (trained against the ImageNet database) and apply it to our labelled dataset.

First, bottlenecks are calculated. Bottlenecks are values for each image in the dataset that are cached and later fed to the classification layer. The bottlenecks are meant to be meaningful and compact pieces of information that the classification layer can use to distinguish the image quickly and accurately. Without pre-caching bottlenecks the retraining process would take significantly longer. 

Retraining is broken down into steps. In each step 10 images are randomly chosen from a training set. Their bottlenecks are then fed to the final layer of the model and used to predict their classification. Whether the classifier was right or wrong will then impact the weights given to the nodes in the model. Within each step 3 values are calculated - training accuracy, cross entropy, and validation accuracy.

Training accuracy tells us what percentage of the training images were classified correctly.
Cross entropy values will trend downwards if the model is learning. The objective of training is to make the cross entropy as small as possible.
Validation accuracy is a true indication of the performance of the model. 
The following is an example output of a training step. 

`Step 1750: Train accuracy = 95.0%`

`Step 1750: Cross entropy = 0.205942`

`Step 1750: Validation accuracy = 82.0% (N=100)`

## Testing My Model

Tensorflow will spit out a final test accuracy for the model. 

`Final test accuracy = 84.1% (N=69)`

Tensorflow automatically sets a portion of the images for testing.
I also did my own testing using the 50 images that I removed from the dataset earlier. It's important not to test with images that were used to train the model. I saved my testing images in folders named abnormal_test and normal_test.

To test, I entered the following into terminal

```
bazel build tensorflow/examples/image_retraining:label_image && \
bazel-bin/tensorflow/examples/image_retraining/label_image \
--graph=/tmp/output_graph.pb --labels=/tmp/output_labels.txt \
--output_layer=final_result:0 \`
--image=$HOME/desktop/abnormal_test/CHNCXR_0327_1.jpeg
```

Change `$HOME/desktop/abnormal_extra_jpg/CHNCXR_0327_1.jpeg` to point at the image you want to test.

After a few moments tensorflow spit out the following

```
abnormal (score = 0.96525)
normal (score = 0.03475)
```

Here the model states that the image I presented was more similar to an abnormal chest x-ray than a normal one. It also states the relative strength of that prediction. For this particular image, you could say the model was confident with the prediction (~97%).

## Optimizations and Experimentation 

There are various things that can be done to the dataset and the model in order to affect the accuracy and speed of training. I used the flag `--how_many_training_steps` to change the number of steps that were run during training. I changed `--learning_rate` to change the size of the updates made to the final layer of the model during training. There are various other flags that can be used to adjust the dataset, and hopefully increase effectiveness of the model. The `--print_misclassified_test_images` tag is also useful at looking at what images the model could not correctly classify during training. As I found later, it's inappropriate to try to improve the model be removing those difficult images - it doesn't fix the root issue and leads to a more fragile model. 

I tested my dataset with 3 different parameter settings, focusing solely on training steps and learning rate. I was able to achieve slightly different results with different parameters. Later, I found I was also able to improve accuracy by using higher quality jpeg images.

By looking strictly at whether my model was right or wrong - and not considering strength of association. My model went from being correct **60%** of the time, to **74%** of the time, to **78%** of the time. This was done through increasing the number of training steps, playing around with the learning rate, and removing difficult before from training. 

## Assumptions and errors

So at this point I was very pleased with myself for having a functioning model with decent results. However, I wasn't aware of _how_ my model was differentiating between the images, or _why_ it was correctly classifying some images and not others.  I discovered that my dataset was small, all of my images came from one hospital, the population they came from was the same, and the way in which the pictures were presented was always the same. These factors directly impact the _generalizability_ of the model. That is to say, that my model may only be effective for the particular dataset that I have presented. 

Some ways to increase the generalizability of the model would be to increase the size of the dataset, add more variance to the images, play with hyperparameters, add x-rays presented in different ways, and add noise to the images. Humans come in all shapes and sizes, its important to include images that represent as many variations as possible in order to make the model more robust.

It is important to note that I only retrained the model on chest x-rays. What would happen if I present it something completely different?

I presented to it the following image.
![alt text](https://github.com/Ha-san-ali/xray-ml-demo/blob/master/cow.jpg?raw=true "cow")

One would suspect that an image of a cow would not be associated with either abnormal or normal chest x-rays, however the model stated differently. 

```
normal (score = 0.99828)
abnormal (score = 0.00172)
```

In fact, the model presents a near 100% association to normal chest x-rays. What features could possibly exist within this cow image that are so strongly associated with features of normal chest x-rays? To answer that question is my next task.
I look forward to exploring the mechanism by which the model is able to detect features, and how it uses those features in classification. 
