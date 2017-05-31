# **Finding Lane Lines on the Road** 

## Writeup

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I applied a Gaussian blur with a kernel size of 5 to suppress noise. I then ran Canny Edge detection with a low and high threshold of 50 and 100 respectively. The approach I took was to tweak the values of the kernel size, high threshold and low threshold to minimize horizontal lines and any artifacts in the edge detection image. I tried various settings of thresholds ranging from (80,240) to (40,80) and found that (50,100) eventually worked best.

The next step was to isolate the lane using a Region of Interest and perform the Hough transform on the edge detection image to return the Hough lines drawn on the image. I found that a trade-off was required at this step to get good averaging when extrapolating the lines. In particular, I found that increasing the line length and gap parameters allows the Hough transform to model the small white stripes closer to the top of the image as a single line which helped with the extrapolation. The rho, theta, min line length, max line gap and threshold were all tweaked to achieve the final result. A slight modification of my ROI was also

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
