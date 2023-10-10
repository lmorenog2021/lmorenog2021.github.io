---
layout: post
title: Task 2 - Autonomous Car
---
## Objective of this task
For this excercise we are tasked with making the virtual F1 car run around the circuit following the red line.

Because this is a challenging task to take all at once, we will fragment it into two parts that can be tested and debugged independently. The first part is making the artificial vision work,
and after that we can takcle the PID controller for the movement.

## Configuring artificial vision
We are using OpenCV in python, so reading THIS(ADD LINKS) tutorial will be recomended.
Our objective is to find the center of the line. There are many ways to do this, but they all start with masking out all of the colors but the red we need, for this we will use this code:
[ADD IMAGE]
Now we can process the image to find the center of the line. There are two ways to do this
1. Manually iterate through each pixel in a line or segment of the road.
   ✅ Easy to implement
   ✅ Can be less demanding than scanning the complete image
   ❌ Less precise
   ❌ Less reactive
2. Use OpenCV functions to find the centroid of the line
   ✅ More reliable
   ✅ Better aproximation helps when taking curves
   ❌ LHarder to implement
   ❌ Processor heavy

We chose to use the second option to save us some trouble when making the PID, as the more advanced artificial vision will solve some problems that could arise when taking corners.

## Making a PID controller

## Testing for speed

## Additional Problems
