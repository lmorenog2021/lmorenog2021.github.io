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
3. Use OpenCV functions to find the centroid of the line  
   ✅ More reliable  
   ✅ Better aproximation helps when taking curves  
   ❌ Harder to implement  
   ❌ Processor heavy  

We chose to use the second option to save us some trouble when making the PID, as the more advanced artificial vision will solve some problems that could arise when taking corners.

## Making a PID controller
For this we are going to reuse the code from a previous project, one of the many benefits of object oriented programming.  
[ADD IMAGE]  
The way this PID implementation works is by setting up the limits of the inputs it can recieve, and the limits of the outputs it can write. Then you can tweak the constants Kp, Ki, Kd depending on how you want the PID to behave. We have set the input limits to (-320, 320) to represent the width of the screen, and the oitput limits as (-1, 1) as having any more anglar velocity will make the car spin out of control.   
[ADD IMAGE]   
(We take 320 as the center of the image, even though the camera is off to the side of the car. 385 would have been the correct point to take if we wanted a perfectly centered vehicle, but for simplicity we decided against it)   
[ADD IMAGE]  
For the constants you either have to try and make a mathematical model of the car, or simply test different values and find the best ones. After much experimentation, this are the ones we optimised for a linear velocity of 4'5.  

## Testing for speed
Using a constant velocity can give us great results, but to truly achieve speed we need to, like any racer, accelerate in the straights, and slow down before the curves. I had tried two methods:
1. **Using the PID for linear velocity**
   My first idea was to simply take a high linear velocity, and reduce it by the error from the turning PID, this way when the car was going straight and the error was low, it would move faster, and when the car was turning and had a high error, it would slow down. While the idea was great on paper, in practice it proved to be a whole different thing, as simultaneously changing both the angular and linear speed made the car unstable and very wobbly, in a way that no PID changing could solve.
2. **Using a NEW PID for linear velocity**
   Moving on to another idea, using the *y* axis of the centroid as the input for a *NEW* PID. The logic behind this change was that when the image was of a straight, the *y* axis was constant at a predictable level; but when fed a curve, the added pixels on top of the image shifted the axis up a considerable amount of levels. This phenomenom is what we will use to create a new PID to regulate the linear velocity.
   [ADD IMAGE]
   The problem of this concept came when leveling two different PID proved to be too finicky to be done by hand. While a more knowlegeable engineer could probably make a mathematical model to iron out all the kinks, in our case it's out of the scope of the project.
3. **Using different set speeds**
   The problem with the las attempt was adding complexity with the PIDs, but the *y* axis concept had potential, so we fell back on ol'reliable, the if statement.
   By taking three different set speeds and asociating them with three possible levels on the *y* plane, we could benefit from more controlled changes that can be adapted to work with our PID.
   [ADD PICTURE]
   And it works!
   ...sometimes. It can finish laps, but once in a while, it will deviate from the path and lose itself, so it becomes a tradeoff, an improvement in speed vs the flawless laps of the constant velocity model.

## Results

