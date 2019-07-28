# AI-Computer-Vision-01-Canny-to-Detect-Lane-Lines

1. Color Selection
2. Region Selection
3. Canny to Detect Lane Lines
    1. Canny
    2. Gaussian smoothing
4. Hough Transform

## Color Selection

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/test.jpg" width = "60%" height = "60%" div align=center />

1. Define a color threshold in the variables ``red_threshold``, ``green_threshold``, and ``blue_threshold``.
2. Select any pixels below the threshold and set them to zero.

After that, all pixels that meet my color criterion (those above the threshold) will be retained, and those that do not (below the threshold) will be blacked out.

```python
# Define color selection criteria
red_threshold = 200
green_threshold = 200
blue_threshold = 200
rgb_threshold = [red_threshold, green_threshold, blue_threshold]

# Do a boolean or with the "|" character to identify
# pixels below the thresholds
thresholds = (image[:,:,0] < rgb_threshold[0]) \
            | (image[:,:,1] < rgb_threshold[1]) \
            | (image[:,:,2] < rgb_threshold[2])
color_select[thresholds] = [0,0,0]
```

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/test-after.jpg" width = "60%" height = "60%" div align=center />

## Region Selection

Assume that the front facing camera that took the image is mounted in a fixed position on the car, such that the lane lines will always appear in the same general region of the image.

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/readme.img/Region%20Masking.png" width = "60%" height = "60%" div align=center />

The variables ``left_bottom``, ``right_bottom``, and ``apex`` represent the vertices of a triangular region that I would like to retain for my color selection, while masking everything else out. 

```python
# Define a triangle region of interest (Note: if you run this code, 
# Keep in mind the origin (x=0, y=0) is in the upper left in image processing
left_bottom = [0, 717]
right_bottom = [1276, 717]
apex = [650, 440]

fit_left = np.polyfit((left_bottom[0], apex[0]), (left_bottom[1], apex[1]), 1)
fit_right = np.polyfit((right_bottom[0], apex[0]), (right_bottom[1], apex[1]), 1)
fit_bottom = np.polyfit((left_bottom[0], right_bottom[0]), (left_bottom[1], right_bottom[1]), 1)

# Mask pixels below the threshold
color_thresholds = (image[:,:,0] < rgb_threshold[0]) | \
                    (image[:,:,1] < rgb_threshold[1]) | \
                    (image[:,:,2] < rgb_threshold[2])

# Find the region inside the lines
XX, YY = np.meshgrid(np.arange(0, xsize), np.arange(0, ysize))
region_thresholds = (YY > (XX*fit_left[0] + fit_left[1])) & \
                    (YY > (XX*fit_right[0] + fit_right[1])) & \
                    (YY < (XX*fit_bottom[0] + fit_bottom[1]))
```

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/test-line-image.jpg" width = "60%" height = "60%" div align=center />

## Canny to Detect Lane Lines

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/exit-ramp.jpg" width = "60%" height = "60%" div align=center />

1. Convert the image to grayscale.

```python
# Grayscale conversion
gray = cv2.cvtColor(image,cv2.COLOR_RGB2GRAY)
```

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/readme.img/Canny%20Edge%20Detection2.png" width = "60%" height = "60%" div align=center />

2. Apply ``Canny`` to the gray image.

```python
# Define our parameters for Canny and run it
low_threshold = 100
high_threshold = 200
edges = cv2.Canny(blur_gray, low_threshold, high_threshold)
```

**Canny**

1. Detect strong edge (strong gradient) pixels above the ``high_threshold``, and reject pixels below the ``low_threshold``. 

2. Pixels with values between the ``low_threshold`` and ``high_threshold`` will be included as long as they are connected to strong edges.

3. The output edges is a binary image with white pixels tracing out the detected edges and black everywhere else. 

Hints:

- In our case, converting to grayscale has left us with an 8-bit image, so each pixel can take 2^8 = 256 possible values. Hence, the pixel values range from 0 to 255. 

- This range implies that derivatives (essentially, the value differences from pixel to pixel) will be on the scale of tens or hundreds.

- As far as a ratio of ``low_threshold`` to ``high_threshold``, John Canny himself recommended a low to high ratio of 1:2 or 1:3.

Ref: [OpenCV Canny Docs](http://docs.opencv.org/2.4/doc/tutorials/imgproc/imgtrans/canny_detector/canny_detector.html)

**Gaussian smoothing**

- Before running Canny, which is essentially a way of suppressing noise and spurious gradients by averaging.

- ``cv2.Canny()`` actually applies Gaussian smoothing internally, but we include it here because you can get a different result by applying further smoothing (and it's not a changeable parameter within ``cv2.Canny()``!). 

- You can choose the ``kernel_size`` for Gaussian smoothing to be any odd number. A larger kernel_size implies averaging, or smoothing, over a larger area.

```python
# Define a kernel size for Gaussian smoothing / blurring
# Note: this step is optional as cv2.Canny() applies a 5x5 Gaussian internally
kernel_size = 3 # Must be an odd number (3, 5, 7...)
blur_gray = cv2.GaussianBlur(gray,(kernel_size, kernel_size),0)
```

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/readme.img/Canny%20Edge%20Detection1.png" width = "60%" height = "60%" div align=center />

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/edges.jpg" width = "60%" height = "60%" div align=center />

## Hough Transform

- The Hough transform is a conversion from image space to Hough space.

- In Hough space, I can represent my "x vs. y" line as a point in "m vs. b" instead.

- So the characterization of a line in image space will be a single point at the position m-b in Hough space.

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/readme.img/Hough%20Transform1.png" width = "60%" height = "60%" div align=center />

So our strategy to find lines in image space will be **look for intersecting lines in Hough space**.

1. Run the canny edge detection algorithm to find all points associated with edges.

    1. Consider every point as a line in Hough space.
    
2. Found a collection of points that describe a line in image space where many lines in Hough space intersect, 

    1. We have a problem
    
        1. vertical lines have infinite slope in m-b representation
        
    2. Redefine our line in polar coordinates
    
        1. ρ - the perpendicular distance of the line from the origin
        
        2. θ - the angle of the line away from horizontal
        
    3. Now each point in image space corresponds to a sine curve in Hough space.
        
        1. the intersection of those sine curves in θ-ρ space gives the parameterization of the line.
        
Ref: [Hough transform(霍夫变换)](https://www.cnblogs.com/AndyJee/p/3805594.html)

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/readme.img/Hough%20Transform2.png" width = "60%" height = "60%" div align=center />

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/readme.img/Hough%20Transform3.png" width = "60%" height = "60%" div align=center />


To do this, we'll be using an OpenCV function called ``HoughLinesP``

Ref: [Understanding Hough Transform With Python](https://alyssaq.github.io/2014/understanding-hough-transform/)

```python
# Define the Hough transform parameters
# Make a blank the same size as our image to draw on
rho = 2                 # distance resolution in pixels of the Hough grid
theta = np.pi/180       # angular resolution in radians of the Hough grid
threshold = 15          # minimum number of votes (intersections in Hough grid cell)
min_line_length = 40    # minimum number of pixels making up a line
max_line_gap = 20       # maximum gap in pixels between connectable line segments
line_image = np.copy(image)*0   # creating a blank to draw lines on

# Run Hough on edge detected image
lines = cv2.HoughLinesP(masked_edges, rho, theta, threshold, np.array([]),
                            min_line_length, max_line_gap)
```

- `masked_edges` - the output from Canny
- `rho`, `theta` - the distance and angular resolution of our grid in Hough space
    - `rho` takes a minimum value of 1, and a reasonable starting place for `theta` is 1 degree (pi/180 in radians). 
    - Scale these values up to be more flexible in your definition of what constitutes a line. 
- `threshold` - the minimum number of votes (intersections in a given grid cell) a candidate line needs to have
- `np.array([])` - a placeholder, no need to change it.
- `min_line_length` - the minimum length of a line (in pixels)
- `max_line_gap` - the maximum distance (in pixels) between segments
- `lines` - an array containing the endpoints (x1, y1, x2, y2) of all line segments

<img src="https://github.com/ChenBohan/AI-CV-01-Canny-to-Detect-Lane-Lines/blob/master/lines_edges.jpg" width = "60%" height = "60%" div align=center />
