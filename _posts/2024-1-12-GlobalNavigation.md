---
layout: post
title: Task 4 - Global Navigation
---
# Objective of this task
We want to create a self-driving car capable of automatically routing a path to any point selected on a map.

This involves loading a map, determining a path from the car's current position to the target, and dynamically adjusting the path as the car moves and encounters obstacles.

# A brief overview of how it works
The system works by first processing a map to identify obstacles and navigable paths. It then uses gradient-based path planning to find the optimal route from the car's current position to the target destination. The car continuously updates its position and adjusts its path in real-time to avoid obstacles and ensure it reaches the target efficiently.

# Processing the map
1. ***Load and Binarize Map:*** The map is loaded from a specified URL and binarized to distinguish between obstacles and free space, as the original map has some inbetween values that skew our later calculations.
2. ***Gradient Path Planning:*** The algorithm uses a priority queue to assign weights to each cell in the map, starting from the target position and working backwards. This helps in identifying the shortest path.
3. ***Adjust Corridor Values:*** The weight values of the 3 cells adjacent to obstacles are adjusted by adding increasing waights to create a buffer zone around obstacles, making the path planning more robust.
4. ***Transform Map Array:*** The weighted grid is transformed to enhance visualization, we trim and invert the wheights so that the cells closer to the goal are white, while faraway cells remain black.

![Screenshot from 2024-06-06 00-39-47](https://github.com/lmorenog2021/lmorenog2021.github.io/assets/92941117/479d076d-da66-43bf-a18b-18b251168126)

# Driving around
This part uses the same code as the previous task, with very minor adjustments. Theoretically you could keep the obstacle avoidance code and avoid any obstacles not stored in the map.
1. ***Initial Setup:*** The start and goal positions are determined based on the car's current location and the user-selected target position.
2. ***Dynamic Path Adjustment:*** As the car moves, it continuously finds the nearest optimal path within a defined radius(five cells in our code). It converts these positions from absolute map coordinates to coordinates relative to the car's current position and orientation.
3. ***Force Calculation:*** The relative target coordinates are used to compute attractive forces that guide the car towards the goal.
4. ***Motion Control:*** The car's speed and steering angle are adjusted based on the computed forces to smoothly drive towards the target while avoiding obstacles.

![Screenshot from 2024-06-06 00-46-54](https://github.com/lmorenog2021/lmorenog2021.github.io/assets/92941117/b96b29c0-2517-4578-a3a1-62c5da4ed54b)

# Challenges and alternate versions
The first versions we made for this task used an A* search algorithm, and created a path that was made visible with 
```python
GUI.showPath(array)
```
But that proved to be more challenging than originally expected, and with the added confusion that created the changing of coordinates to and from map to world; debugging was complicated and slowed development time to a halt.

In the end we switched to gradient path planning, which was more straight-forward and came with unique benefits; such as being able to plop down the car in any starting location, and being able to path to the goal without needing to redo the map. 
