# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program

[//]: # (Image References)

[image1]: ./images/pathplanning.gif "Working Sample"
[image2]: ./images/FiniteStateMachine.jpg "FSM"
[image3]: ./images/CrossingTheMiles.png "Cross Miles"


![Working Sample][image1] 

### Goals of the project

* Drive a car safely around a highway without any incident
* Use sensor fusion data to safely maneuver the vehicle among the other traffic.
* Use cost functions to choose the most optimal tragectory from several available tragectory options

### Simulator.
You can download the Term3 Simulator which contains the Path Planning Project from the [releases tab (https://github.com/udacity/self-driving-car-sim/releases).

### Project Rubix
You can refer to the project rubix [here](https://review.udacity.com/#!/rubrics/1020/view)


### Implementation Details

This project is implemented in a simple one source file. The entire logic is implemented inside the main function of the main.cpp file between lines 313 to 558. The implemation is based on Finite State Machine(FMS) architecture. FMS uses 2 simple cost functions in order to choose the optimal next state.

### Finite State Machine

![FSM][image2]

The image above describe the Finite State Machine used in this implementation. It includes 3 states. Keep Lane(KL), Lane Change Left (LCL) and Lane Change Right (LCR).

#### Keep Lane
This is the initial state of the machine. According to the associated cost of the next state, this state can stay the same or change to 2 other states LCL and LCR. The KL state will remain still unless changing to any other state is safe and less costly than maintaining the KL state.  Once the Initial KL state change to LCL or LCR, the machine can return to KL state by keeping the same that It changed to. 

#### LCL
This is the State that Machine can change from KL state. In this action, the vehivle will change lane to the left, in our case, the more desirable state change. Once the vehicle moved to the left lane, it can return to KL state where it keeps the left lane untill a lower cost action available. 

#### LCR
This is the State that Machine can change from KL state. In this action, the vehivle will change lane to the right. Once the vehicle moved to the right lane, it can return to KL state where it keeps the right lane untill a lower cost action available. 

### Cost Functions
In this implementation, We use only 2 simple cost functions. 

#### Speed Cost
The implementation of this cost functions can be found in the lines 176 - 183 in the main.cpp. Here, the vehicle is penalizes for taking lower speeds. The desirable speed is closer to the spped limit. In this case, The desirable speed is 49.5. 

#### Lane cost
The implementation of this cost function can be found in the lines 187 - 199 in main.cpp. Here, Vehicle will be encourage to use the leftmost lane whenever possible. This lane is the fastest lane, hense the most efficient. 

The total cost will the total of these 2 cost functions and the vehicle will choose the lowest cost state as the next bext action to perform. 

The function get_cost() in lines 201 to 273 uses a little bit of logic to call these cost functions for given action/state. This function will return a highest possoble cost if a given action is impossible or not safe. 

### Path Generation and Speed Control

#### Path Generation

Once the desired action was choosen from the previous section, 3 waypoints are generated in the desired trajectory, each 30m apart. Then we use these 3 waypoints to generate a spline using the [spline library](http://kluge.in-chemnitz.de/opensource/spline/) to generate smooth transitioning points in between the main 3 waypoints. Another option is to generate polynomials for the 3 waypoints as discussed in the class. ( Lines 451 - 558 in main.cpp)

#### Speed Control

We graduallt increase or decrease the speed from current speed to the reference velocity from the increments of 0.225 to void jerk and exceeding the max acceleration of the vehicle. 


Below are few details to build the project yourself. 

#### The map of the highway is in data/highway_map.txt
Each waypoint in the list contains  [x,y,s,dx,dy] values. x and y are the waypoint's map coordinate position, the s value is the distance along the road to get to that waypoint in meters, the dx and dy values define the unit normal vector pointing outward of the highway loop.

The highway's waypoints loop around so the frenet s value, distance along the road, goes from 0 to 6945.554.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.

Here is the data provided from the Simulator to the C++ Program

#### Main car's localization Data (No Noise)

["x"] The car's x position in map coordinates

["y"] The car's y position in map coordinates

["s"] The car's s position in frenet coordinates

["d"] The car's d position in frenet coordinates

["yaw"] The car's yaw angle in the map

["speed"] The car's speed in MPH

#### Previous path data given to the Planner

//Note: Return the previous list but with processed points removed, can be a nice tool to show how far along
the path has processed since last time. 

["previous_path_x"] The previous list of x points previously given to the simulator

["previous_path_y"] The previous list of y points previously given to the simulator

#### Previous path's end s and d values 

["end_path_s"] The previous list's last point's frenet s value

["end_path_d"] The previous list's last point's frenet d value

#### Sensor Fusion Data, a list of all other car's attributes on the same side of the road. (No Noise)

["sensor_fusion"] A 2d vector of cars and then that car's [car's unique ID, car's x position in map coordinates, car's y position in map coordinates, car's x velocity in m/s, car's y velocity in m/s, car's s position in frenet coordinates, car's d position in frenet coordinates. 

## Details

1. The car uses a perfect controller and will visit every (x,y) point it recieves in the list every .02 seconds. The units for the (x,y) points are in meters and the spacing of the points determines the speed of the car. The vector going from a point to the next point in the list dictates the angle of the car. Acceleration both in the tangential and normal directions is measured along with the jerk, the rate of change of total Acceleration. The (x,y) point paths that the planner recieves should not have a total acceleration that goes over 10 m/s^2, also the jerk should not go over 50 m/s^3. (NOTE: As this is BETA, these requirements might change. Also currently jerk is over a .02 second interval, it would probably be better to average total acceleration over 1 second and measure jerk from that.

2. There will be some latency between the simulator running and the path planner returning a path, with optimized code usually its not very long maybe just 1-3 time steps. During this delay the simulator will continue using points that it was last given, because of this its a good idea to store the last points you have used so you can have a smooth transition. previous_path_x, and previous_path_y can be helpful for this transition since they show the last points given to the simulator controller with the processed points already removed. You would either return a path that extends this previous path or make sure to create a new path that has a smooth transition with this last path.

## Tips

A really helpful resource for doing this project and creating smooth trajectories was using http://kluge.in-chemnitz.de/opensource/spline/, the spline function is in a single hearder file is really easy to use.

---

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


## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to ensure
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

