---
title: "Real Time Finger Detection"
date: 2022-05-22T22:53:39-04:00
draft: false
---

## Introduction

In this software project we use image processing techniques on a single area of a live video feed to identify the number of fingers on display. The main image processing technique used is identifying contours. A contour is created by joining all the continuous points of an image that have the same color. Contours can be used for various image processing programs like shape analysis, object detection or recognition.
Image contouring is an important tool with uses in any field. It is used in medical research to identify important information, such as organs or tumors in MRI images, CT scans, or cardiac images. Object detection includes identifying persons. This could be implemented in public spaces during this pandemic to count the number of people in a shop.
Object detection and tracking is currently used in military technology. Unmanned aircrafts prove to be a threat to military bases. Military security uses this form of image processing to detect unmanned aircrafts and track their location. Cameras placed around the perimeter of a base that can apply image processing will be able to give ample warning of what the program considers a threat.

## Method

We begin this program by selecting a region of interest (ROI) inside the webcam image. This allows us to perform image processing only within the ROI and leave the rest of the image untouched.
To identify the hand, an upper and lower HSV value is set to encompass the range of palm skin-tone colors. The ROI image is thresholded such that pixels with HSV values falling within the skin-tone range will be white and any pixels falling outside the range will become black. The image is then blurred and thresholded again to reduce noise.
Next the function cv2.findContours is called to outline the hand shape from the binary image created in the first step. Then we also call the function cv2.convexHull to create a polygon around the hand using the contour obtained. The convex hull is created using the points set that describes the hand’s contour. We use the contour and hull obtained in this step to identify the defects.
Defects are the locations where the contour does not match the hull. From the image we can see that defects occur where fingers connect to the palm and at the wrist. The simplest method I found to count the number of fingers in an image was to find the defect points in between the fingers, then add 1 to obtain the number of fingers. The idea behind this is that a defect point is the result of two fingers. So, if three defect points are sensed, then there must be four fingers present.

In order to only take into account the defect points in between the fingers, we use the Law of Cosines. This trigonometric theorem helps relate triangle sides to angles. The formula to calculate the angle x would be

By calling cv2.convexityDefects we get four indexes. These indexes represent the starting point, ending point, furthest point, and depth of each defect. Looking at the image, the starting point represents B, ending point is A, and C is the furthest point. Using these coordinate points we can calculate the length from one point to another using the Euclidean distance formula.

From the image, we can see that the angle in between two fingers must be less than 90°. After calculating the angle at each convexity defect, we can simply filter out the defect points we need by testing whether the angle is less than 90°. Finally, to obtain the number of fingers we add 1 to the number of defect points that satisfy this condition.

## Discussion

In the process of writing the program I found that it was important to create a clear binary image for the next few image processing functions to work well. The binary image created by thresholding using the skin-tone range had some noise within the palm or in the background and varied in intensity depending on the lighting. To help create a clearer image I found that it was useful to blur the binary image then threshold it again using OpenCV functions to end up with a clear binary image with little to no noise.
While this program works well to count multiple fingers, the algorithm does not work when trying to identify if a single finger is being displayed. When one finger is held up, there is no defect point with an angle less than 90°. In the demo, even when there is one finger being shown the count will show 0. 
Confining the image processing to the ROI helped simplify this project. If the ROI was not included, other objects in the path of the camera would affect the process. It would be difficult to set up the camera such that no other parts of the person or any furniture were in frame.

## Conclusion

The main shortcoming of this program was that when a single finger is displayed, the algorithm senses it as 0 fingers. The solution to this would be to create a new algorithm to sense the fingertips instead of the point in between the fingers. In this program we did find the starting and ending points of each convexity defect. These points represented the fingertips. Another method would have to be found to filter out the points that do not count. 
After fixing this feature, the goal of this project can be expanded to count up to 10 fingers by processing an ROI that could display both hands or having two separate ROIs and adding the results from both for a
total. In the future this program could also be adapted to audibly count out the number of fingers, recognize and display what object is a hand or fingertip.
