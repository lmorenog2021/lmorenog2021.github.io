---
layout: post
title: Task 1 - Basic vacuum cleaner
---

## Setup and first problems
Because this is our first exercise with this new software (Unibotics) we will have to spend some time downloading everything to get up to speed.
Follow this steps to start programming as quikly as possible:
1. Download Docker and test it with the "Hello-World" package, [the official docs have a great guide](https://docs.docker.com/desktop/)
2. Pull the current distribution of Robotics Academy Docker Image:
  ```javascript
   docker pull jderobot/robotics-academy:latest
   ```
3. Launch the docker container with one of two options, the standard one is
   ```javascript
   docker run --rm -it -p 7681:7681 -p 2303:2303 -p 1905:1905 -p 8765:8765 -p 6080:6080 -p 1108:1108 -p 7163:7163 -p 7164:7164 jderobot/robotics-academy:latest
   ```
   and for users with GPUs and software compatible with [virtualGL](https://virtualgl.org/)
   ```javascript
   run --rm -it --device /dev/dri -p 7681:7681 -p 2303:2303 -p 1905:1905 -p 8765:8765 -p 6080:6080 -p 1108:1108 -p 7163:7163 -p 7164:7164 jderobot/robotics-academy:latest
   ```
4. Now all that is left is to create a Unibotics account, navigate to the "Academy" tab, and start programing.

In my case, I had some compatibility problems caused by Firefox, that made the VNC viewers not load properly, thankfully, my question was answered in a pinned comment on the unibotics forum.
They have proven to be a very handy resource, so try to check them periodically.

## Objective of this task
To make an autonomous robot that can move over as much floor area as possible, without colliding with the walls and other objects. To achieve this, the robot is going to have to combine two behaviours, an obstacle avoidance movement sistem, and a basic coverage algorithms.

For the obstacle avoidance software we will use the lidar, that when detecting a wall, will randomly choose another direction to move in.
And for the coverage algorithm, we will ignore the many complicated solutions there are, and settle with a simple spiral pattern.
[ADD CURA SLICER PICTURE]

## First tests with the site

## Creating a state machine

## Testing the state machine

## Minor adjustments

## Success! 
