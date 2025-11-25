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
Much of the map processing, coordinate transforms, and state validity logic from the previous type can be reused, thanks to the OOP aproach taken.  
However:
- The robot footprint is no longer the same as before: the planner must respect the vehicle’s non-holonomic constraints.
- The motion controller had to be replaced by a steering-based controller capable of moving the vehicle around curved trajectories.

### Different Code

### Video Example
[![Second Example](https://img.youtube.com/vi/sLmZ-jPfSF0/0.jpg)](https://www.youtube.com/watch?v=sLmZ-jPfSF0)

---
