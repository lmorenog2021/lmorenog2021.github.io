---
layout: post
title: Task 5 - Montecarlo Localization
---
# Objective of this task and test
We want to use a probabilistic approach to finding the location of the robot in a known map. Using the LIDAR the robot comes equipped with as the only sensor to rely on.

# How does our algorithm work
First it creates an amount of "particles" in random positions along the entirety of the map. This particles will be able to measure the distance to the walls as if it had a LIDAR.
The amount of particles created is important, as putting too little will impact the effectivness of later steps, but too many will slow down the hardware and introduce errors in the code execution.
For our hardware 200 particles were a healthy compromisse, but if you can afford it, it is reccomended to go higher.

Then we get the measurements of the LIDAR from the robot, but to save on computing power, we only store every tenth value. Next we iterate through every particle and get the same measurements.

Next we calculate the weight of each measurement by calculating the difference between them, and then calculating the inverse of that.
In other words, we calculate how similar the results are, and assign a bigger weight to the most similar results.

While this is happening the robot is moving in circles, and every particle is doing the same, with some slight Gaussian error added to their movements.
This helps to get more varied reading and to eliminate all particles that end up inside walls or outisde the map.

Lastly, the program chooses randomly from the particles with the highest weight, and creates new particles in those regions.
After many loops of this algorithm, the particles converge on the position of the real robot.

# Results
[Result.webm](https://github.com/lmorenog2021/lmorenog2021.github.io/assets/92941117/8fa81fee-0092-4b3b-93a1-4cd87bc1f3b8)

