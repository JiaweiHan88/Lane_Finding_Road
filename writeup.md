# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)


[image1]: ./test_images_out/solidWhiteCurve.jpg_step1.jpg 
[image2]: ./test_images_out/solidWhiteCurve.jpg_step2.jpg 
[image3]: ./test_images_out/solidWhiteCurve.jpg_step3.jpg 
[image4]: ./test_images_out/solidWhiteCurve.jpg_step4.jpg 
[image5]: ./test_images_out/solidWhiteCurve.jpg_step5_1.jpg 
[image6]: ./test_images_out/solidWhiteCurve.jpg_step5_2.jpg

[image7]: ./test_images_out/test22.jpg_step1.jpg
[image8]: ./test_images_out/test22.jpg_step3.jpg



---

### Reflection

### 1. Image Processing pipeline

My pipeline consisted of the following steps:
####1. convert to grayscale to have only to deal with one channel, result looks like:

![alt text][image1]

####2. apply gaussian blur to reduce noise, we use a kernel size of 5, result looks like:

![alt text][image2]

####3. use canny edge detection to reduce pixel information to only consists of edges

We use the tool provided by https://medium.com/@maunesh/finding-the-right-parameters-for-your-computer-vision-algorithm-d55643b6f954
to find appropriate parameter

![alt text][image3]

####4. use roi definition to reduce area
here we use a polygon with 4 points to blend out most of unnecessary information

![alt text][image4]

####5. Hough transformation to find a set of lines
We had to play around with the parameters, reducing the resolution seems to provide better results

![alt text][image5]

this image contain all the recognized lines and also the average line we calculated

####6. modify the draw_lines function
This part was actually more tricky, since previously, only the parameters had to be tried out.
First step is to filter the resulting lines and sort them by left and right part of the lane.
Observing the video and images, we can determine that lane markings are more vertical then horizontal, in the coordinate system
of the image it means, that an absolute slope of 0.5 and above leads to a more vertical orientation of the line.
What we can also observe is that the slope is always negativ for the left lane marking and positive for the right.
This might be not the case in specific scenario or during lane changes, but in all example videos/images we are just following
lane, so this is a valid method.
Using the absolute value condition we can filter out lines which doesnt belong to a lane marking, maybe using a range would provide 
better results. 
Instead of using the slope, we could also extrapolate the line to the bottom and using the x value to decide whether it belongs
to the left or right lane (left = 0- width/2), and we can use a range of x values to filter out non lane lines.
In this project, the slope approach is used.

![alt text][image6]





### 2. Identify potential shortcomings with your current pipeline

While using our pipeline on the challenge video, we observe some issues, where lanes are not recognized, we
extract one of those images and take a closer look:

![alt text][image7]

![alt text][image8]

Because of the lighting condition we can see that the left lane is barely visible in the grayscale image, therefore 
edges are not detected. On the right lane markings are missing partly or completely.

Another challenge are the shadows which edges are detected and sometimes match the criteria for a lane marking.

A general shortcoming that we can observe in all videos is the stuttering of the lane marking detection or lanes are not detected
in some frames.

Another possible shortcoming might happen during lane change, but i did not experiment with that

Last but not least i can imagine problem for lanes with high curvature which will either not generate lines or to many line with different slopes.
Also lane curvature is some information which are important for SDCs.

### 3. Suggest possible improvements to your pipeline

Besides parameter optimization i propose following improvements resulting from the shortcomings:
Instead using the grayscale image, we either process each channel separately and fuse them back together 
(that would resolve missing left lane of the previous image) or we convert the image to another color space, since RGB 
values differ under different lighting conditions and are thus not stable.

We can use a kalman filter for lane mark detection in videos, since lane markings can not change dramatically over the frames and we can receive a more 
stable detection.

We can use transform the images using camera parameters to get a view from the top find parallel lanes and maybe calculate lane curvature


