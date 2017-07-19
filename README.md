## **Priliminary Attempt to Find Lane Lines on the Road by RaghuNath (RaNa)** 





---

**Finding Lane Lines on the Road**

The intent of this project is the following:
* Make a pipeline that finds lane lines on the road from a video



[//]: # (Image References)

[original]: ./process_images/original.png "Original"
[gray]: ./process_images/gray.png "Gray"
[gauss]: ./process_images/gaussfiltered.png "Gauss output"
[canny]: ./process_images/edgedetected.png "Canny output"
[mask]: ./process_images/maskonoriginal.png "Mask"
[maskedcanny]: ./process_images/maskoncanny.png "Mask on Canny filtered"
[hough]: ./process_images/houghoutput.png "Hough output"
[result]: ./process_images/result.png "Result"
[shade_original]: ./process_images/shade_original.png "Shaded Road"
[shadedlines]: ./process_images/shade_lines.png "Lines with shade"
[nolinesright_original]: ./process_images/nolinesright_original.png "No strong lines on the right"
[nolinesright]: ./process_images/nolinesright.png "No detected lines on the right"
[brightroad]: ./process_images/brightroad_original.png "Road texture changed to bright color"
[zigzaglines]: ./process_images/zigzaglines.png "Resulting zigzag lines"

---
#### Repo Contents

This repo contains a Jupyter notebook that was developed and tested in the following environment:
 * Python 3.5.2
 * Matplotlib 1.5.3
 * NumPy 1.3.1
 * OpenCV 3.2.0
 * MoviePy 0.2.3.2
 * IPython 5.1.0
 
This repo also contains a html file that shows the contents of the jupyter notebook after all the cells are run. 

This repo contains few folders to hold test images, test videos and output of test videos. 

### Reflection

### 1. Description of my pipeline

The pipeline is implemented in the following sequence of steps:
1. The original RGB image is converted to gray scale image
2. The gray scale image is blurred to remove unwanted spikes in thresholds
3. The smoothed image is used to detect edge sthrough CANNY edge detection function
4. A mask is applied on the area, for the area of interest
5. The Hough transformation is applied to identify the lines in the image. At this stage, each frame consisted of multiple lines.
6. The lane lines are identified from the set of all lines, and the individual frames of the image are marked.


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

For a good image (i.e. an image with crisp lane lines and good contrast), finding the lane lines is very straightforward. This includes the following steps: 

RBG => GRAYSCALE => GAUSSBLURRED => EDGE_DETECTED => MASKED_EDGED => HOUGH_TRANSFORMED => LINES_SUPERIMPOSED

The folling sequence of images indicate the process followed.
![A sample sequence][original]
![alt text][gray]
![alt text][gauss]
![alt text][canny]
![alt text][mask]
![alt text][maskedcanny]
![alt text][hough]
![alt text][result]

After the lines from Hough transformation are obtained, all the points that may correspond to the each side of lane line ( based on their location in the image : left or right, and based on the closeness of their slope to the past slope of the corresponding lane line) are seggregated, and then 1st order curve fit is done. The resulting curve (straight line equation) is used to draw the lines on the image. To avoid the jumps in the lines drawn, the lines equations are passed through a moving average filter to remove jitter. This results in a smoothly drawn lines. Assumption here is that the lane lines don't change the orientation drastically. The moving average is implemented with a set of global variables.


The basic pipeline works well for a static image, where the thresholds, and some conditions can be finetuned. To make this pipeline robust, we have to also consider some of the failure modes, and make the pipeline adaptive to the dyamically changing conditions. 

Some of the possible variations in the images observed:
1. Given images may have different sizes (eg: 960 X 540 pixels). The pipeline designed is set to have the geometric thresholds (such as masking polygon coordinates) based on the image size. 
2. Given images may have different road brightness. The thresholds for canny edge detection function are derived based on the average intensity of the pixels in the masked image (the road portion of our interest)

![alt text][brightroad]
![alt text][zigzaglines]

3. The given image results in many lines in the canny edge detected image as a result of the tree shadow on the road. This case was handled by considering only the lines whose slope is within the upper and lower threshold for the slopes. These thresholds are derived from the moving average of the past slopes. In this case, another input considered was that, the left lane lines are considered from negative slopes resulting out of the lines on the left side of the image only. This is to avoid possible errors in computing the lines.

![alt text][shade_original]
![alt text][shadedlines]

4. In some extreme cases, there were hardly any lines on one of the sides. In such cases, till the next lane line is identified, the past value is maintained. 

![alt text][nolinesright_original]
![alt text][nolinesright]




### 2. Potential shortcomings with current pipeline


This pipeline is not robust, and may not work for all the real conditions. This is a functional pipeline for a constrained conditions. 

Some of the expected shortcomings are: 
1. The masking polygon is fixed at corners. When the vehicle moves off the lane, the lane lines may no longer be identifiable. 
2. When there is a vehicle in front of the camera covering part of or full view of the road, this algorithm may not work for a long duration. 
3. When the vehicle reaches a Y-junction, the slopes of both the lane lines could be close. This results in calculating wrong slope for the lane line. 
4. When the lane lines fade away on the roads, this pipeline fails to detect them. The thresholds are set with some assumptions.
5. When the road has pot-holes, some of which may be along the travel direction, this pipeline detects the edges of the pot-hole too, and includes them in estimating the lane lines. 
6. When the vehicle is going on a non-paved road, the jitter is so very high that identifying the lane-lines would be difficult.

The roads are never this ideal always. This list never ends. 

### 3. Suggested improvements to this pipeline

Some of the possible improvements are:

1. The masking polygon's corners need to be dynamically adjusting. Starting with a fixed known frame, the corners should move based on the position of the vehicle on the lane.
2. The pipeline may have better fail-safe methods for protection againest failure modes, such as when a vehicle is closely following another vehicle at front, and there is no visibility of the road. In such cases, the size and distance of the vehicle in the front may be estimated, and the VUT(vehicle under test) may follow the same course. 
3. A seperate case handling has to be included for double lanelines and Y-junction's lane lines. 
4. Canny edge detection for the complete image may result in wrong edges for faded lane lines, or partially shaded roads. A smaller frame of sufficient pixels needs to be identified that will move around the desired zones of the image to process canny edge algorithm with different thresholds in each part of the image. This is to be adaptive. 
5. This pipeline identifies only the straight lines. It does not detect curved roads yet, due to the implementation of 1st order curve fitting implementation. A higher order curve fit of the correct points will be able to give indication of road curve too. 

This list is also never ending, and there may never be a perfect pipeline for this purpose. This has to evolve as we test more and more images/videos, leaning from the failures.  
