# **Finding Lane Lines on the Road** 

## Writeup
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ROI.jpg "Region of Interest"
[image2]: HoughLines.jpg "Hough Lines on Canny edge image"
[image3]: solidWhiteCurve.jpg "Extrapolated lines"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I applied a Gaussian blur with a kernel size of 5 to suppress noise. I then ran Canny Edge detection with a low and high threshold of 50 and 100 respectively. The approach I took was to tweak the values of the kernel size, high threshold and low threshold to minimize horizontal lines and any artifacts in the edge detection image. I tried various settings of thresholds ranging from (80,240) to (40,80) and found that (50,100) eventually worked best.

The next step was to isolate the lane using a Region of Interest and perform the Hough transform on the edge detection image to return the Hough lines drawn on the image. I found that a trade-off was required at this step to get good averaging when extrapolating the lines. In particular, I found that increasing the line length and gap parameters allows the Hough transform to model the small white stripes closer to the top of the image as a single line which helped with the extrapolation. The rho, theta, min line length, max line gap and threshold were all tweaked to achieve the final result. The images below shows my choice for a region of interest: 

![alt text][image1]

This image shows the masked Hough lines drawn on the complete edge image returned by the Canny edge detection algorithm. Note the small white stripes near the top of the ROI are drawn as connected due to the choice of max_line_gap.

![alt text][image2]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by first calculating the gradient of every line returned by the hough_lines() function and the mid point of the two coordinates associated with the end points of the line. The gradients and mid-points for the positive and negative lines were subject to several tests prior to being segregated into separate arrays based on the the polarity of their slope. The tests were:
  1. Bounds on the magnitude of the gradient to drop any steep lines that may be in the ROI. The bounds were determined by calculating the gradients for all the lines on certain images and observing the spread.
  2. Lines of zero gradient (horizontal lines) were dropped.
  3. The location of the line w.r.t the ROI. Positive slope lines were expected in the R.H.S. of the ROI and negative slope lines in the L.H.S.

Once the gradients and midpoints were separated, an average gradient and midpoint was computed on the resulting arrays to provide the m- value and an (x,y) co-ordinate so that the intercept, b, could be calculated as per y = mx + b. Now, with a known (m,b) for both the positive and negative lines, the line end points were calculated using the top of the ROI and bottom of the image as end-points. The resulting lines were then drawn on the image. 

![alt text][image3]

### 2. Identify potential shortcomings with your current pipeline
One limitation of this pipeline is the simple pre-processing of the image (conversion to grayscale) prior to running the edge detection algorithm. This approach doesn't filter out artifacts in the roadway that may appear in the region of interest as seen in the challenge video.

The current pipeline is based on a crude averaging of the hough transform lines to extrapolate the lane lines. This approach is limited in its effectiveness and fails if the lane lines are curved since a linear line is fit. 

Another limitation of the current method is the fixed means used to discard "outlier" lines that may skew the averages. The bounds are based on estimations and will not reflect all scenarios. This was also clearly visible in the challenge video.

Finally, the extrapolated lines are calculated on a frame-by-frame basis which results in jitter in the lines when the video is played. This could be mitigated by using a moving average across the frames, which at this point, is well beyond my skill level.

### 3. Suggest possible improvements to your pipeline

A first improvement would be to convert the image to an HSV colormap and extract the yellow and white line information. A composite image with just the lane lines would be a much better candidate for edge detection and hough line calculation. I attempted an HSV transform and had limited success with extracting the yellow stripe. The white stripe, however, eluded me for now.

Another potential improvement could be to remove outlier lines by calculating how far the gradient is from the mean gradient. A first pass on the hough transform lines would calculate the complete mean gradient and std dev. A second pass could then be used to filter out the lines where the gradient is 2 or 3 std devs away. The resulting mid-points could then be fit using a linear regression model instead of averaging the end-points and gradients. This could be taken one step further to do a second order polynomial fit which could fit curved lane lines as well. Both the standard deviation and linear regression approaches were unsuccessfully tested in this project. The standard deviation filter resulted in empty arrays on certain video frames which crashed the pipeline. I wasn't able to implement linear regression in a stable way (the linear regression fit would jump wildly at certain points) which I suspect was due to my choice of Hough parameter thresholds. I believe a smaller line length would have increased the population of lines allowing for a better fit. This approach, however, could not be fully investigated.

