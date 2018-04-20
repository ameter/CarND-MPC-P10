# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program
___

### The Model

This project implements a kinematic model....

#### Vehicle State
    * x    x position
    * y    y position
    * ψ    orientation angle
    * v    velocity
#### Control inputs (actuators)
    * δ    steering angle
    * a    throttle (positive) and brakes (negative) between 1 and -1

#### Model update equations (to predict next state based on actuator values)
    * x = x + v * cos(psi) * dt     (dt is delta time)
    * y = y + v * sin(psi) * dt
    * ψ  = ψ  + (v / Lf) * δ  * dt   (Lf measures size of vehicle (distance between center of mass and front axle))
    * v = v + a * dt

### Timestep Length and Elapsed Duration (N & dt)

The timestep and elapsed duration were chosen through trial and error.  A timestep of 1 second (with 10 steps (N=10) and delta time of .1 (dt=.1)) was selectected because I attempted to find the longest timestep that maintained good control, and when I incrested much more, the car would crash.  The values could potentially be further refined with additional testing, but this combination produced acceptable results and allowed the car to successfully navigate the track.

### Polynomial Fitting and MPC Preprocessing

Waypoints were fitted to a third degree polynomial.  The following preprocessing steps were completed...
    1. The car was shifted to (0,0) and reference angle was rotated to 0 to corespond to the map
    2. The cross track error (CTE) and error psi (epsi) (difference between our angle and what we want) were computed.
    3. Latency was factored in (see the following section for a detailed description of this step).

### Model Predictive Control with Latency

During preprocessing, latency of 0.1 seconds (100 milliseconds) was factored in by applying the following...

    * latency = 0.1 (set latency to 100 milliseconds)
    * Lf = 2.67
    * latency_x = v * latency;
    * latency_y = 0;
    * latency_v = v + throttle_value * latency;
    * latency_psi = -v * steer_value / Lf * latency;
    * latency_cte = cte + v * sin(epsi) * latency;
    * latency_epsi = epsi - v * steer_value / Lf * latency;

Note: Lf (the length from front to CoG that has a similar radius) was provided, but is obtained by measuring the radius formed by running the vehicle in the simulator around in a circle with a constant steering angle and velocity on flat terrain.

Lf was tuned until the the radius formed by the simulating the model presented in the classroom matched the previous radius.

---

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
