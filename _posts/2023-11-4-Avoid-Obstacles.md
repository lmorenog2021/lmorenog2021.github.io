---
layout: post
title: Task 1 - Basic vacuum cleaner
---
## Objective of this task
For this task we are going to take the car we've been working on previously, and make it run laps around our digital track; with the added complexity of other cars that he must avoid.

To do this we have discarded the camera, and instead we added a lidar to the front of the vehicle. Using this LIDAR and pre mapped points around the circuit, we will calculate the Virtual Force Field (VFF) of our car.

## What is VFF
VFF is an algorithm very popular in the world of mobile robotics, as it allows for autonomous movement of a robot as long as we have a map where the robot can know its absolute position, and a sensor to analize its enviroments, and change the map according to cahnges in it's surroundings.

We are going to implement it by calculating two vectors, one that points to our point, and another one that points away from any obstacles. The sum of those two vectors will be our direction vector.

## Calculating the oposing force vector

## Calculating the direction vector

## Translating vector values to the car

## Problems faced

## Results
