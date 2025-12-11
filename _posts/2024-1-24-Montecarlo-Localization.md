---
layout: post
title: Task 5 - Laser Mapping
---
# Objective of this task
The goal of this project is to build a robot that can explore an indoor environment on its own while creating a representation of the space as it moves. The robot continuously reads its position and laser measurements, updates an internal occupancy map, and navigates around obstacles using a simple behavior pattern. The position of the robot is not precisely known so the map is made using probability to continuously update it.

# Map making

## Log Probability

The map is represented as a grid where each cell stores a log-odds value indicating how likely it is to contain an obstacle. Each laser beam reduces the log-odds of the cells it passes through, encouraging them toward a “free” classification, while the end of the beam increases the log-odds, nudging that cell toward “occupied.” This formulation allows many small updates to accumulate gradually, smoothing out noise and giving consistent results even when sensor readings vary. Log-odds also prevent numerical saturation by keeping values bounded, and they convert cleanly back to probabilities for visualization.

Log-odds mapping is advantageous because it supports repeated corrections. A cell that was initially marked free may later be updated as occupied if new, stronger evidence appears. Likewise, uncertain or conflicting readings settle naturally toward the value that has received the most consistent support. This makes the method robust for real-time mapping with imperfect sensor inputs.

## Bresenham

To determine which cells lie along a laser beam’s path, the program uses the Bresenham line algorithm. This classic technique efficiently steps through grid cells between two points without gaps or diagonal artifacts. By tracing these cells from the robot to the beam endpoint, the robot can reliably identify the exact path the laser traveled through the map.

This approach ensures that free and occupied updates are applied in the right locations. Free-space cells along the beam get incrementally reinforced as the robot moves, carving out corridors and open areas in the map. At the same time, the final cell of each beam becomes the primary candidate for marking obstacles, creating crisp, well-defined walls once enough scans accumulate.
# Driving around

## State machine

Robot movement is controlled by a small state machine that cycles through a predictable exploration routine. It begins with an initial turn, drives forward until an obstacle appears, performs a pair of ninety-degree turns with a short forward step in between, and then continues straight again. The turning direction alternates each cycle, allowing the robot to sweep different parts of the environment. This structured pattern keeps the behavior simple but still effective for covering space while the map is being built.

# Video example
[![LaserMapping](https://img.youtube.com/vi/xJt3bN62E5c/0.jpg)](https://www.youtube.com/watch?v=xJt3bN62E5c)


