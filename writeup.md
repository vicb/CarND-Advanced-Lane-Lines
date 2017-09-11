## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

[calibration]: ./output_images/test1_undistorted.jpg "Undistorted"
[sampleinput]: ./test_images/test1.jpg "Road Transformed"
[treshold]: ./output_images/test1_threshold.jpg "Threshold"
[warp1]: ./output_images/straight_lines1_warp.jpg "Warp"
[warp2]: ./output_images/test3.jpg "Warp"
[poly_fit]: ./output_images/poly_fit.png "Fit a polynomial"
[output]: ./output_images/test1_full_pipeline.jpg "Test on a single image"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

You're reading it!

The project is implemented in the Jupyter Notebook "Advanced Lane Finding.ipynb"

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code can be found in the first section of the notebook.
 
I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][calibration]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][sampleinput]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. 

- The image is converted to an HSL color space,
- The Sobel algorithm is used on the L channel to find the ~vertical lines,
- A single value threshold is applied on the S channel,
- The output is the combination of the 2 previous steps.

Here's an example of my output for this step.

![alt text][threshold]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

To implement the perspective transform, I have first found a source area in a sample image.
It took me a few iteration to fine tune the shape of the input area.

I have chosen an arbitrary rectangular area for the projected destination.

This resulted in the following source and destination points:

```python
src = [[  235.,   680.],
       [  562.,   470.],
       [  728.,   470.],
       [ 1055.,   680.],]

dst = [[  235.,   680.],
       [  235.,     0.],
       [ 1055.,     0.],
       [ 1055.,   680.],]
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][warp1]
![alt text][warp2]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I have used the sliding window alogorithm described in the course to identify the lane pixels and then fit
their positions with a polynomial.

![alt text][poly_fit]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I have used the function given in the course to compute the radius of curvature.

It's important to remember to convert pixels to meters before computing the radius.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Here is an example of my result on a test image:

![alt text][output]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_images/project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline implemented in the attached notebook works pretty well on the test video.
There are only a few images where the lanes are not correctly identified.

However there is a lot of room for improvement.

We could use the fact the position of the lanes is approximetely the same on two consecutive frames:
- It could speed up finding the lanes,
- It could help remove the outliers (ie when positions or radius are too different)

