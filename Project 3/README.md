#**Advanced Lane Detection**
---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/undistorted_image.jpg "Undistorted"
[image2]: ./output_images/undistorted_test_image.jpg "Undistorted test image"
[image3]: ./output_images/warped_test_image.jpg "Warp test image"
[image4]: ./output_images/thresholded_binary_test_image.jpg "Thresholded binary image"
[image5]: ./output_images/historgram.jpg "Histogram"
[image6]: ./output_images/fit_polynomial.jpg "Polynomial fit"
[image7]: ./output_images/fit_polynomial_2.jpg "Polynomial fit using previous fit"
[image8]: ./output_images/Lanes_drawn.jpg "Image with lane drawn"
[image9]: ./output_images/Lanes_drawn_with_data.jpg "Image with lane and the data drawn"
[video1]: ./project_video_output_final.mp4 "Video"


### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  
You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook titled `Advanced_Lane_Lines_Final.ipynb` in the submissions folder.

First, I started by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world (9*6*3). Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image using `cv2.findChessboardCorners`.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
The first step in the pipeline consists of undistorting the test image included in the function `undistort()`. To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The second step in the pipeline consists of obtaining a warped image after applying the perspective transform. I did this step before applying color and gradient transforms since it gave better results.
The code for my perspective transform includes a function called `perspective_transform()`.  The `perspective_transform` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the following hardcoded source and destination points after a lot of trial and error:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. The original test image and the warped image after applying the perspective transform are shown below:
![alt text][image3]

#### 3. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

After trying various combinations, I settled on using a combination of thresholded gradient magnitude on the l-channel of the image and the thresholded s color channel. The code to get the thresholded binary image after applying color and gradient transforms is included in the function called `threshold_binary()` 
Here's an example of my output for this step.  

![alt text][image4]


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

A histogram of the image is calculated to detect the position of the lane lines. An example of how the histogram is obtained over the lower half of the test image is show below:

![alt text][image5]

This step is repeated in a sliding window manner from the bottom of the image and a second order polynomial is fit for each window. The functions `sliding_window_fit()` and `polyfit_using_prev_fit` in the code fit a second order polynomial to the lane lines detected. `sliding_window_fit()` essentially identifies 9 windows starting from bottom to top of the image and fits a polynomial for each window. `polyfit_using_prev_fit` does the same job as the `sliding_window_fit()` function, but it only searches for lane line pixels within a certain range of the previous fit which might have been obtained from the previous frame of a video.  An example output after passing an image to the `sliding_window_fit()` function is shown below:

![alt text][image6]

An example output after passing an image to the `polyfit_using_prev_fit`  function is shown below:

![alt text][image7]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The radius of curvature and the position of the vehicle with respect to the center is calculated by the function `curvature` in the code. The radius of curvature is calculated by determining the radius of the circle that passes through most points along the lane in each frame. The position of the vehicle is calculated by taking the difference of the car position (image center) and midpoint of the two lanes which is calculated by taking the average of the x intercepts of the polynomial fit. 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The function to plot the lane onto the original image is done using the `draw_lane()` and the data (radius of curvature and position of the vehicle) is plotted on this image using the function `draw_data`. Using the polynomial fits obtained for the left and right lane lines, a polygon is calculated which is drawn onto the original image after calculating the inverse perspective transform (in the same function `perspective_transform` used to calculate the perspective transform). This polygon is then drawn on the undistorted version of the original image. Here's how the output looks like:

![alt text][image8]

The output after drawing the data looks as follows:

![alt text][image9]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output_final.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Out of all the steps in the pipeline, I spent a lot of determining what combination of color and gradient threshold would yield the best image for lane detection. I tried several combinations and finally converged to using s-channel for both color thresholding and gradient thresholding, because it gave decent results on the images provided. I am not sure if this will provide good results on videos with worse lighting conditions.

The second problem I faced was with choosing the  source and destination points to obtain the perspective transform. I used hardcoded points ultimately but it would have been better if I could somehow get these points from the image itself. The reason is that I didnt always obtain perfectly parallel lanes from bird's eye view for the given source and destination points. 

For the challenge video (not submitted), I need to make my fits more robust and try out different methods to do so. Currently, the codes for polynomial fits are those from the course lectures itself. I will look for better methods in the due course of time. 


