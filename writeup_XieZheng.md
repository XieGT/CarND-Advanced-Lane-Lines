
## Advanced Lane Detected Project

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

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/dis2un_test6.png "Road Transformed"
[image3]: ./output_images/test6_thresh.png "Binary Example"
[image4]: ./output_images/test6_birdview.png "Warp Example"
[image5]: ./output_images/test6_poly.png "Fit Visual"
[image6]: ./output_images/test3_lane.png "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first part of the IPython notebook named `P2.ipynb`.

1. Define a function named `find_points`. With all chess board images in folder `camera_cal`, find the 'objpoints' which are the invarient points and  'imgpoints' which are the distorted image points. 
2. Use the 'objpoints' and 'imgpoints' and apply `cv2.undistort` to a distorted image and get an undistorted image. Features are encapsulated in `cal_undistort`

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
1. Read in all test images located in test_images folder using `glob.glob('./test_images/*.jpg)`
2. Use for loop to traversal every test images and apply `cal_undistort` to get undistorted images. At last, append all undistorted test images in test_images.

An example of distorted VS. undistorted of `test6.jpg` is shown below
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
1. In cell **3.1 Gradient Thresh**, following gradient threshold features have been considered
	* absolute sobel value: `abs_sobel_thresh`
	* magnitude and dirction sobel: `mag_sobel_thresh`, `dir_sobel_thresh`
2. In cell **3.2 Color Thresh**, both hls and lab color spaces has been taken into account to distinguish the yellow line and white line from the road. Those features are defined as
	* hls thresholds: `h_hls_thresh`, `l_hls_thresh`, `s_hls_thresh`.
	* lab thresholds: `labBSelect`.

A figure with all different thresholds for `test6.jpg` is given 

![alt text][image3]
From figure shown above a combined threshold is chosen and defined as `combined_thresh`.
#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Choose the algorithm mentioned in class and listed as below 

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

Get the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580, 460      | 300, 0        | 
| 700, 460      | 950, 0        |
| 1096, 720     | 950, 720      |
| 200, 720      | 300, 720      |

In **4.Perspective Transformation** cell, with src and dst, define `transform_perspective_top` and `transform_top_perspective`. An example of `test6.jpg` is given as

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Follow the **sliding window** techinques, I identified leftline points and rightline points and fit them into a 2nd order polynoimal(see `find_lane_pixels`, `init_fit_poly`). Then using those parabolic information, define `search_around_poly` which search line pixels near the previous lines.

Example of `test6.jpg` is shown as,

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

In cell **6. Determine the lane curvature**, `cal_fit_meter` and `measure_curvature_real`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

In cell **7. Draw line on image**, `Draw Lane on Image`, example of test3.jpg is illustrated

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_detected.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

How to detect yellow line at the 23 sec of the video really give me a hard time when I only use gradient and hls threshold. Finally, lab thresh help me solve this issue.
