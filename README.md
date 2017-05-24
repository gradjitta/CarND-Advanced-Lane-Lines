# **Advanced Lane Finding Project**

[//]: # (Image References)

[image1]: ./example_images/chessboard_dist.png "Undistorted"
[image2]: ./example_images/dist_undist.png "Road Transformed"
[image3]: ./example_images/sliders1.png "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"
[image41]: ./example_images/img_lines.png
[image42]: ./example_images/warped_transform.png
[image51]: ./example_images/histogram.png
[image52]: ./example_images/lane_lines.png
[image61]: ./example_images/radius_dist.png
[video2]: ./project_video_lanes_grad1.mp4 "Video"

##### Here I will consider the [rubric](https://review.udacity.com/#!/rubrics/571/view) points individually and describe how I addressed each point in my implementation.  

---

### Code
* P4_Advanced_Lane_Finding.ipynb

### Camera Calibration

#### 1. Compute the camera matrix and distortion coefficients.


The code for this step is contained in the cells 2 and 3. Given a folder of distorted chessboard images we can loop through all the images and collect the image points. The object points are the same for every image. The main line, in cell 2, that extracts the image points from the gray scale image, is given below

``ret, corners = cv2.findChessboardCorners(gray, (9,6), None)``

Here the corners are the ``imgpoints`` of one distorted chessboard. These are appended into a list of ``imgpoints`` and finally we compute the distortion coefficients and camera calibration using

``ret, mtx, dist, rvecs, tvecs = cv2.calibrateCamera(objpoints, imgpoints, gray.shape[::-1], None, None) ``

as shown in cell 3. Here, mtx is camera matrix and dist is the distortion coefficients. Finally, distorted image can be undistorted using the following line.

``undist_calib = cv2.undistort(dist_calib, mtx, dist, None, mtx)``


![alt text][image1]

### Pipeline (single images)


#### 1. Provide an example of a distortion-corrected image.

The following image was specifically chosen as it has shadows, sparse white lines, and traffic. Making sure that, if my pipeline works on this image; it should for the entire road. I applied the distortion correction to this image.

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient(direction and magnitude) threshold. The choice of gradient parameters is done by interactive plot. These parameters are chosen with the help of sliders and visualizing the effect of various thresholds on the image. The code for this is in cell 5 and 7.




![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The following are the source and destination points I used to perform perspective transform


| Source        | Destination   |
|:-------------:|:-------------:|
| 203, 720      | 203, 720      |
| 585, 460      | 203, 0        |
| 695, 460      | 1000, 0       |
| 1127, 720     | 1000, 720     |

Original undistorted image

![alt text][image41]

Warped Image

![alt text][image42]

The code for this part can be found in cells 8 and 9

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Using the histogram of the binary warped image

![alt text][image51]

I constructed left and right lines using init_search function, contained in cell 6. Once we have the line information we can draw them on the warped images as follows

![alt text][image52]


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The function ``def radcurvature(leftline, rightline)``, in cell 6,  computes the radius and the deviation from center. The leftline and rightline objects of class Line store all x. Using these objects,  We fit the new polynomials in the world space and the coefficients from these fitted lines were used to calculate the radii of left and right lines. Further explanation is the code comments.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The result on the running test example is as follows

![alt text][image61]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_lanes_grad1.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I tried to make use of smoothing but currently I am failing to achieve it.
Some ways to improve my pipeline

* There are few frames where its a bit wobbly, implementing smoothing will work here along with windowed search to make it robust.

The pipeline might fail, in the following cases:

* Cracks on the roads in the form of lines can have a major setback to the pipeline

* Sparse road lines can cause wobbly results for radius calculations; since we are using less amount of data. We can overcome this by smoothing
