# CarND-01-Finding-Lane-Lines-on-the-Road
Udacity Self-Driving Car Engineer Nanodegree: Project 1 - Finding Lane Lines on the Road

## About

In this project, you will use the tools you learned about in the lesson to identify lane lines on the road. 

You can develop your pipeline on a series of individual images, and later apply the result to a video stream (really just a series of images).

The tools you have are:

- Color selection
- Region of interest selection
- Grayscaling
- Gaussian smoothing
- Canny Edge Detection
- Hough Tranform line detection

## Set up environment

- Follow the instructions in: [this README](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md).

- Learning more about [Anaconda and Jupyter Notebooks](https://classroom.udacity.com/courses/ud1111).

## Run the code

- Launch the Jupyter notebook with Anaconda or Docker.

```bash
# Anaconda
source activate carnd-term1 # If currently deactivated, i.e. start of a new terminal session
jupyter notebook finding_lane_lines.ipynb
```

## Details

`cv2.inRange()` for color selection
`cv2.fillPoly()` for regions selection
`cv2.line()` to draw lines on an image given endpoints
`cv2.addWeighted()` to coadd / overlay two images `cv2.cvtColor()` to grayscale or change color `cv2.imwrite()` to output images to file
`cv2.bitwise_and()` to apply a mask to an image

[ChenBohan/AI-Computer-Vision-01-Canny-to-Detect-Lane-Lines](https://github.com/ChenBohan/AI-Computer-Vision-01-Canny-to-Detect-Lane-Lines)
