#**Traffic Sign Recognition**

---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./examples/Visualization_1.png "Example Images from all classes"
[image2]: ./examples/Visualization_2.png  "Visualization 1"
[image3]: ./examples/Visualization_3.png "Visualization 2"
[image4]: ./examples/Raw-and-processed-Images.png "Processing of Images"
[image5]: ./examples/Test_1.png "Traffic Sign 1"
[image6]: ./examples/Test_2.png "Traffic Sign 2"
[image7]: ./examples/Test_3.png "Traffic Sign 3"
[image8]: ./examples/Test_4.png "Traffic Sign 4"
[image9]: ./examples/Test_5.png "Traffic Sign 5"

## Rubric Points
In this writeup, I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation. I have submitted the project code with the file name "Traffic_Sign_Classifier.ipynb"  

---

##Dataset Exploration

Dataset Summary:

I used the numpy library to calculate summary statistics of the traffic
signs data set:

* The size of training set is 34799
* The size of the validation set is 4410
* The size of test set is 12630
* The shape of a traffic sign image is (32, 32, 3)
* The number of unique classes/labels in the data set is 43

Exploratory Visualization of the dataset:

The figure below shows 43 images, one image from each of the 43 class with their true class value. This figure was plotted just to verify if the classes match the images.

![alt text][image1]

Here is an exploratory visualization of the data set. The first figure below shows the distribution of the classes over the training set, while the second figure shows the distribution of classes over the validation set.

![alt text][image2] ![alt text][image3]

##Design and Test a Model Architecture

Preprocessing:

I have used the following preprocessing techniques:

* Slicing: Here, each image data was sliced in order to center the traffic sign in the images. The edges in most images did not contain any useful information, hence slicing reduced the size of the image data while retaining useful information about the traffic sign.

* Sharpening: I used the technique of Unsharp masking wherein the negative of the blurred image is added to a weighted original image. This helps in sharpening of the image, thus giving the image some contrast. I have used guassian blurring to create the mask. 

Here is how the raw image and the preprocessed image (after slicing + sharpening) look like,

![alt text][image4]

After the above two preprocessing techniques, I combined the training and validation sets and checked for classes that each had less than 1500 image samples. For all such classes, I generated additional data by rotating the image by a random degree. Thus, each class now has atleast 1500 samples corresponding to them. The number 1500 was chosen at random.

After this, I used the train-test-split function from SKlearn library to divide this larger dataset into training and validation sets. The ratio was chosen to be 85:15 (training:validation). The final size of training dataset was 61051  and that of validation set was 10774.


Model Architecture:

My final model consisted of the following layers:

| Layer         		|     Description	        					|
|:---------------------:|:---------------------------------------------:|
| Input         		| 26x26x3 RGB image   							|
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 22x22x6 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 11x11x6  				|
| Convolution 5x5	    | 1x1 stride, valid padding, outputs 7x7x16 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 4x4x16   				|
| Fully connected		| Input 256, Output 120   						|
| RELU + Dropout		|           									|
| Fully connected		| Input 120, Output 84   						|
| RELU + Dropout		|           									|
| Fully connected		| Input 84, Output 43 (logits)   				|


Model Training:

To train the model, I used the Adam Optimizer with a learning rate of 0.001. The batch size was 128 and the number of epochs was 15. The weights were initialized using the truncated normal distribution with mu = 0 and sigma = 0.01. I added dropout for better generalization. The edge probability for drop out was 0.6. 

Solution Approach: 

My final model results were:
* training set accuracy of 98.4%
* validation set accuracy of 98.1%
* test set accuracy of 92.8%

I started with the LeNet architecture because it gave great results with MNIST dataset, which implied it predicts numerical signs very well and that it might predict other shapes as well. 

However, it gave a training and validation accuracy of around 85% without data preprocessing.

With data preprocessing, the size of the input layer of LeNet architecture changed to (26, 26, 3) and 
it gave training accuracy of about 97% whereas the validation set accuracy was around 90%. I then added dropout (edge probability = 0.6) to the fully connected layers of the architecture for better generalization. With this, the training and validation set accuracy were almost equal to 98%. Therefore, the model was able to generalize better and seemed to be working well. The accuracy on the test set was around 92.8% and the per-class accuracy for most classes in the test set was around 90%. 

##Test a Model on New Images

Acquiring New Images:

Here are five German traffic signs that I found on the web:

![alt text][image5] ![alt text][image6] ![alt text][image7]
![alt text][image8] ![alt text][image9]

The first image and the third image might be difficult to classify because the shape of the traffic sign board is not very clear from these images. They are supposed to be round, but the images I got from the web were slightly large and after going through the same steps of preprocessing as the train-validation-test data, the edges of these images which had useful information about sign board shape got cropped off. Therefore, these are the two signs that I think may be difficult to classify. However, the centre of these images is quite clear, so it totally depends on how well the model learns the shape of the board, the shape of the sign thats on the board, the colors on the board and how robust it is to the absence of either one of them. 

Performance on the new images: 

Here are the results of the prediction:

| Image			        |     Prediction	        					|
|:---------------------:|:---------------------------------------------:|
|Speed limit (60km/h)   | Speed limit (60km/h)   						|
| No passing     		| No passing 									|
| Ahead only			| Ahead only									|
| Yield      		    | Yield					 				        |
| Stop			        | Stop     							            |

The accuracy on the new images was 100%. On the test set, the accuracy of each of the classes that these new images belonged to was around 90%. Therefore, the performance of the model on the new images is extremely good.

Model Certainty - Softmax Probabilities:

The code for making predictions on the new images is located in the 23rd cell of the Ipython notebook and the code to output top 5 softmax probabilities for each of these images is located in the 26th cell of the Ipython notebook.

For the first image, the model is extremely sure that this is a speed limit sign and that the speed posted is 60km/hr. The top five soft max probabilities were,

| Probability         	|     Prediction	        					|
|:---------------------:|:---------------------------------------------:|
| .99         			| Speed limit (60km/h)   						|
| .001     				| Speed limit (80km/h) 							|
| .00052				| Speed limit (50km/h)							|
| .000045	      		| Vehicles over 3.5 metric tons prohibited		|
| .0000001				| End of all speed and passing limits      		|


Even for the second image, the model is extremely certain that it is a no passing sign. The top five softmax probabilities were,

| Probability         	|     Prediction	        					|
|:---------------------:|:---------------------------------------------:|
| .99         			| No passing   						            |
| .000001     		    | No passing for vehicles over 3.5 metric tons 	|
| almost 0 				| Yield							                |
| almost 0	      		| Vehicles over 3.5 metric tons prohibited		|
| almost 0				| End of no passing     		                |

For the third image, the model is fairly certain that it is Ahead only sign. The next best guess was that the sign is Turn left ahead, followed by Go straight or right. The model is quite certain that the sign is indicating directions, since the top three guesses are all direction signs. The top five softmax probabilities were,

| Probability         	|     Prediction	        					|
|:---------------------:|:---------------------------------------------:|
| .71         			| Ahead only   						            |
| .12    		        | Turn left ahead 	                            |
| .11 				    | Go straight or right						    |
| .003	      		    | Children crossing		                        |
| .0013				    | Road work    		                            |

For the fourth image, the model is 100% certain that it is Yield sign. The softmax probabilities were zero for other classes.

For the fifth image, the model is extremely certain that it is Stop sign. The top 5 softmax probabilities were,

| Probability         	|     Prediction	        					|
|:---------------------:|:---------------------------------------------:|
| .99         			| Stop   						                |
| .00000010    		    | No entry 	                                    |
| almost 0 				| Speed limit (50km/h)						    |
| almosy 0    		    | Yield		                                    |
| almost 0 			    | Speed limit (30km/h)   		                |

