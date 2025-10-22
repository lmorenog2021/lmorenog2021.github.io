---
layout: post
title: Task 2 - Rescue People
---
# Objective of this task
We want to use create an algorithm that controls a drone. The drone will take off and land autonomously and travel to a specified location from where it would begin a search for people.

# Structure of the code 
## Drone class and state machine
To control the execution of the drone we use use a state machine inside a loop so that it assures responsive and reliable behaviour. Additionally, instead of programming the code responsible for the drone's controls in separate functios, we unify them all inside a class, this allows us to have cleaner code, while being flexible enough to scale up to use multiple drones at the same time.
## Timer
To limit the time the drone spends flying over the sea we have implemented a timer that counts down to the moment we are allowed to change states to "Land", Otherwise it would constantly loop around the affected zone, searching for people even if all of the survivors were found, as we do not know how many of those there are.

# Challenges
## GPS to UTM
We were given the GPS coordinates to the boat and to the aproximate coordinates of the survivors. Unfortunately the drone API does not accept GPS coordinates and we must convert them to UTM coordinates that it can understand.
[ADD IMAGE OF WEBSITE]

## Search algorithm
Once the drone is in position and ready to start scannning for people it will start to move in a helix pattern using this algorithm
[ADD COODE]

## Face recognition
we are given resources to complete the face recognition software using Haar Cascades, so we implement them. However, the Haar Cascades algorithm is trained on faces in a single orientation, and our floating victims can be in many orientations; so to avoid false negatives and leaving people stranded, we spin each image 360 degrees and scan again each 20 degrees spun. That way we make sure that no person is left behind.


# Results
[Result.webm](https://github.com/lmorenog2021/lmorenog2021.github.io/assets/92941117/8fa81fee-0092-4b3b-93a1-4cd87bc1f3b8)

