[//]: # (Image References)

[image1]: ./Model_Arcitecture.png "Model Architecture"
[image3]: ./h-channel.png "H-Channel"
[image4]: ./s-channel.png "S-Channel"
[image5]: ./v-channel.png "V-Channel"
[image6]: ./hist1.png "Steering Angle Distribution"
[image7]: ./hist2.png "After Flipping"
# Behavioral Cloning Project

The purpose of this project was to teach an agent to drive a simualted car around a track. This was accomplised by using a convolutional neural network on top of several image processing techniques from OpenCV. Multiple combinations of image processing techniques and CNN architectures were attempted until a viable solution was found that was able to successfully clone driving behavior.  
  
These approaches are described below and in greater detail in the log pdf file. The processing and CNN code are contained in the Jupyter notebook file. The interface code between the model and the driving program is contained in drive.py.  

This project was a great challenge. Although the final CNN structure is reasonably simple, it took many iterative steps to reach a viable solution.   

## Final Model
### 1. Data Processing  
(in order of implementation in final Jupyter notebook file)
  
#### a. Including left and right camera images with a steering correction factor
In order to help the car stay in the middle of the road and to recover when it approached the lane lines or edges, it was necessary to use all three camera angles. A correction of 0.3 was added to the side cameras. 
  
#### b. BGR2HSV Colorspace mapping  
Performed a colorspace mapping using cv2.cvtColor library. Only used the saturation 's' channel. This gets rid of a lot of the noise that is inherent in a BGR colorspace. 

![alt text][image3]
![alt text][image4]
![alt text][image5]  
Figure 1:H, S, and V Channels respectively.

#### c. Resizing Images  
The images were resized from 164x320x3 to 64x64x3. Not only did this improve performance, but it vastly improved training time. 
  
#### d. Augmenting with Flipped Images  
After viewing a histogram of the dispersion of steering angles in the training set, it was apparent that the car spent more time turning left that turning right. To fix this skew and to supply additional data cheaply, all images were flipped horizontally. The corresponding steering measurements were likewise multiplied by -1.

![alt text][image6]
![alt text][image7]  
Figure 2:Flipped versions of the same image.
  
#### e. Normalizing  
Within the Keras model, all image pixel values were normalized from a range of [0,255] to [-0.5,0.5]. This is generally good practice with neural networks and was seen to improve convergence on a good model.   
  
#### f. Image Cropping  
Also with the Keras model, the top 25 rows (of 64) were cropped out to get rid of image data above the horizon. Also the bottom 10 rows were cropped out to remove the hood of the car. 


### 2. Model Architecture  
The following architecture was implemented in Keras:

![alt text][image1]

## Training Approach  
(Reference "Behavioral Cloning Project Log.pdf". File included in repository.)
### 1. Overview

The process of refining this model in order to obtain the required performance was very iterative. Early on it became clear that it would be necessary to use a systematic approach and to carefully document all changes to various revisions of the model. I went through 13 major revisions in order to find a model that could make it all the way around the track without ever hitting the edge. In my final model the car never touches the lanes lines (see the video2.mp4).

I used an alternating approach of trying various network architectures based off of the NVIDIA architecture, interspersed with implementing various data processing and augmentation techniques. When an (subjective) improvement was realized, I would use the most recent approach as a starting point for a new set of attempts. In the end it seemed that the NVIDIA architecture was too large for 64x64 images. The final model was nearly identical to the NVIDIA architecture, minus the final 64-depth convolutional layer. 

I ran several sessions of recording my own data. The first contained about as many samples as the original Udacity dataset, and the second was much larger. I was unable to every get better results from using my own data than I was from using the Udacity dataset on an equivalent network architecture. I eventually also tried appending all of my generated data onto the end of the Udacity dataset, under the hope that 'more data is always better'. this approach led to a reasonably good model (one of the best actually), but in the end the final model was created using only the Udacity dataset. 

One of the most important techniques that enabled convergence on an acceptable model was the use of rapid prototyping. I tried to find ways to quickly produce a lot of models so that testing (simulated driving) could run almost continuously. Because on one model could be tested at a time, I tried to make sure that this was the only bottleneck of the system. In order to do this I implemented the image resizing (as described above). I also implemented a checkpoint system within Keras that saves a new model after every epoch and allows for a previously tested model to be loaded and further trained. I also included graphs of training and validation loss that allows for a visualization of the training process of each architecture from epoch to epoch. Lastly I allowed multiple models to be training at the same time, one on an AWS instance, and another on my local machine. The system required constant vigilance, but created virtually no down-time waiting for models to train. All of this allowed me to rapidly produce and test several hundred models over the course of 3 days, before I decided to select my best attempt and call it quits.

(include data capture attempts)
(include saving model after each epoch and using checkpoints)
### 2. Detailed Account

Each of these revisions had multiple sub-revisions that can be seen seen in this repository and are described in detail in the log file pdf. 

#### Rev 1-
Implemented a very simple fully-connected network to see is I could get the car to run off of the model. Then I tried a simple convolutional network and was quickly able to produce a model that would drive the car reasonably well up until at least the first sharp turn.    

#### Rev 2-
Due to the high number of zeros in the Udacity training set steering angles, I collected my own data with a mouse. I was unable to get any improvements from using my own data and eventually went back to the Udacity dataset.

#### Rev 3-
I fully implemented the suggested pipeline from the project videos, starting with the LeNet architecture and eventually switching to the NVIDIA architecture. I also tried downsampling the number of records with a steering angle of zero by randomly dropping a percentage of the zero-angle images before training the network. This approach was made unnecessary later when I started using the data from all 3 cameras.

#### Rev 4-
Made various changes to the NVIDIA architecture including adding or subtracting layers, changing the number of neurons in various layer, adding dropout, using various batch sizes, and training for various numbers of epochs. 

#### Rev 5-
I played around with performing Canny transforms on the training images. Performing variation on Canny transform parameters including thresholds and Gaussian blur kernels, I was able to train a model that performed reasonably well on Canny-transformed images. I also had to modify the code within drive.py in order to transform the new images gathered by the car as it was driving (this code has been left in the drive.py file for reference, commented out). I ended up abandoning the idea in favor of implementing a variety of other data processing techniques (as described above).

#### Rev 6-
Started using the Keras ModelCheckpoint() class to make models at every epoch and to allow for model loading and retraining. This greatly sped up the training precess, as described above. Started seeing better performance in some of the models.


#### Rev 7-
Decided to make another attempt at creating my own data. I recorded numerous laps around the track in both directions and tried to drive very smoothly. My dataset was about twice the size of the Udacity dataset. Although the data seemed good, I was unable to get any improvement in performance.

#### Rev 8-
Implemented a resizing of the training images to 64x64x3. This drastically improved both training time and model performance. I tried out a variety of architectures on the new image size and ended up dropping off the last convolutional layer from the NVIDIA architecture. This is when my car first managed to go all the way around the track, with some instances of rolling over curbs. 


#### Rev 9-
Started using the data from all three cameras, with various correction factors. This showed immediate improvements in performance. Within a few sub-revisions I was able to create a model that completed the requirements of the project. This is the model that was first submitted. Because the model still rolls over the painted lines in a couple of places along the track, I kept trying to get better performance in Revs 10-13. 

#### Rev 10-
Tried appending my collected data to the Udacity data to have one massive dataset with about 120k samples. No improvement.


#### Rev 11-
Tried using a Relu activation function on my fully-connected layers (which was not present in any of the previous revisions). No improvement.

#### Rev 12-
Changed from using the Convolution2d keras class to the Conv2d (it looks like what I was using is an old version. Added model.summary() to keep track of numbers of parameters. Played around with various Conv2d architectures. Saw reasonable performance but no serious improvements.

#### Rev 13-
Mapped images into HSV colorspace. This was the 'magic bullet' After doing this I was quickly able to obtain a working model with far fewer parameters.

## Conclusions
The most significant finding of this project was the demonstration of the need for a systematic approach to network development and model training. It seems that there is a requirement for a balance between intuitive inspiration and disciplined iteration. While training models I often had a string of ideas pop into my head all at the same time, and was unable to test them, all at one. It was important to document these ideas as they came up and also to document the results of each test. Also it was crucial to resist the urge to make multiple changes at the same time. 

One of the greatest challenges was trying to determine whether a particular model was underfit or overfit. Small changes a single parameter or a difference of a single training epoch could result in drastic changes in driving performance. It is difficult to guess whether a similar model would be able to generalize to other situations, or if one would have to make significant changes. For the time being, it appears that much of the process of behavioral cloning through deep neural networks is a matter of trial and error.


