# **Finding Lane Lines on the Road** 

## Writeup 

### This writeup contains the approach to thre project,  and my understanding of key concepts in the program



**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* A pipeline that finds lane lines on the road
* More details on new draw_lines() function
* Some reflections




### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps. 
1. Converted the images to grayscale
2. Applied a Gaussian Noise kernel to the grayscale to get gaussian_blur image
3. Applied the canny transform to the gaussian_blur to get an image named edges 
4. Made a region of interest to filter the region which I wanted to process
5. Got the endpoints of line segements from the region of interest in the edges with the function cv2.HoughTransformP() from Opencv
6. Drew a sigle line on the left and right lanes in the image whcih needed to be processed

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by four steps:
1. Remove the slopes calculated by the endpoints which are zero nearly, because this kind of slope will make the drawing lines appear and disappear alternately
2. Create a line_check() function to remove the slopes which are far away from the mean of all the slops by setting a k_diff_threshold, because this kind of slope will make the drawing lines far away from the lane lines
3. Create a line_fit() function to find the endpoints which are used to draw lines with respect for the filted endpoints, this created function consists of two important functions, numpy.polyfit() and numpy.poly1d()
4. Draw left and right lane lines with the function cv2.line() from Opencv

Images to show how the pipeline works are following:


grayscale image            |  grayscale blur image
:-------------------------:|:-------------------------:
<img src="https://user-images.githubusercontent.com/44608355/59586241-07a36400-9115-11e9-9a5f-44ac5d30159a.jpg" width="380"> | <img src="https://user-images.githubusercontent.com/44608355/59586240-070acd80-9115-11e9-8c7d-21ffa62d39ca.jpg" width="380">

esges                      |  masked image
:-------------------------:|:-------------------------:
<img src="https://user-images.githubusercontent.com/44608355/59586239-06723700-9115-11e9-81f8-fa48c7fd49a8.jpg" width="380">  |  <img src="https://user-images.githubusercontent.com/44608355/59586238-06723700-9115-11e9-91f5-72f6f3048d1d.jpg" width="380">

line image                 |  weighted image
:-------------------------:|:-------------------------:
<img src="https://user-images.githubusercontent.com/44608355/59586237-05d9a080-9115-11e9-93d1-998fdbbcd268.jpg" width="380">  |  <img src="https://user-images.githubusercontent.com/44608355/59586235-05d9a080-9115-11e9-933d-60c97b670d6b.jpg" width="380">

 
 
### 2. Identify potential shortcomings with your current pipeline
One potential shortcoming in the Optional Challenge would be what would happen when the lane line under the shadow of tree undetection of lane lines


### 3. Suggest possible improvements to your pipeline
A possible improvement would be to change the order of channels of the images from the video
