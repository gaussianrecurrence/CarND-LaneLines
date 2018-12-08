
# **Finding Lane Lines on the Road** 
**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[laneRois]: ./resources/LaneLinesROIs.jpg "Lane line ROIs"

---

### Reflection

### 1. Here is how my pipeline work

While analyzing the problem I noticed that each lane line lays on each image half, so in order to avoid unnecessary noise I wen't for a approach in which the pipeline is executed once per each lane line (image half). So instead of defining ROI shaped as a truncated pyramid, I defined 2 ROIs, one each the half of a pyramid, just as can be seen here:

![Lane lines ROIs][laneRois]

I run the pipeline once for the area colored in blue and once for the area colored in purple.

If you'd like to include images to show how the pipeline works, here is how to include an image: 



### 2. Some possible issues


One problem this pipeline has is that is not reliable under poor illumination conditions.

Another shortcoming could be ...


### 3. Further work

Get rid of the CV algorithm and use a proper DL model to limit the drivable areas for each lane
