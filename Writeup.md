# **Path Planning Project**

[//]: # (Image References)

[image1]: ./md_images/equations.png "Kinematic Equations"
[image2]: ./md_images/equations_latency.png "Equations with Latency"

The approach of the project is based heavily on the project walkthrough.

Frenet coordinates (s, d) are used to represent the vehicle’s position on a road than traditional Cartesian coordinates (x, y). The **s** coordinate represents _distance along the road_ (aka longitudinal displacement) while **d** coordinate represents _side-to-side position from the center (yellow) dividing line on the road_ (aka lateral displacement).

The track is approximately 6947 meters, thus s value varies from 0 to 6946. The track comprises of 6 lanes, with a yellow line that divides the lanes in each direction. The d value indicates the distance from the center (yellow) dividing line, where each lane is 4 meters wide. Each direction has 3 lanes, namely, left, center and right, and the d values for each lane are:
* left lane: 0 < d < 4
* center lane: 4 < d < 8
* right lane: 8 < d < 12

Hence, the Frenet coordinates (s, d) specifies a vehicle’s position on the road.

To generate paths, Frenet coordinates (s, d) and spline interpolation were used. The purpose of spline interpolation is to estimate the location of points between known waypoints. 

The project consists of 3 parts:
1. Sensor fusion: understand (nearby) traffic
2. Path planning: determine lane change behavior
3. Trajectory generation: construct vehicle’s trajectory

### Sensor Fusion

The simulator provides information on the cars that are travelling in the same direction as the Ego car. It does not provide information on cars travelling in the opposite direction. The Ego car starts in the center lane and then drives on its own based on the nearby traffic. 

It is necessary to analyze the sensor fusion data to determine if there is a car in front of Ego car, within 30 m. If yes, slow down the speed of Ego car to match the speed of the front car. Otherwise the Ego car will maintain a reference speed of 49.5 unless it is obstructed by traffic. 

The sensor fusion is implemented in lines 342 – 360 of main.cpp.

### Path Planning

The changing of lane has the following behavior: Ego car would stay in its lane, unless there’s traffic ahead that prevents it from driving its reference speed of 49.5 mph. The Ego car will attempt to find a lane that it can safely move into. 
1. Stay in the lane and drive at reference speed for as long as possible
2. If there is traffic ahead, request for a lane change
   1. First, check the traffic in the adjacent lane left of Ego car lane. If Ego car is not already in the leftmost lane, find the closest front and back gaps in the adjacent left lane. If the front gap is more than 30 m and back gap is more than 18 m, set target lane to the adjacent lane left of Ego car lane
   2. If the above lane isn’t available to change into, check the traffic in the adjacent lane right of Ego car lane. If the Ego car is not already in the rightmost lane, find the closest front and back gaps in the adjacent right lane. If the front gap is more than 30 m and back gap is more than 18 m, set target lane to the adjacent lane right of Ego car lane

The path planning is implemented in function `changeLane()` and line 362 – 386 of main.cpp

### Trajectory Generation

Three waypoints, evenly spaced at 30 m intervals and ahead of the Ego car’s reference distance, are used for interpolating a smooth path between them using spline interpolation. The three waypoints are converted to local coordinates (shift and rotation), and interpolated points are evenly spaced such that each point is traversed in 20 ms. The points are then converted to Frenet coordinates and sent to the simulator.

To obtain acceleration below 10 m/s<sup>2</sup> and jerk below 10 m/s<sup>3</sup>, a constant acceleration/deceleration is added to the Ego car speed.

The trajectory generation is implemented in lines 389 – 447 of main.cpp.

The adjustment on Ego car speed is implemented in lines 423 – 426 of main.cpp

### Results

The Ego car can drive itself safely along the entire ~7 miles track without incident and collision, stays in its lane except for the time between changing lanes, and travelling within the following limits:
* Max speed – 50 mph
* Max acceleration – 10 m/s<sup>2</sup>
* Max jerk – 10 m/s<sup>3</sup>

Here's the [link](https://www.dropbox.com/s/pqzafbl0ylfrc1m/PathPlanning.mp4?dl=0) to the video result.