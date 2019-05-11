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

[image1]: output_images/OrigionalVsUndistortedImage.png "Undistorted"
[image2]: output_images/OrigionalVsUndistortedImage_road.png "Road Transformed"
[image3]: output_images/CombinedSobels.png "Binary Example"
[image4]: output_images/SourceAndWarpedImage.png "Warp Example"
[image5]: output_images/WarpedBinaryWithFit.png "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

All code for this project is located in the IPython notebook titled "P2.ipynb" located in base directory of the CarND-Advanced-Lane-Lines repository. 

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.
The code for this step is contained in the code cells of the IPython notebook under the heading "Camera Calibration". 

The camera is calibrated by analyzing 20 unique pictures taken of a chess board. I loop through each picture and do the following steps:

a) Store an array of "object points" which are the (x,y,z) coordinates of each internal intersections of the chess board. In this project, the chessboard had 9x6 intersections.

b) Store an array of "image points" which are the actaul pixel coordinates of each intersecting grid in the chessboard. 

c) I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Example of a distortion-corrected image.

The first step of my pipeline is to apply distortion correct to the image. I use the calibration and distortion coefficients found earlier from the chessboard calibration. Below is an example: 
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in "Helper Functions" section titled "Helper functions for color and gradient thresholds") [lines 6-59]. These functions are called in the section titled "Color Transform with Thresholding and Gradients" [line 21]. 

I used the following steps to create the binary image:

a) Convert the image to RGB and HLS color space.

b) I then individually thesholded the "S" (saturation), "L" (light), and "R" (red) color channels. I took the sobel gradient in the x direction for each of those.

c) I "And"ed the "S" and "R" channels and "Or"ed that with the "L" channel to create the output binary image.

Here's an example of my output for this step. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in lines 64 through 92 in the "Helper Functions" section.  The `warp()` function takes as inputs an image (`img`), as well as source (`src`) points. I chose the hardcode the source and destination points in the following manner:


| Source        | Destination   | 
|:-------------:|:-------------:| 
| 682, 445      | 900, 0        | 
| 1054, 677     | 900, 720      |
| 250, 680      | 250, 720      |
| 598, 445      | 250, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

After I warped the image to get a bird's eye view, I ran the image through my gradient and color threshold functions to get a binary image. Then I did the following steps to fit a second order polynomial (see section titled "Detect Lane Pixels to find Lane Boundary"):

a) Took a histogram of the bottom half of the image and used the peaks to separate the base of the left and right lanes.

b) Implemented 9 sliding windows to identify the pixels making up  the left and right lines.

c) Fit a secondary order polynomial to the pixels making up the left and right lanes.

Below is an example of the second order line fits:


![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I calculated teh radius of curvature in lines 364 through 367 in the "Helper functions" section. To calculate I used the equation:

$$E=mc^2$$



#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
