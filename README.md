# Vehicle Path Planning Algorithm
In this project, our goal is to design a path planning algorithm that is able to a car around a simulated highway scenario, including traffic and given waypoints, telemetry, and sensor fusion data. Our goal is to safely navigate around the virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit

---

## The path planning algorithms consist of three parts:

### Prediction [line 255 to line 293](./src/main.cpp#L255)
This part of the code deal with the telemetry and sensor fusion data. It intents to reason about the environment.
In the case, we want to answer three questions:
- Is there a car blocking the way in front of us?
- Is there a car to the right of us making a dangerous lane change?
- Is there a car to the left of us making a dangerous lane change?

These questions are answered by
- Calculating the lane each car is taking.
- Calculating the position each car will be at, at the end of the last plan trajectory.
- Considering a car "dangerous" when its distance ahead or behind our car is less than 30 meters.

### Behavior [line 295 to line 318](./scr/main.cpp#L295)
This part decides what to do:
  - If there's a car blocking the way in front of us, do we change lanes?
  - Do we speed up or slow down?

Based on the prediction of the situation we are in, We do the following:
- Increase the speed.
- Decrease the speed
- Make a lane change when it is safe.
Instead of increasing the speed at this part of the code, a `speed_diff` is created to be used for speed changes when generating the trajectory in the last part of the code.
This approach makes the car more responsive acting faster to changing situations, like if a car in front of us breaks and we were about to have collision.

### Trajectory [line 320 to line 419](./scr/main.cpp#L320)
This code does the calculation of the trajectory based on:
- The speed and lane output from the behavior.
- Car coordinates.
- Past path points.

First, the car coordinates and past path points (or only the car coordinates if there are no previous trajectory) are used in conjunction with three points at a far distance to initialize the spline calculation.
To make the work less complicated to the spline calculation based on those points, the coordinates are transformed (shift and rotation) to local car coordinates (lines 367 to 374).

In order to ensure more continuity on the trajectory (in addition to adding the last two point of the pass trajectory to the spline adjustment), the pass trajectory points are copied to the new trajectory.
The rest of the points are calculated by evaluating the spline and transforming the output coordinates to back from local coordinates.
- Worth noticing the change in the velocity of the car (lines 395 to 401). The speed change is decided on the behavior part of the code, but it is used in that part to increase/decrease speed on every trajectory points instead of doing it for the complete trajectory.

---

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
