# **Behavioral Cloning** 

## Writeup Report

---

**Behavioral Cloning Project**

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./examples/modelviz.png "Model Visualization"
[image2]: ./examples/center.png "Center"
[image3]: ./examples/comeback1.png "Recovery Image"
[image4]: ./examples/comeback2.png "Recovery Image"
[image5]: ./examples/comeback3.png "Recovery Image"
[image6]: ./examples/normal.png "Normal Image"
[image7]: ./examples/flipped.png "Flipped Image"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/432/view) individually and describe how I addressed each point in my implementation.  

---
### Files Submitted & Code Quality

#### 1. Submission includes all required files and can be used to run the simulator in autonomous mode

My project includes the following files:
* model.py containing the script to create and train the model
* drive.py for driving the car in autonomous mode
* model.h5 containing a trained convolution neural network 
* writeup_report.md summarizing the results
* video.mp4 (a video recording of my vehicle driving autonomously around the track for at least one full lap)

#### 2. Submission includes functional code
Using the Udacity provided simulator and my drive.py file, the car can be driven autonomously around the track by executing 
```sh
python drive.py model.h5
```

#### 3. Submission code is usable and readable

The model.py file contains the code for training and saving the convolution neural network. The file shows the pipeline I used for training and validating the model, and it contains comments to explain how the code works.

### Model Architecture and Training Strategy

#### 1. An appropriate model architecture has been employed

I used the model architecture published by the autonomous vehicle team at NVIDIA which consists of a normalization layer, followed by 5 convolutional layers, followed by 4 fully connected layers. The convolution neural network has 5x5 for the first 3 filter sizes with strides of (2,2) each and 3x3 filter sizes for the last 2, and filter depths between 24 and 64 (model.py lines 49-67).

The model includes RELU layers to introduce nonlinearity (model.py lines 55-59), and the data is normalized in the model using a Keras lambda layer (model.py line 52). 

#### 2. Attempts to reduce overfitting in the model

The model contains no dropout layers and was not trained and validated on different data sets to ensure that the model was not overfitting. I focused on collecting more data and further augment the data set that I had. I reduced the number of epochs if the loss started increasing. The model was tested by running it through the simulator and ensuring that the vehicle could stay on the track.

#### 3. Model parameter tuning

The model used an adam optimizer, so the learning rate was not tuned manually (model.py line 66).

#### 4. Appropriate training data

Training data was chosen to keep the vehicle driving on the road. I used a combination of center lane driving, recovering from the left and right sides of the road, driving by the dirt boundary multiple times, and smooth driving around curves. I also did a partial lap on the bonus track. Unfortunately none of this did the trick. The sample data provided ended up being all I needed. 

For details about how I created the training data, see the next section. 

### Model Architecture and Training Strategy

#### 1. Solution Design Approach

The overall strategy for deriving a model architecture was to follow David Silver's methodologies in the Udacity module videos in the project. In the end, Paul Heraty's helpful guide that was provided in the "Further Help" section of the submission page convinced me to stick to the NVIDIA model architecture.

My first step was to use a simple model with no convolutions to get the car driving. Afterwards I used a convolution neural network model similar to the LeNet architecture. I thought this model might be appropriate because it was simple to implement and a great starting point to see how convolutions would improve the vehicle's driving.

In order to gauge how well the model was working, I split my image and steering angle data into a training and validation set. I found that my first model had a low mean squared error on the training set but a high mean squared error on the validation set. This implied that the model was overfitting. 

To combat the overfitting, I collected more data and further augmented the data set. I modified the model so that it utilized the left and right camera images with corrected steering angles and I flipped all the images horizontally. This supplied me with 6 times the amount of data than I originally began with. I recorded 3 laps on the first track and part of a lap on the bonus track.

Then I upgraded to the NVIDIA model architecture, I cropped all images so that the model could focus on just training on the road instead of the pixels that made up the hood of the car, the sky, and the trees. I also normalized the adata and reduced the number of epochs to 5. 

The final step was to run the simulator to see how well the car was driving around track one. There were a few spots where the vehicle fell off the track so to improve the driving behavior in these cases, I recorded recovery vehicle behavior and behavior of how the vehicle should drive in these trouble spots. Unfortunately the vehicle still gave me troublesome behavior. I had completely deleted the sample data in order to train the model solely on data that I collected and that was unsuccessful. I decided to delete all the data I accumulated and redownload the sample data. The difference this time is that I would record new data to supplement the sample data provided instead of replace it. I decided to begin by training the model solely on the sample data that I redownloaded and to my surprise, it worked! No need to record new data!

At the end of the process, the vehicle is able to drive autonomously around the track without leaving the road.

#### 2. Final Model Architecture

The final model architecture (model.py lines 49-67) consisted of a convolution neural network with the following layers and layer sizes:

* normalization layer (-0.5 to 0.5)
* cropping functions (removes top 70 pixels and bottom 25 pixels)
* 5x5 convolutional layer (filter depth 24, stride of (2,2), relu activation)
* 5x5 convolutional layer (filter depth 36, stride of (2,2), relu activation)
* 5x5 convolutional layer (filter depth 48, stride of (2,2), relu activation)
* 3x3 convolutional layer (filter depth 64, relu activation)
* 3x3 convolutional layer (filter depth 64, relu activation)
* flatten layer function
* fully connected layer (100 neurons)
* fully connected layer (50 neurons)
* fully connected layer (10 neurons)
* fully connected layer (1 neurons)

Here is a visualization of the architecture (note: visualizing the architecture is optional according to the project rubric)

![alt text][image1]

#### 3. Creation of the Training Set & Training Process

To capture good driving behavior, I first recorded 3 laps on track one using center lane driving, and a partial lap on track two using right lane driving. Here is an example image of center lane driving:

![alt text][image2]

I then recorded the vehicle recovering from the left side and right sides of the road back to center so that the vehicle would learn to avoid hitting the wall. These images show what a recovery looks like starting from ... :

![alt text][image3]
![alt text][image4]
![alt text][image5]

Then I repeated this process on track two in order to get more data points.

To augment the data set, I also flipped images and angles thinking that this would remove the counter-clockwise driving bias. For example, here is an image that has then been flipped:

![alt text][image6]
![alt text][image7]

After the collection process, I had 6 times the number of data points (38572 images total). I then preprocessed this data by cropping the images to get rid of the top 70 pixels (sky and trees) and bottom 25 pixels (hood of car) that wouldn't help train the model on the pixels that included the road.

I finally randomly shuffled the data set and put 20% of the data into a validation set. 

I used this training data for training the model. The validation set helped determine if the model was over or under fitting. The ideal number of epochs was 5 as evidenced by the increase in loss on the 6th epoch. I used an adam optimizer so that manually training the learning rate wasn't necessary.
