---
layout: post
title: Task 5 - Laser Mapping
---
# Objective of this task
The goal of this project is to build a robot that can explore an indoor environment on its own while creating a representation of the space as it moves. The robot continuously reads its position and laser measurements, updates an internal occupancy map, and navigates around obstacles using a simple but effective behavior pattern. By combining mapping and autonomous movement, the robot gradually uncovers the structure of the area without requiring prior knowledge of the layout.

# Map making

## Log Probability

The map is stored as a grid in which each cell holds a log-odds value representing how likely it is to be occupied. Laser beams decrease the probability for the cells they pass through (marking them as free) and increase it at the endpoints where objects are detected. Log-odds values allow incremental corrections over time and avoid numerical instability. These values are later converted into a grayscale image that the user can view in the interface.

## Bresenham

To determine which cells a laser beam crosses, the system uses the Bresenham line algorithm. This method efficiently traces a straight line between two grid locations, allowing the robot to update all the cells along the beam’s path. As the robot scans the environment, these traversed cells are marked as free, while the end cell is marked as either occupied or simply the edge of sensor range.

# Driving around

## State machine

Robot movement is controlled by a small state machine that cycles through a predictable exploration routine. It begins with an initial turn, drives forward until an obstacle appears, performs a pair of ninety-degree turns with a short forward step in between, and then continues straight again. The turning direction alternates each cycle, allowing the robot to sweep different parts of the environment. This structured pattern keeps the behavior simple but still effective for covering space while the map is being built.

# Video example
[YOUTUBE LINK]


