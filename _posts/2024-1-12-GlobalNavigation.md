---
layout: post  
title: Task 4 - Amazon Warehouse
---

# Objective of this task
The goal of this task is to program a logistics robot capable of autonomously transporting warehouse shelves from one location to another.  
To achieve this, the robot must combine **path planning** using the OMPL library with **reactive navigation** based solely on positional feedback.  

The assignment is divided into three progressively complex parts:
1. **Point-based planning with a holonomic square robot and inflated obstacles.**  
2. **Planning with different robot geometries for the outbound and return trip**, incorporating a shelf footprint.  
3. **Full navigation using a dynamically constrained rectangular robot with Ackermann steering.**

Each stage builds on the previous one, ultimately producing a robust navigation pipeline similar to those used in real automated warehouses.

---

# First Type
In the first part, the robot is modeled as a **simple square holonomic agent**.  
The main idea is to test pure OMPL planning using an **inflated obstacle map**. This simulates the robot’s footprint without needing to incorporate geometric collision checks.

### Key Characteristics
- Workspace represented as a 2D occupancy grid.  
- Obstacles are “grown" to safely account for robot size.  
- SE2 state space is used, but only positions matter (orientation is not constrained).  
- Any sampling-based OMPL planner may be used (e.g., RRT, RRT*, PRM).  
- The planned path is followed using a simple proportional controller driving directly toward each waypoint.

### Limitations
While this system works for the first trip of the holonomic robot, the moment the robot picks up the shelf, the geometry changes and all obstacles have to be inflatedto the biggest radious of the shelf. This is suboptimal. Using the ackerman robot brings new problems with this system.

The combinations of these problems and the better alternatives on hand are the reason we skipped implementing this system. It may be simpler, but it is incompatible with the task at hand.
---

# Second Type
This section introduces realistic geometric constraints.  
Here the robot still moves holonomically, but we must validate robot–obstacle collisions using **actual rectangular footprints**, both when the robot is empty and when it is carrying a shelf.

To implement this we coded different classes to contain the relevant helper functions and make the code clean and interchangeable wiht future verisons.

### Map Processing
A homography is used to convert between **Gazebo world coordinates** and **pixel map coordinates**.  
The map is segmented into:
- Free cells  
- Walls  
- Inaccessible zones (e.g., rails or forbidden areas)  

A binary occupancy grid is generated, and visualization layers are provided to inspect the planned path and obstacle masks. 

### Planning to Location
Path planning is executed with OMPL’s SE2 state space and a custom **state validity checker**.  
The validity checker:
- Rotates the robot’s rectangular footprint according to its orientation.  
- Projects it into map coordinates.  
- Checks for collisions by rasterizing the polygon into the occupancy map.  

Before the return trip, the shelf footprint is temporarily in the binary map to allow navigation from its original position.

### Driving to Location
A simple state machine is called to:
- Make a plan using the relevant classes.  
- Paint on the map the middle points of the plan.  
- Drive and iterate through waypoints until the goal is reached.
- Repeat all these processes with a new robot footprint and destination once the robot has lifted the shelf.

The robot measures its position and angle relative to the next waypoint, and using simple proportional controllers, aproaches it.
This provides stable and predictable motion suitable for holonomic platforms.

### Video Example
[![First Example](https://img.youtube.com/vi/AbDXpyGJzAw/0.jpg)](https://www.youtube.com/watch?v=AbDXpyGJzAw)

---

# Third Type
The final stage transitions from holonomic motion to a **rectangular robot with Ackermann steering**, such as a real warehouse automated guided vehicle (AGV).

### Reusing Code
Much of the map processing, coordinate transforms, and state validity logic from the previous type can be reused, thanks to the OOP aproach taken.<br />
The MapProcessing class could be reused same as the footprint validator class in charge of the geometric checks. The overall code for the state machine was reused, but the helper functions within were changed to accomodate the new planning software and the new ways the robot was allowed to move.

### Different Code
To make an ackerman plan with OMPL we had two choices to make, **DubinsStateSpace** and **ReedsSheppStateSpace**. While both were valid choices, I went with Reeds for it's ability to include backing up in it's planing. This was a key feature in the cramped space of the warehouse and the lacking turning radius of the robot.<br />
To properly implement this OMPL I needed to take some extra measurements. The wheelbase (distance between axels) was taken by measuring the robot and using a rule of thirds using the data that was given in the briefing. The steer angle was taken a similar way, by measuring on screen and superimposing a digital angle wheel to get "precise" measurements.

For the driving part of the state machine I made it so that on each update it checks the next target point on the planned path, computes the distance and angle from the robot to that point, and decides whether to move forward or reverse depending on how well the robot is already facing the target. .<br />
It then generates a steering angle  and a linear speed proportional to the diference in actual position and target position. Using a simplified Ackermann model, it converts the chosen steering angle and linear speed into an angular velocity command, clamps it to a safe range, and finally sends the linear and angular velocity commands to the robot; when a waypoint is reached, it advances to the next one, and when the whole path is finished, it stops.

### Video Example
[![Second Example](https://img.youtube.com/vi/sLmZ-jPfSF0/0.jpg)](https://www.youtube.com/watch?v=sLmZ-jPfSF0)

---
