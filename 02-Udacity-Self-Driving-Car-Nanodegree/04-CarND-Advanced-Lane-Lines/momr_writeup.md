
# Advanced Lane Finding Project

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./HTML/CarND-Advanced-Lane-Lines.html".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image (All threshold work/code can be found under the section titled "2. Image Threshold" in the notebook found under the HTML folder).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in section "3. Perspective Transformation: to rectify binary image ("birds-eye view")" (CarND-Advanced-Lane-Lines.html).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```
src_pts = np.float32(
    [[(img_size[0] / 2) - 62, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 18), img_size[1]],
    [(img_size[0] * 5 / 6) + 30, img_size[1]],
    [(img_size[0] / 2 + 60), img_size[1] / 2 + 100]])
dst_pts = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])

```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did lane identification, radius of curvature calculation, and offset from lane center calculation, and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

I did this in "4. Lane Detection" of the notebook (CarND-Advanced-Lane-Lines.html)

#### 5. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in section "6. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position." of the notebook (CarND-Advanced-Lane-Lines.html).  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result][video1]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.

The approach I followed was starting simple, then incremently improve my technique. Unfortunatly, I am feeling nervious about time to submit this project, and that's why I am submitting this simple (but hopefully satisfactory) implementation.

Here are some of the things I wanted to make to further improve the pipeline:
1. Study more color spaces and pick a better channel than S in HLS or L in LUV, or B in LAB. So that I can depend less on gradiants which cause troubles with different road conditions such as shadows in the example video.
2. Use exponential average to give most recent frames more wieght than older ones.
3. Implement a more robust/better sanity checks to reject unusable results and replace them with a result from prior frames. Here I used only change in lane width (called delta_lane_width), I did this only at the end of the lane (I could try to repeat that for the middle and the beginning of the lane). I could also use redius of curvature (I avodied it only because of straight lines case, but I think I could do better at least for testing lines parallelism)




<!-- Links -->
[image1]: ./assets/undistort_output.png "Undistorted"
[image2]: ./assets/undistorted_road.png "Road Transformed"
[image3]: ./assets/binary_combo_example.png "Binary Example"
[image4]: ./assets/warped_straight_lines.png "Warp Example"
[image5]: ./assets/color_fit_lines.png "Fit Visual"
[image6]: ./assets/example_output.png "Output"
[video1]: ./project_ouput.mp4 "Video"
