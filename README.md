
# **Finding Lane Lines on the Road** 
[//]: # (Image References)

[laneRois]: ./resources/LaneLinesROIs.jpg "Lane line ROIs"
[pipeline]: ./resources/pipeline.jpg "Pipeline"
[laneLineRoi]: ./resources/generalLaneLinePipeline.jpg "Lane Line Pipeline"
[getLaneState]: ./resources/getLaneState.jpg "getLaneState"
[src_solidWhiteCurve]: ./test_images/solidWhiteCurve.jpg "src_solidWhiteCurve"
[result_solidWhiteCurve]: ./test_images_output/solidWhiteCurve.jpg "result_solidWhiteCurve"
[src_solidWhiteRight]: ./test_images/solidWhiteRight.jpg "src_solidWhiteRight"
[result_solidWhiteRight]: ./test_images_output/solidWhiteRight.jpg "result_solidWhiteRight"
[src_solidYellowCurve]: ./test_images/solidYellowCurve.jpg "src_solidYellowCurve"
[result_solidYellowCurve]: ./test_images_output/solidYellowCurve.jpg "result_solidYellowCurve"
[src_solidYellowCurve2]: ./test_images/solidYellowCurve2.jpg "src_solidYellowCurve2"
[result_solidYellowCurve2]: ./test_images_output/solidYellowCurve2.jpg "result_solidYellowCurve2"
[src_solidYellowLeft]: ./test_images/solidYellowLeft.jpg "src_solidYellowLeft"
[result_solidYellowLeft]: ./test_images_output/solidYellowLeft.jpg "result_solidYellowLeft"
[src_whiteCarLaneSwitch]: ./test_images/whiteCarLaneSwitch.jpg "src_whiteCarLaneSwitch"
[result_whiteCarLaneSwitch]: ./test_images_output/whiteCarLaneSwitch.jpg "result_whiteCarLaneSwitch"

### Reflection

#### 1. Pipeline

While analyzing the problem I noticed that each lane line lays on each image half, so in order to avoid unnecessary noise I wen't for a approach in which the pipeline is executed once per each lane line (image half). So instead of defining ROI shaped as a truncated pyramid, I defined 2 ROIs, one each the half of a pyramid, just as can be seen here:

![Lane lines ROIs][laneRois]

I run the pipeline once for the area colored in blue and once for the area colored in purple.

Here can be seen the pipeline at a general level:

![Pipeline][pipeline]

* First of I take original image and convert it into grayscale. **(1)**
* Later I darken those areas in the image with a brightness below a threshold. **(2)**
* Following I apply Canny algorithm. **(3)**
* On this point I pass to the specific pipeline for each of the lane line ROIs.

---
Here can be seen the specific pipeline for each lane line ROI:

![Lane line ROI][laneLineRoi]

* First of I call getLaneState **(1)** (which I'll later explain what it does, but basically returns average segment):
  * If it returned **None**, then I jump after. **(3)**
  * Otherwise continue to the next step.
* I convert the segment into the slope notation of its support line. **(2)**
* Then I append it to a circular buffer (Python's **deque** with fixed size). **(3)**
* After that:
  * If buffer is empty return.
  * Otherwise continue to the next step.
* I obtain the weighted moving average of the lane line: **(4)**
  * As can be seen each line in the buffer has its weight. In this case the more recent the lane line detection is the higher the weight is.
* Lastly I draw the resulting line. **(5)**

---
Here can be seen a deeper view of getLaneState:

![getLaneState][getLaneState]

* First of I apply the lane line ROI. **(1)**
* In the two next steps **(2)**, **(3)** I apply **dilation** and **closing** morphological transformations using a **5x5 uniform kernel**.
  * **NOTE**. Take into account that the **2** superscript stand for 2 iteration of each morph transformation.
* After that I apply the Hough Transform: **(4)**
  * If it doesn't detect segments return **None**.
  * Otherwise continue to the next step.
  
* In the next step I average the points of all detect segments which absolute slope is in the accepted range. **(5)**
* After that: **(6)**
  * If there were segments left after filtering then and I have a valid average, return the points average.
  * Otherwise return **None**.

---
#### 2. Some possible issues

One problem this pipeline has is that is not reliable under poor illumination conditions.

---
#### 3. Further work

* Try a Kalman filter to model slope so as distance change.
* Get rid of the CV algorithm and use a proper DL model to limit the drivable areas for each lane.

---
### Results


#### Images
Original image             |  Result
:-------------------------:|:-------------------------:
![solidWhiteCurve.jpg][src_solidWhiteCurve]  |  ![Result of solidWhiteCurve.jpg][result_solidWhiteCurve]
![solidWhiteRight.jpg][src_solidWhiteRight]  |  ![Result of solidWhiteRight.jpg][result_solidWhiteRight]
![solidYellowCurve.jpg][src_solidYellowCurve]  |  ![Result of solidYellowCurve.jpg][result_solidYellowCurve]
![solidYellowCurve2.jpg][src_solidYellowCurve2]  |  ![Result of solidYellowCurve2.jpg][result_solidYellowCurve2]
![solidYellowLeft.jpg][src_solidYellowLeft]  |  ![Result of solidYellowLeft.jpg][result_solidYellowLeft]
![whiteCarLaneSwitch.jpg][src_whiteCarLaneSwitch]  |  ![Result of whiteCarLaneSwitch.jpg][result_whiteCarLaneSwitch]

---
#### Videos
Original video             |  Result
:-------------------------:|:-------------------------:
[solidWhiteRight.mp4](./test_videos/solidWhiteRight.mp4) |  [solidWhiteRight.mp4](./test_videos_output/solidWhiteRight.mp4)
[solidYellowLeft.mp4](./test_videos/solidYellowLeft.mp4) |  [solidYellowLeft.mp4](./test_videos_output/solidYellowLeft.mp4)
[challenge.mp4](./test_videos/challenge.mp4) |  [challenge.mp4](./test_videos_output/challenge.mp4)
