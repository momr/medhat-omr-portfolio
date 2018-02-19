## Project: Vehicle Detection

The goals / steps of this project are the following:

*   Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
*   Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
*   Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
*   Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
*   Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
*   Estimate a bounding box for vehicles detected.

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.

---

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YUV` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and used them to train a simple classifer (a quick one to train) to get a feeling about how changing parameters change classifer performance. Then picked the combination with best performance and started to focus more on building a more robust classifier as described in the next section.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

The code for building my final classifer can be found under the "2.3 Combining Features" section of my notebook. I trained different classifers using the parameters I picked in the previous section. After normalizing my features and spliting up data into randomized training and test sets. I tested Linear SVM, Random Forest, Ada Boost, and a VotingClassifier consist of all the previously mentioned classifers. And found that Linear SVM is the best interms of Accuracy (99.2%) to Seconds to predict (4 ms) ratio, and that is why I choosed it to be used later on in my video pipeline code.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I used the same sliding window search demonstrated in the lesson videos, and below is the result of running the search with windows scale 1. (I used different scales for different regions of the frame later on as exaplained in following sections)

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on three scales using YUV 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4a]
![alt text][image4b]
![alt text][image4c]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.

Here's an example result showing the heatmap from a frame of video:

![alt text][image5]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.

In addition to the heatmap trick illutered in the last section. I also used some kind of a circular buffer (implemented using python list, but could also be implemented used dequeue from collections package for better performance) to store the result of the 5 most recent frames. This buffer is input to the function `draw_labeled_bboxes_list` which applies heatmap trick but for different purpose, I used it here to fill in the gap between frames where a car is suffesseffuly detected in one frame but not in a following one. I am still getting few gaps, but they are less than what I got before using this technique.

I noticed also if I increase the buffer length I will start get false positives (I don't know if I should call it false positives or not because it is still detections but for cars on the opposite lane (I don't know if it is random, or the classifier is able to detect a car from just its top portion appearing above the road separator/fence) as show in the following image).
![alt text][image4b]

I chose to keep the buffer short to avoid these false positives, also inaccurate box position (lagging a little bit behind the true position of the car (the lag is more clear when I use a longer buffer, which make sense, since I am looking more backword in time using a longer buffer)).

The pipeline will likely fail for detecting Trucks or motorbikes, if not because of the classifer, then this will be because of the window search algorithm assumes implecitly certain size of vehicles to search for.

Two things that I wanted to try but I hadn't enough time are: using a DNN instead of my current classifier. Second is using [YOLO](https://pjreddie.com/darknet/yolo/) :)


<!-- Links -->
[//]: # (Image References)
[image1]: ./assets/car_not_car.png
[image2]: ./assets/HOG_example.png
[image3]: ./assets/sliding_windows.png
[image4a]: ./assets/sliding_window1.png
[image4b]: ./assets/sliding_window3.png
[image4c]: ./assets/sliding_window2.png
[image5]: ./assets/bboxes_and_heat.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4
