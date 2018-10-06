# MPC Control Project

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

Self-Driving Car Engineer Nanodegree Program

Overview

---

[//]: # (Image References)

[video]: ./results/result_video.gif "Video showing loop around track"
[model_eq]: ./results/model_eq.gif "Kinematic model equation"
[video_high_kp]: ./results/High_Kp.gif "Video with high Kp"
[Video_low_kd]: ./results/low_Kd.gif "Video with low Kd"
[Video_high_kd]: ./results/high_kd_2.gif "Video with high Kd"

**PROJECT DESCRIPTION**
---
In this project I implemented Model Predictive Control(MPC) to drive the car around the lake race track in a simulated envrionment. I calculated the cross-track error and accommodated for 100 millisecond latency of actuations command in addition to connection latency. Basic principle of MPC is to control a process while satisfying a set of constraints. It boils downs to become an optimization problem where goal is to follow a desired trajectory the vehicle must follow.

![alt text][video]

**Project Goals**

The goals / steps of this project are the following:
- Build a MPC controller in C++
- Tune controller hyperparameters
- Calculate cte and epsi error along with total cost
- Compensate for latency
- Vehicle succesfully drives a lap around the track without tires leaving the driveable portions

The simulation is overlayed with two lines. The yellow is a polynomial fitted to waypoints and the green line represents the x and y coordinates of the MPC trajectory. This trajectory is the predicted path solved by the controller with a goal of keeping cost low. 

The vehicle model is built into the MPC controller and the state of the vehicle at any given time can be depicted as follows:

- **x**: Position X
- **y**: Position Y
- **psi**: Orientation
- **v**: Current Velocity
- **cte**: Cross Track Error (Deviation from desired path)
- **epsi**: Orientation Error

Inputs from the simulator:

- **ptsx**: A list of x positions in map coordinate system of the trajectory
- **ptsy**: A list of y positions in map coordinate system of the trajectory
- **x**: The current x position of the vehicle in map coordinate system
- **y**: The current y position of the vehicle in map coordinate system
- **psi**: The vehicle orientation in radians, with respect to the global map
- **speed**: The current speed in mph
- **steering_angle**: The steering angle in radians
- **throttle**: The current throttle value (in the range [1, -1]) 

Outputs to the simulator:

- **steering_angle**: Next steering angle value for actuators [1, -1]
- **throttle**: The throttle value to submit next (in the range [1, -1])
- **next_x**: A list of x positions for the reference trajectory in vehicle coordinate system
- **next_y**: A list of y positions for the reference trajectory in vehicle coordinate system
- **mpc_x**: A list of x positions computed by the MPC for the minimized cost trajectory
- **mpc_y**: A list of y positions computed by the MPC for the minimized cost trajectory

Vehicle Motion Model:

The state vector is updated from time t to time t+1 with the following equations:

![alt text][model_eq]

**Lf**: Distance between the center of mass and front axle.

**Results**

The parameters chosen include a time_step of 0.1 sec and a N of 10 which results in a timeframe from 1 sec. This means that the MPC controller plans 1 sec into the future at any given time. Choice of weights in the cost function was done based on importance. So critical parameters such as cte and epsi were given a larger weight value. 

The time step of 1 sec was chosen based on experimentation it was found that larger time periods resulted in the car driving off the road. 

| Parameter  | Value| Comment
|------------|------|--------------------------------------------|
| cte        | 2000 | Penalty for high cte error (deviation)     |
| epsi       | 2000 | Penalty for high epsi error (orientation)  |
| v_start    | 1    | No penalty for high velocity               |
| delta      | 5    | Penalty for using actuators                |
| a          | 5    | Penalty for using actuators                |
| Diff_delta | 200  | Penalty for sudden changes in angle        |
| Diff_a     | 10   | Penalty for sudden changes in throttle     |


## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
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
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Tips

1. It's recommended to test the MPC on basic examples to see if your implementation behaves as desired. One possible example
is the vehicle starting offset of a straight line (reference). If the MPC implementation is correct, after some number of timesteps
(not too many) it should find and track the reference line.
2. The `lake_track_waypoints.csv` file has the waypoints of the lake track. You could use this to fit polynomials and points and see of how well your model tracks curve. NOTE: This file might be not completely in sync with the simulator so your solution should NOT depend on it.
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)
4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)
5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/b1ff3be0-c904-438e-aad3-2b5379f0e0c3/concepts/1a2255a0-e23c-44cf-8d41-39b8a3c8264a)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).
