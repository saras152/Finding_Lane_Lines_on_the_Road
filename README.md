## **Attempt to Find Lane Lines on the Road by RaghuNath (RaNa)** 





---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[original]: ./process_images/original.png "Original"
[gray]: ./process_images/gray.png "Gray"
[gauss]: ./process_images/gaussfiltered.png "Gauss output"
[canny]: ./process_images/edgedetected.png "Canny output"
[mask]: ./process_images/maskonoriginal.png "Mask"
[maskedcanny]: ./process_images/maskoncanny.png "Mask on Canny filtered"
[hough]: ./process_images/houghoutput.png "Hough output"
[result]: ./process_images/result.png "Result"
[horizontallines]: ./process_images/hlines.png "Horizontal lines"
[shade_original]: ./process_images/shade_original.png "Shaded Road"

[shadedlines]: ./process_images/shade_lines.png "Lines with shade"

[nolinesright_original]: ./process_images/nolinesright_original.png "No strong lines on the right"

[nolinesright]: ./process_images/nolinesright.png "No detected lines on the right"

[brightroad]: ./process_images/brightroad_original.png "Road texture changed to bright color"
[zigzaglines]: ./process_images/zigzaglines.png "Resulting zigzag lines"

---

### Reflection

### 1. Description of my pipeline

The pipeline is implemented in the following sequence of steps:
1. The original RGB image is converted to gray scale image
2. The gray scale image is blurred to remove unwanted spikes in thresholds
3. The smoothed image is used to detect edge sthrough CANNY edge detection function
4. A mask is applied on the area, for the area of interest
5. The Hough transformation is applied to identify the lines in the image. At this stage, each frame consisted of multiple lines.

RBG_IMAGE => GRAYSCALE_IMAGE => GAUSSBLURRED_IMAGE => EDGE_DETECTED_IMAGE => MASKED_EDGED_IMAGE => HOUGH_TRANSFORMED_IMAGE

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![A sample sequence][original]
![alt text][gray]
![alt text][gauss]
![alt text][canny]
![alt text][mask]
![alt text][maskedcanny]
![alt text][hough]
![alt text][result]
There were some instances where sometimes there were few lines of undesired slope that resulted in distorted lines. Such cases were also taken care of. 
![alt text][horizontallines]
![alt text][shade_original]
![alt text][shadedlines]
There were also few cases where there were no lines detected for one of the sides. Such cases were also taken care of.
![alt text][nolinesright_original]
![alt text][nolinesright]
There were instances where the road texture changed, resulting in wrong calibration of the thresholds. These cases were also taken care of.
![alt text][brightroad]
![alt text][zigzaglines]



### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...

