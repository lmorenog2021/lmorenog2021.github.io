---
layout: post
title: Task 1 - Localized Vacuum Cleaner
---

## Objective of this Task

The goal of this task is to implement a **localized autonomous vacuum cleaner** capable of:
- Understanding its environment from a known map,
- Dividing the environment into discrete cells,
- Systematically covering all traversable areas,
- Avoiding obstacles,
- Navigating efficiently between regions when direct traversal is not possible.

The robot operates in a simulated environment using the `HAL` and `WebGUI` APIs, combining **coverage planning**, **grid-based pathfinding**, and a **low-level motion controller**.


## General Overview

The solution is structured into clearly separated blocks, each responsible for a specific subsystem:

- Global state and configuration  
- Coordinate transformations  
- Grid and cell utilities  
- Coverage planning logic  
- Motion control  
- BFS pathfinding  
- Map initialization and execution loop  

This modular design makes the system easier to understand, debug, and extend.

## Coverage Planning

Coverage planning is implemented in the `generate_coverage()` function.

### Cell-based representation
- The map is divided into square cells of size `CELL_SIZE = 33`.
- Each cell (`region`) is a collection of pixel coordinates.
- Cells are classified as:
  - **Blocked** (contain obstacles),
  - **Visited**,
  - **Pending** (reachable but not yet cleaned),
  - **Priority** (cells that require revisiting via path planning).

### Strategy
The planner follows a greedy, frontier-based approach:
1. Start from the robot’s current cell.
2. Mark the current cell as visited and blocked (to prevent revisiting).
3. Inspect adjacent cells (up, down, left, right).
4. Prefer immediate neighbors that are traversable.
5. If no adjacent traversable cell is available:
   - Select the closest pending cell (Euclidean distance).
   - Mark it as a priority zone.
   - Add it to the route to be reached using pathfinding.

Priority zones ensure that unreachable areas are not forgotten and are later reached using BFS.

The planner also updates the visualization in real time using `WebGUI.showNumpy()`.

## BFS Pathfinding

Pathfinding between non-adjacent cells is handled by the `shortest_cell_path()` function.

### Key characteristics:
- Uses **Breadth-First Search (BFS)** on the cell graph.
- Each node represents a cell, not individual pixels.
- Adjacency is computed using the same grid logic as coverage planning.
- Ensures the shortest path in terms of number of cells.

This method is simple, reliable, and well-suited for uniform grid environments.

## Motion Controller

Low-level motion is handled by the `drive_to()` function.

### Coordinate conversion
- Target pixels are converted into world coordinates using `img_to_world()`.
- The robot pose is obtained from `HAL.getPose3d()`.

### Control logic
The controller operates in two phases:

1. **Orientation alignment**
   - The robot rotates in place until the heading error is below a small threshold.
   - Angular velocity (`W`) is applied proportionally.

2. **Forward motion with heading correction**
   - Linear velocity (`V`) is kept constant.
   - Angular velocity is computed using a simple **PD controller**:
     - Proportional term: heading error
     - Derivative term: change in heading error

The robot stops once it is within 0.1 meters of the target.

## Coordinate Transforms

Two helper functions manage coordinate transformations:

- `world_to_img(wx, wy)`  
  Converts world coordinates into image pixel coordinates.

- `img_to_world(px, py)`  
  Converts image pixel coordinates back into world space.

Both functions rely on precomputed transformation matrices obtained through calibration.

## Execution Loop
During initialization:
- The map image is loaded using `WebGUI.getMap()`.
- The environment is divided into grid cells.
- Cells containing black pixels (`raw_map[x][y] == 0`) are marked as blocked.
- Traversable cells are initialized as free space.
- The robot’s starting cell is detected using its initial pose.

A visual representation of the grid is rendered before execution begins.

The main loop continuously:
1. Generates a coverage plan starting from the current cell.
2. Iterates through the planned regions:
   - Directly drives to adjacent regions.
   - Uses BFS to reach priority regions.
3. Removes regions from the priority list once cleaned.

When all reachable cells have been vacuumed, the system prints a message to notify the end of the program.

# Vídeo Result
[![Task 1: Vacuum Cleaner](https://img.youtube.com/vi/c0Sszfwa4wY/0.jpg)](https://www.youtube.com/watch?v=c0Sszfwa4wY)
