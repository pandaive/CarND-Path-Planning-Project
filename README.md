# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program
   
### Simulator.
You can download the Term3 Simulator which contains the Path Planning Project from the [releases tab (https://github.com/udacity/self-driving-car-sim/releases/tag/T3_v1.2).

### Goals
In this project your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. You will be provided the car's localization and sensor fusion data, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.

## Dependencies

* cmake >= 3.5
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```

### Implementation

Implementation of the path planning can be found in main.cpp in lines 247-458. It consist of sensor fusion data analysis, making a decision on next move and calculating the path of the car.

![image.png]

# Sensor fusion data

Each coming frame the data of cars around is being checked. If there is a car ahead, the distance between it and Ego car is calculated (lines 266-279). Reaction is triggered if a car ahead is in a closer distance than 30 meters. The cost of keeping lane is calculated based on how close it is - so how much braking it requires to follow it. The good thing about is that this cost function doesn't penalize following a car at steady, reasonably speed. Here we are also adapting the speed, to slow down if the car is too close (less than 15 meters) or drives with a lower speed tha 45mph.

Next, if the car is too close, it's time to check other lanes. If we're on right (left accordingly) lane, the lane change to the right (left acc.) is automatically set to 1000 as this would end up in a car leaving the road.

The cost function of changing lane is calculated based on all vehicles seen in front of the car in distance up to 60 meters, proportionally to the distance between them and Ego car. The result of it is that the car will choose the less busy lane if that's possible. Though if the other car is in distance closer than 30 it automatically rise the cost of the lane change to avoid collisions. Also, the value of 3 is given by default to the cost of lane change, to favor keeping the same lane without a good reason to change it. The code is in lines 286-337.

# Decision

If a car is spotted ahead, cost functions are taken into account so that the car decides on the next move. I restricted the possibility of lane change only to the situations, when the car is driving at least average speed (more than 30mph) and that it did not change lane in last two lanes, as quick changing lanes might affect the perception of the situation on the road as well as slow lane change might end up with other car running into Ego car. The code is in lines 339 - 360. Max speed is limited to 44 mph.

# Calculating the path of the car
The path of the car is calculated using up to 2 previous paths and Frenet coordinates of the car. Taking into account 3 estimated coordinates of the desired path at next 30, 60 and 90 meters, translated from Frenet, the shift based on car x and y positions and yaw is calculated and then used with spline library to calculate way of the lane change. Target distance is 30 meters ahead and next waypoints are calculated for each next 0.2s step. Afterwards, next x and y points are fed into the simulator.
The code for those calculations is in lines 362-455.

