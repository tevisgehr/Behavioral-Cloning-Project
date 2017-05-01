# Behavioral Cloning Project
### Project Information

## Final Model
### 1. Data Processing 

(in order of implimantation in final Jupyter notebook file)
  
  #### a. Including left and right camera images with a steering correction factor.
  
In order to help the car stay in the middle of the road and to recover when it aproached the lane lines or edges, it was nessesary to use all three camera angles. A correction of 0.3 was added to the side cameras. 
  
  #### b. Resizing Images
  
The images were resized from 164x320x3 to 64x64x3. Not only did this improve performance, but it vastly improved training time. 
  
  #### c. Augmenting with Flipped Images
  
After viewing a histogram of the dispersion of steering angles in the training set, it was apparent that the car spent more time turing left that turning right. To fix this skew and to supply additonal data cheaply, all images were flipped horizontally. The corresponding steering measurements were likewise multipled by negative one.
  
  #### d. Normalizing 
  
Within the Keras model, all image pixel values were normalized from a range of [0,255] to [-0.5,0.5]. This is generally good practice with neural networks and was seen to improve convergence on a good model.   
  
  #### e. Image Cropping
  
Also with the Keras model, the top 25 rows (of 64) were cropped out to get rid of image data above the horizon. Also the bottom 10 rows were cropped out to remove the hod of the car. 
  
  
(Include Images)

### 2. Model Architecture
The following architecture was implimented in Keras:

2D Convolutional Layer - 5x5, stride of 1. Depth of 24.

Activation - Relu

Subsampling - 2x2

2D Convolutional Layer - 5x5, stride of 1. Depth of 36.

Subsampling - 2x2

Activation - Relu

2D Convolutional Layer - 5x5, stride of 1. Depth of 48.

Subsampling - 2x2

Activation - Relu

2D Convolutional Layer - 3x3, stride of 1. Depth of 64.

Activation - Relu

Fully Connected Layer - 100 Neurons

Fully Connected Layer - 50 Neurons

Fully Connected Layer - 10 Neurons

Output Layer - 1 Neuron

(Include Images?)


## Training Approach
(Reference Behavioral Cloning Project Log.pdf. File included in repository.)
1. Overview
(include data capture attempts)
(include saving model after each epoch and using checkpoints)
2. Detailed Account

#### Rev 1-

#### Rev 2-

#### Rev 3-

#### Rev 4-

#### Rev 5-

#### Rev 6-

#### Rev 7-

#### Rev 8-

#### Rev 9-

#### Rev 10-

## Conclusions


