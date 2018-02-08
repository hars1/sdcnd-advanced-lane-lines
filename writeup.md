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

[image1]: ./output_images/undistorted_image.png "Undistorted"
[image2]: ./output_images/road_undistorted.png "Road Transformed"
[image3]: ./output_images/threshold.png "Binary Example"
[image4]: ./output_images/warped_transformed.png "Warp Example"
[image5]: ./output_images/polynomial_fit.png "Fit Visual"
[image6]: ./output_images/rad.png "Output"
[video1]: ./project_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "Advanced Lane lines notebook.ipynb".


I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
![alt text][image2]

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `Color/Gradient threshold` section in notebook).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The purpose of the conducting perspective transform is to obtain a bird’s‐eye view transform that let us view a lane from above; this will be useful for calculating the lane curvature later on. The code for my
perspective transform is includes a function called warp().The warp() function takes as inputs an image
(img) , source (src) and destination (dst) points as well as one flag. The flag sign was used to determine
the perspective transform matrix (M) calculation and the image size. I chose the source and destination
points as follows:
```python
src_pts = np.array([[690, 450], [1110,img.shape[0]‐2],[210,img.shape[0]‐2], [595, 450]], np.int32)
dst_pts = np.array([[1100,0], [1100,img.shape[0]‐2],[200,img.shape[0]‐2], [200, 0]], np.int32)
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 700,  450      | 1100, 0        |
| 1160,  718      | 1100, 718      |
| 200,  718     | 200, 718      |
| 595,  450     | 200, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

After applying calibration, binary thresholding, and a perspective transform to a road image, we
obtained a binary image where the lane lines stand out clearly. We still need to decide
explicitly which pixels are part of the lines and which belong to the left line and which belong to the
right line. Histogram is calculated for the lower half of the perspective
transformed image and then implemented the sliding windows technique to identify the lane line pixels, and then fitted the lines
with a polynomial

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The lane line radius curvature and the car offset was calculated as described in the `Lane curvature calculation` section in the notebook

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `Draw on a frame` section in the function `draw_lines()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.
This project is  more robust to identify the curved lane lines due to the second
polynomial fitting technique used. However,the created
pipeline doesn’t perform well when the road lightness varies violently. The issue is due to the fact that
the developed lane detection pipeline relies on color thresholding binary which is designed for yellow
and while color specifically. More robust color masks can be developed to handle the issue.
