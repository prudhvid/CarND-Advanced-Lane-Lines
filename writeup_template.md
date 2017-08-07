## **Advanced Lane Finding Project**

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

[image1]: ./images/distorted.png "Undistorted"
[image2]: ./images/distorted2.png "Road Transformed"
[image3]: ./images/test.png "Test Image"
[image4]: ./images/test_result.png "Thresolding Results"
[image5]: ./images/test_result2.png "Thresolding Results"
[image6]: ./images/test_result3.png "Thresolding Results"
[image7]: ./images/test_result4.png "Thresolding Results"

[image8]: ./images/persp.png "Perspective Transform"
[image12]: ./images/final.png "Final Transformation"

[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./Solution.ipynb". The code for this is mostly taken from the lecture videos

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. I did the following things:
1. Applied sobel thresold in both x(40,250) and y(50, 250) directions independently, after converting from RGB to Gray
2. Sobel Threshold for magnitude(50,250) of x and y 
3. Direction threshold(0.6, 1.2)
4. Color thresholding for S-channel in HLS space. (160, 255)
5. Combine the thresholds and get the final image

![alt text][image3]
![alt text][image4]
![alt text][image5]
![alt text][image6]

**Final Output of all the combined images**

![alt text][image7]


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `undistort_image()`, in the 10th code cell of the IPython notebook.  The function takes as inputs an image (`img`). I chose the hardcode the source and destination points in the following manner:

```python
src = np.array([[582, 455], [700, 455], [1150, 720], [150, 720]], dtype = np.float32)
dst = np.array([[300, 0], [1000, 0], [1000, 720], [300, 720]], dtype = np.float32)
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 455      | 300, 0        | 
| 150, 720      | 300, 720      |
| 1150, 720     | 1000, 720      |
| 700, 455      | 1000, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image8]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

For this, I've done very similar to what is said in the video lectures. After thresholding, I found the peaks in the histogram. And then used sliding window technique to identify all the rectangles nearby the histogram. After this, I tried to fit them using a second order polynomial. Whole of this can be seen in process_image function of code cell 9

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

To calcuate radii of curvature for both the lines, I used the equation of the radius of curvature mentioned in the video lectures. The code for this can be found in code cell 20(line 95-110). For the position of vehicle, I have taken help from the code of [Upul]( https://github.com/upul/CarND-Advanced-Lane-Lines). (115 - 120)

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines 125-140 and is similar to what is told in video lectures. Here is the example output for that.

![alt text][image12]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](https://youtu.be/tkZsqcNjirQ)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My implementation didn't work well on challenege videos. I didn't make use of history and I think I should do that. It would fail if the s_thresholding fails in any case. To make it more robust, I think I should check for other thresholdings in varying conditions.
