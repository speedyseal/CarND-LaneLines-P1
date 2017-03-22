#**Finding Lane Lines on the Road** 

##Writeup Template

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[gray_solidYellowCurve]: ./test_images_output/gray_solidYellowCurve.jpg "After color select and grayscaling"
[edge_solidYellowCurve]: ./test_images_output/edge_solidYellowCurve.jpg "After edge detection"
[lines_solidYellowCurve]: ./test_images_output/lines_solidYellowCurve.jpg "Annotated with ROI, hough lines, and extrapolated lanes"
---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 
1. Select parts of image corresponding to white and yellow lane lines. I used RGB ranges [150, 150, 150] to [255, 255, 255] for white and [150, 150, 0] to [255, 255, 150] for yellow.
2. Convert to grayscale
3. Canny edge detection with a Gaussian blur prefilter of size 5.
4. Mask out the trapezoidal ROI
5. Hough transform to detect lines.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by
Selecting lines by slope. Lines with a slope > 0.5 and < 1.0 are right lane markers. Lines with slope < -0.5 and > -1.0 are left lane markers
Then I do a weighted average of the left slopes and x-intercepts and right slope and x-intercepts, weighting by the length of each segment. The idea is that due to parallax, the closer lines will have greater length and provide more accurate slope/intercept measurements.

![alt text][gray_solidYellowCurve]
![alt text][edge_solidYellowCurve]
![alt text][lines_solidYellowCurve]


###2. Identify potential shortcomings with your current pipeline

Vehicles with features that fall within the color mask - such as the C-pillars of a white or yellow car - within the ROI may be detected as extraneous lines, corrupting the lane detection.

The pipeline only detects a straight line lane. A wavy lane, or a curved lane will pose problems.

The pipeline selects lanes within a certain slope range. Changing lanes, or turning into a lane will confuse the lane detection.

The challenge file shows deficiencies in the color selection. An absolute color index may not be the best way to detect lane lines. Lane markings may vary in shades of white. Pavement can also vary in shades of white, for example concrete sections. There are sections of 280 where pavement is part white concrete and part black asphalt, and the transistion crosses lane lines.

###3. Suggest possible improvements to your pipeline

Smooth lane detection across frames.

Weight towards lane lines closer to the bottom of the image, which show up with greater resolution that lane lines towards the horizon.

Detect expected line patterns, such as dashed lines going towards the horizon, or continuous lines.

Detect expected curves in addition to straight lines, possibly by fitting to splines of a certain order.

Variable horizon detector for setting ROI boundary - the horizon may vary depending on the slope of the road. The ROI should adapt to the changing field of view.

Adaptive color selection. Calibrate the lane line colors based on what's in the image and corresponding contrast with lane line context.