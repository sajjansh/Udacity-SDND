**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of the following 5 steps. 

1. I convert each of the color images to a grayscale image
2. I then apply guassian smoothing with kernel size of 5 to each of the grayscle images 
3. Next, a canny transform is applied to each of these images. I set the parameters low_threshold = 50
    high_threshold = 140 for canny edge detection
4. I then apply the mask to obtain the canny edges only in the regions of interest.
5. Next, a hough transform is applied to the masked images to obtain all possible lines in the regions of interest.
6. Now, in order to draw a single line on the left and right lanes, I modified the draw_lines() function as follows:
   a. I divide the lines obtained from hough transform into two sets. One set contains the lines appearing       on the right half of the image after checking if they have positive slope and if the x coordinates         of their end points lie in the right half part of the image. The second set contains lines on the         left side of the image after checking if they have negative slope and if the x-coordinates of their       end points appear on the left side of the image.
   b.  I sort the lines in each of the above sets in the ascending order of their y co-ordinates.
   c.  I then calculate the average slope of right side lines and left side lines respectively. 
   d.  I then draw a single line on the left and right side by first extrapolating to the end points 
       specified in the region of interest using the respective average slopes and then using cv2.line            function to draw the single left and right lane lines.

###2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when the lanes on the actual road are extremely curved. The region of interest mask would have to be small and we may need to use hough transform to detect curves instead of lines on the image for the code to work well.

Another shortcoming could be when the lane lines are not very clear. The current algorithm does not work for different lighting conditions.


###3. Suggest possible improvements to your pipeline

A possible improvement to detect extrememy curved lanes would be to have a small region of interest mask and we may need to use hough transform to detect curves instead of lines on the image for the code to work well.

In order to detect lanes on images with different lighting conditions, we may have to use a colored mask instead of just converting it to a grayscale image in step 1 of the pipeline.
