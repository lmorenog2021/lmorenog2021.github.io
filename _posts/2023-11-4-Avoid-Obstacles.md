---
layout: post
title: Task 1 - Basic vacuum cleaner
---
## Objective of this task
For this task we are going to take the car we've been working on previously, and make it run laps around our digital track; with the added complexity of other cars that he must avoid.

To do this we have discarded the camera, and instead we added a lidar to the front of the vehicle. Using this LIDAR and pre mapped points around the circuit, we will calculate the Virtual Force Field (VFF) of our car.

## What is VFF
VFF is an algorithm very popular in the world of mobile robotics, as it allows for autonomous movement of a robot as long as we have a map where the robot can know its absolute position, and a sensor to analize its enviroments, and change the map according to cahnges in it's surroundings.

We are going to implement it by calculating two vectors, one that points to our destination, and another one that points away from any obstacles. The sum of those two vectors will be our direction vector.

## Calculating the oposing force vector
For the oposing drection vector we are going to use the data given to us from the LIDAR, though this is not a simple plug and play operation.
1. First we are going to use the code provided by our teachers to parse the laser data, and store them in a list that holds the angle of the laser measured, and the distance measured by that laser.
2. Secondly we are going to take that list, and transform each angle-distance pair to (x,y) coordinates relative to the car.
3. Lastly, we are going to take the mean of all the cartesian values, and clamp it around (-3, 3) so it shows better in our graphical interface and limits erroneus values that can sometimes come up.

## Calculating the direction vector
To calculate the direction vector we need to create a vector that points to the next target in the map. For this we will follow these steps:
1. First we get the absolute coordinate values of the next target by making a call to the API, and we parse it throught this function to transform the absolute cartesian coordinates to coordinates relative to our vehicle.
2. Secondly we clamp the function around (-3,3) for the same reasons as before, and because if we had taken the original values, the vector would have been to large to process confidently whe the car is far away from the target.
3. Lastly, we run this loop that constantly checks if we got close to our target, and if we did, it changes to the next intended target. 

## Translating vector values to the car
Now we have a VFF implemented, but we still need to translate vector values to movement. This is the hardest part of the whole excercise, as the best method we have now is trial and error.

Our first attempts were to use the Y axis of the DirectionVector as angular velocity, while keeping linear velocity constant. But this proved to be too slow for straights, and to fast when dealing with turns.

Our next attempt was to add X values as linear speed and Y values as angular. Predictably, this meant that as the car neared his next target it would slow down almost to a stop, and when it reached it, it would very quicly accelerate due to the pull of the new far away target.

To solve this we tried addng a constant number to the linear velocity, this solved the starting and stopping problem, but it created a new one; the car just wouldn't turn in time to avoid walls and other cars.

A solution we came up with is incrementing proportionally the X and Y values of the apposing force with two different constants. This seemed to work, but we recommend some testing, as your results may vary, as the slightest changes in code and simulation could warrant adjustments for these constants.

## Problems faced

## Results
