---
layout: post
title: Task 2 - Rescue People
---
# Objective of this task

The goal of this task is to design and implement an **autonomous drone control algorithm** capable of performing a search-and-rescue operation over the sea.  

The drone must:
- Take off and land autonomously.
- Navigate to a predefined starting location.
- Perform a systematic search for people (victims) using computer vision.
- Return safely to the base once the search is completed or the timer expires.

This mission simulates a real-world emergency response, where the drone is deployed to locate survivors after a maritime accident.

---

# Structure of the code

The solution is implemented in Python and organized around a **class-based state machine**. This ensures that all drone behaviors — from takeoff to search to landing — are handled in a modular, scalable, and maintainable way.

## Drone class and state machine

All the control logic is encapsulated inside a single class called `SearchDrone`.  
This class acts as a **mission controller**, managing states and transitions between them depending on the drone’s progress.

The drone operates under four main states:
1. **init** → takeoff and setup.
2. **go_start** → move to the initial search area.
3. **search** → execute the search pattern and perform face detection.
4. **return** → return to the base and land.

Each state has its own function, and the class method `update()` decides which one to execute depending on the current mission state.  
This structure avoids large monolithic loops and makes the behavior easier to debug and expand (for example, adding a `pause` or `emergency` state later).

Example state logic:
```python
def update(self):
    if self.mission_state == "init":
        self.takeoff()
    elif self.mission_state == "go_start":
        self.go_to_start()
    elif self.mission_state == "search":
        self.search_pattern()
    elif self.mission_state == "return":
        self.return_and_land()
````

Because all the functionality (movement, scanning, logging detections, etc.) is grouped within the class, you could easily run **multiple drones** by creating several `SearchDrone` objects, each with its own parameters and search zone.

---

## Timer

In real missions, the drone’s flight time is limited by its **battery capacity**.
To simulate this constraint, the program includes a **countdown timer** (`self.battery_ticks`).

This timer starts after takeoff and decreases during the search phase.
When it reaches zero, the drone stops searching and transitions to the **“return”** state, flying back to the base and landing safely.

This ensures that:

* The drone doesn’t search indefinitely.
* It always has enough battery left to return and land.
* The mission ends predictably, even if no survivors are found.

# Challenges

## GPS to UTM conversion

The initial mission data (the boat location and estimated survivor coordinates) was provided in **GPS coordinates (latitude and longitude)**.

However, the drone’s control API (`HAL`) does not accept GPS coordinates directly — it requires **UTM (Universal Transverse Mercator)** coordinates in meters.

To handle this we made the conversions using multiple external tools.

First we took the GPS coordinates that were given to us in degrees, minutes, and seconds; [and converted them to decimal degrees](https://www.rapidtables.org/convert/number/degrees-minutes-seconds-to-degrees.html).<br/>
With that done, we can use this [other site](https://rcn.montana.edu/Resources/Converter.aspx) to find out the **Zone, Eastings, and Northings** of our GPS coordinates.

**[ADD IMAGE OF WEBSITE]**

---

## Search algorithm

Once the drone reaches its target area, it performs a **systematic search pattern** to cover as much surface as possible.

We chose an **expanding square (spiral) pattern** — similar to what is used in maritime search-and-rescue operations — because it allows efficient coverage starting from a central point.

Each time the drone reaches a waypoint, it updates the next target by slightly increasing the “radius” of the square, creating an expanding spiral.

This is implemented as follows:

```python
def next_waypoint(self):
    if self.direction == 0:
        self.current_target = (x + step, y + step)
    elif self.direction == 1:
        self.current_target = (x + step, y - step)
    elif self.direction == 2:
        self.current_target = (x - step, y - step)
    else:
        self.current_target = (x - step, y + step)
        self.step += 2.0  # increase search radius
    self.direction = (self.direction + 1) % 4
```

This creates a smooth expanding grid path:
→ Up-right → Down-right → Down-left → Up-left → (increase radius) → repeat.

This way, the drone covers the area evenly while avoiding overlap between passes.

**[ADD CODE BLOCK DIAGRAM OR IMAGE OF SEARCH PATTERN]**

---

## Face recognition

The face recognition module uses **OpenCV’s Haar Cascade classifier**, which is pre-trained to detect human faces.

However, because people floating in the water may appear in many orientations — upside down, sideways, or rotated — a single detection pass could easily miss them.
To address this, we implemented a **rotational scanning approach**:

1. Capture the ventral (downward) camera frame.
2. Run face detection.
3. If no faces are found, rotate the image 20° and try again.
4. Repeat up to 360° of rotation.

This ensures that even if a person’s face is tilted, the classifier will eventually see it at a near-upright angle and detect it.

For each succesful scan where a person is found, the location is compared to previous succesful scans, and if it is new, we log it for later use.

# Summary and Results

[ADD VIDEO]

