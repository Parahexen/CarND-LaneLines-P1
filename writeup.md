# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[original]: ./writeup/original.jpg "Original Image"
[grayscale]: ./writeup/grayscale.jpg "Grayscale Image"
[blur]: ./writeup/blur.jpg "Gaussian Blur"
[canny]: ./writeup/canny.jpg "Canny Edge Detection"
[prune]: ./writeup/prune.jpg "Prune Uninterested"
[hough]: ./writeup/hough.jpg "Hough Line Transform"
[lanes]: ./writeup/lanes.jpg "Draw Lanes"
[semi-transparent]: ./writeup/semi-transparent.jpg "Semi-transparent"
[final]: ./writeup/final.jpg "Final Output"


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

  1. Convert RGB/RGBA image to grayscale.

  ![original]
  ![grayscale]
    
  2. Apply gaussian blur with kernel size of **5**, reasonable size between 3 to 7, not strictly.

  ![blur]
  
  3. Apply canny edge detection, here we drop pixels which brightness is below **75**, and make the brightness of **225** as a edge seperator. Of course (50, 150) will do the job also, here I just planned to eliminate some luminate noises by rising up the threshold a little bit, and keep the canny's suggestion of the low/high ratio to **1:3** as the same.

  ![canny]
  
  4. Define a trapezium area that major lanes located in, for better lane shifting tolerance, I left the bottom boundary as it is. Then prune unnecessary area by apply this trapezium to canny output, using a bitwise **AND** operation.

  ![prune]
  
  5. Apply hough line transform, I kept the parameters as in course, because I don't think the major approvement will be started at here.

  ![hough]
  
  6. draw_lines(): the original function was to simply draw every hough lines up, including noises, and it's discrete of course. So I made few modifications here:
    1. Do not draw every lines, instead, try to draw single lane line for each side. For this idea, we need to get the parameters of the equation for two lines.
    2. At first, I tried to use slope-intercept formula, and I encountered vertical/horizontal slope problem, OK, let's filter them.
    3. During the time, I also tried to use sloar coordination formula to better adapt to slope problem, but there must be some coordination converting problem due to the difference origin point, and I gave up.
    4. Then, I tried to accumulate every valid slope and intercept for both sides, and get the mean/average value, to reform lanes. Pretty well on videos except challenge one actually.
    5. During the experiments, I was inspired by [here](https://github.com/naokishibuya/car-finding-lane-lines) for the length/weight solution. I have other thoughts but not this one.
    6. Most of my solution are based on statastics, although actually I know little about it. I tried to examine the normal distribution on slope, intercept, ratio of slope/intercept etc. And I found out this gonna be like some video processing methods of multiple pass. I did not accomplish it automatically, but merely filter more noise by manually do the first pass to get the mean/average and deviation scope.
    7. Finish the lane line and limit the boundary to interested part.
      
  ![lanes]
  
  7. Make the drawn lane semi-transparent.

  ![semi-transparent]
  
  8. Fianlly add a boundary frame to test boundary settings.

  ![final]


### 2. Identify potential shortcomings with your current pipeline

There are few unrevealed shortcomings:

1. How to dynamically set the top boundary?
2. Like above, how to dynamically prune more unrelated/uninterested area around other boundaries?
3. How to make the lane trace far-end curves or potential near-end curves instead of simple straight line?
4. How to make it robust to different illumination/weather?
5. Maybe deep learning will exceed traditional CV algorithms in some points? Of course not all traditionals, they are good enough to some specific problems, in speed, computational resources, and just do the job well.

### 3. Suggest possible improvements to your pipeline

There are two major improvements could be implemented:

1. Color space: simple grayscale is good on white lane but not yellow one, and it's not proof to luminate shadows, other bright cars/objects and so on. There are some improvements by using HSL/HSV color space [here](https://github.com/naokishibuya/car-finding-lane-lines).

2. I think it's possible to using some unsupervised clustering algorithm to find the slope/intercept for major focused lanes on each side, thus making the multi-pass automatically and better to tolerate more situations.
