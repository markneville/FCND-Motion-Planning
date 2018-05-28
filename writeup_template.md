## Project: 3D Motion Planning
![Quad Image](./misc/enroute.png)

---


# Required Steps for a Passing Submission:
1. Load the 2.5D map in the colliders.csv file describing the environment.
2. Discretize the environment into a grid or graph representation.
3. Define the start and goal locations.
4. Perform a search using A* or other search algorithm.
5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0].
7. Write it up.
8. Congratulations!  Your Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1534/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`
The most significant difference between backyard_flyer_solution.py and the modified version called motion_planning.py is the addition of the Planning state and plan_path at line 114. When the drone is at the arming state the state_callback method transitions to the planning state, and the method plan_path is then executed. This methods responsibility is to plan the necessary waypoints for the drone to reach its predefined target location.

The plan_path method consists of;

Defines the initial values of the target altitude at line 117 and safety distance at line 118. Both values were initially set to 5, and the target altitude is stored the target_position[2] Numpy array at line 120.

The obstacle map 'colliders.csv' is loaded at line 133.

The grid is calculated at line 136 using the method create_grid from the module planning_utils.py.

The goal grid is offset 10 north and 10 east from the local position on line 143.

The A* search algorithm executed on line 151 using the a_star method from the module planning_utils.py is used to find the path to the goal

The waypoints are generated at line 155, and they are sent to the simulator using the method send_waypoints at line 157.


### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
At line 125 the colliders.csv file is opened, and the first row is read to extract the Latitude and Longitude using readline() and converted to floating point values. At line 131 these values are used to set the home position with set_home_position.

#### 2. Set your current local position
At line 134 global_position = [self._longitude, self._latitude, self._altitude] retrives the current global postion  and at line 137 current_local_position = global_to_local(global_position, self.global_home) determines the current local position.
#### 3. Set grid start position from local position
At Line 151 grid_start = (int(current_local_position[0] - north_offset), int(current_local_position[1] - east_offset)) the north_offset and east_offset are the center of the grid. This center point is subtracted from the the current local position in the map reference frame to get the start position in the grid reference frame instead of the map center.
#### 4. Set grid goal position from geodetic coords
The goal location is set on the grid from the command line arguments (--target_lat, --target_lon, --target_alt) at lines 195 - 197 and line 200

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
The diagonal motions on the grid method of A* is achieved by adding the Actions NE, NW, SE, and SW with a cost of sqrt(2) at lines 59-62 in planning_utils.py. Valid_actions() was also modified to remove NE, NW, SE, or SW when it would move off the grid, or collide at lines 92-99 also in planning_utils.py.
#### 6. Cull waypoints 
The calculated path is pruned with a collinearity function to eliminate unnecessary waypoints at lines 159-180 in planning_utils.py and is derived from the code presented in lectures
### Execute the flight
#### 1. Does it work?
It works! But sometimes the drone goes too fast and slightly misses the waypoints. 

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.
  
# Extra Challenges: Real World Planning

For an extra challenge, consider implementing some of the techniques described in the "Real World Planning" lesson. You could try implementing a vehicle model to take dynamic constraints into account, or implement a replanning method to invoke if you get off course or encounter unexpected obstacles.


