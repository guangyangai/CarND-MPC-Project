# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---
## Summary 
### Description of the model (including the state, actuators and update equations)
The MPC model is an optimization model which optimizes the total cost given predicted locations of the vehicle and certain constraints. The total cost includes tracking error, control cost and control rate cost. The objective is to find optimal control variable or actuators so that the vechicle can run on the road. 

*State variables*

px (x coordinate), py(x coordinate), psi(orientation), v(velocity), cte(location tracking error), epsi(orientation tracking error);

Actuator variables: steering (\delta) and throttle (a)

*Constraints*

<a href="https://www.codecogs.com/eqnedit.php?latex=x_{t&plus;1}=x_t&plus;v_tcos(\psi_t)*dt" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x_{t&plus;1}=x_t&plus;v_tcos(\psi_t)*dt" title="x_{t+1}=x_t+v_tcos(\psi_t)*dt" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=y_{t&plus;1}&space;=&space;y_t&space;&plus;&space;v_t&space;sin(\psi_t)&space;*&space;dt" target="_blank"><img src="https://latex.codecogs.com/gif.latex?y_{t&plus;1}&space;=&space;y_t&space;&plus;&space;v_t&space;sin(\psi_t)&space;*&space;dt" title="y_{t+1} = y_t + v_t sin(\psi_t) * dt" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\psi_{t&plus;1}&space;=&space;\psi_t&space;&plus;&space;\frac&space;{v_t}&space;{&space;L_f}&space;\delta_t&space;*&space;dt" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\psi_{t&plus;1}&space;=&space;\psi_t&space;&plus;&space;\frac&space;{v_t}&space;{&space;L_f}&space;\delta_t&space;*&space;dt" title="\psi_{t+1} = \psi_t + \frac {v_t} { L_f} \delta_t * dt" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=cte_{t&plus;1}&space;=&space;f(x_t)&space;-&space;y_t&space;&plus;&space;(v_t&space;sin(e\psi_t)&space;dt)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?cte_{t&plus;1}&space;=&space;f(x_t)&space;-&space;y_t&space;&plus;&space;(v_t&space;sin(e\psi_t)&space;dt)" title="cte_{t+1} = f(x_t) - y_t + (v_t sin(e\psi_t) dt)" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=e\psi_{t&plus;1}&space;=&space;\psi_t&space;-&space;\psi{des}_t&space;&plus;&space;(\frac{v_t}&space;{&space;L_f}&space;\delta_t&space;dt)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?e\psi_{t&plus;1}&space;=&space;\psi_t&space;-&space;\psi{des}_t&space;&plus;&space;(\frac{v_t}&space;{&space;L_f}&space;\delta_t&space;dt)" title="e\psi_{t+1} = \psi_t - \psi{des}_t + (\frac{v_t} { L_f} \delta_t dt)" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=-25&space;$^\circ$&space;\leq&space;\psi&space;\leq&space;25$^\circ$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?-25&space;$^\circ$&space;\leq&space;\psi&space;\leq&space;25$^\circ$" title="-25 $^\circ$ \leq \psi \leq 25$^\circ$" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=-1&space;\leq&space;a&space;\leq&space;1" target="_blank"><img src="https://latex.codecogs.com/gif.latex?-1&space;\leq&space;a&space;\leq&space;1" title="-1 \leq a \leq 1" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=-1&space;\leq&space;\delta&space;\leq&space;1" target="_blank"><img src="https://latex.codecogs.com/gif.latex?-1&space;\leq&space;\delta&space;\leq&space;1" title="-1 \leq \delta \leq 1" /></a>

*Objective function*

minimize tracking error:

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum&space;w_1cte^2&space;&plus;&space;w_2e\psi^2&space;&plus;&space;w_3(v-v_{ref})^2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum&space;w_1cte^2&space;&plus;&space;w_2e\psi^2&space;&plus;&space;w_3(v-v_{ref})^2" title="\sum w_1cte^2 + w_2e\psi^2 + w_3(v-v_{ref})^2" /></a>

minimize steering and throttle, and very importantly big turns when vehicle is running fast 

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum&space;w_4\delta^2&space;&plus;&space;w_5a^2&space;&plus;&space;w_6(v*\delta)^2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum&space;w_4\delta^2&space;&plus;&space;w_5a^2&space;&plus;&space;w_6(v*\delta)^2" title="\sum w_4\delta^2 + w_5a^2 + w_6(v*\delta)^2" /></a>

minimize changes in steering and throttle (hard break or pressing hard on pedals), 

<a href="https://www.codecogs.com/eqnedit.php?latex=\sum&space;w_7(\delta_{t&plus;1}-\delta_t)^2&space;&plus;&space;w_8(a_{t&plus;1}-a_t)^2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sum&space;w_7(\delta_{t&plus;1}-\delta_t)^2&space;&plus;&space;w_8(a_{t&plus;1}-a_t)^2" title="\sum w_7(\delta_{t+1}-\delta_t)^2 + w_8(a_{t+1}-a_t)^2" /></a>

### Choice of Parameters
Since the road orientation (the curvature) changes quite often, I chose 20 time steps (N) for the project with dt of 0.1s, that seems to be a reasonable choice with enough estimations going forward. 

Different from the lecture, since the track has quite a few big turns, for this project, in order to make the vehicle finish one lap, the throttle and big angle turns need to be penalized less.

### Polynomial fitting
Since the fitted coordinates are from the vechicle's perspective, measured locations of the lane marks should be transformed to vehicle coordinates. After the transform, a third order polynomial is fitted to get the cte tracking and orientation error.

### Handling Latency
To compensate latency, state is predicted after a certain latency. And that state is passed to the optimization solver to get the control variable. 

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
