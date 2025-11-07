---
layout: post
title: Task 3 - Autoparking
---

## Objective of this task

The main goal of this project is straightforward: **we have a car that must parallel park by itself.** using LIDAR sensors and a simulated IMU.

This is a continuation of our previous work, but this time we’re focusing on fine-tuning the precision and behavior of the system so the car can identify a parking space, align itself correctly, and complete the maneuver smoothly.

---

## Implementation

Just like in earlier exercise, we’ve structured the code around a single main class. This class acts as the brain of the car, holding all the necessary functions that control movement, sensing, and decision-making.

At the heart of it all is a **state machine**, which manages the car’s behavior in a logical and organized way. The state machine is divided into three main behaviors, each one handling a specific part of the parking process. While there are several smaller internal states, for simplicity, we’ll describe them here under these broader categories.

---

### 1. Approach

The car starts off traveling down the center of the road. Its first task is to gently move closer to the row of parked cars. To do this, it measures its current orientation (or angle) for reference, then begins to steer toward the parked cars.

While approaching, the car constantly listens to its front **LIDAR sensors**. When the sensor detects that the distance to the cars has reached a safe, predefined value, the car stops and starts turning in the opposite direction. This counter-turn continues until the car recovers the same orientation it had at the start.

The end result of this phase is that the car is now neatly positioned alongside the parked cars, ready to start searching for a suitable space.

---

### 2. Find Space

Once the car is aligned, it switches to the *searching* mode. Using its right-facing LIDAR, it looks for gaps between parked cars; regions where the sensor returns "inf" distances surrounded by normal readings.

These gaps are transformed into **Cartesian coordinates** relative to the car’s own position, which makes it easier to measure how wide each space really is. The car then finds the largest gap available and compares it to its own width.

If the space is wide enough to fit, the system converts the coordinates to the global reference frame (relative to the world). Once the back of the car has just passed the front boundary of that space, the state machine triggers the *parking* subroutine.

---

### 3. Park

To start parking, the car first turns its wheels left, rotating its body to about a 45° angle with respect to the parking space. It does this by noting its initial orientation and continuing to steer until the desired angle is reached.

After achieving the right angle, the car begins backing into the space in a straight line. It keeps an eye on the rear LIDAR, stopping when it detects that it has reached the appropriate depth (using a helper function defined earlier in the code).

Finally, the car turns its steering in the opposite direction while continuing to reverse gently. This allows it to align itself parallel to the curb and finish the maneuver neatly — **a fully autonomous parallel park!**

---

## Video Results

[ADD VIDEO HERE]

A short demo video will go here showing the car detecting a space, adjusting its angle, and successfully parking without any human input.

---

Would you like me to make this slightly more formal (e.g., suitable for a research or project report) or keep this casual, “README for a GitHub project” tone?

