# CarND-Controls-MPC

---

The Model Predictive controller (MPC) is an iterative method that computes the next few states of a given system based on cost-minimization. The input to the controller are the intial state of the system (in our case vehcicle position, orientation, velocity etc) a cost function, a path/trajectory and set of constraints. The model then predicts the next few states of the vehicle that best follow the trajectory while minimizes the given cost function. We only use the first predicted steering angle and throttle to move our car in the simulator and then feed back the next state back to the system to repeat above steps again.

In this project, we implemented MPC using the Udacity's simulator.

## Model

The model consist of following **state** variables:

* x & y position of the car in map co-ordinate system.
* the orientation of the car 
* The Velocity of the car
* The cross track error (CTE) that measure how far away the car is from the center of the track. 
* The oreintation error that measure the difference between desired oreintation and actual orientation. 

Additionally we have *two actuators* variables:

* The car steering angle
* The throttle of the car (single variable for modeling both the acceleration and break, negative values of this variable indicate breaking)

We use a general kinematics model to model the update of vehicles state. 

## Length and Duration

Two hyper parameters: N (number of timesteps) and dt (elapsed time between actuations) were hand optimized through experimentation. We picked N to be 10 and dt to be 0.1. The other values were tried were N=15, N=20 while keeping the dt fixed. The larger N value resulted in poor results specially at higher reference speed. A higher N value introduce more variables for the optimizer to work with and that can result in higher latencies in sending the command to the vehicle. A too low of N can also cause the system to get poor results since we are focusing on too narrower of a path in fron of the car.  

## Handling Latency

There is typically a delay in command and actuation. This delay can affect on the state of the vehicle as it might have moved a little further than we expected when pass the initial state for the next iteration to optimizer. This delay can be handled by predicting the next state using the current state and a time delay. In our case, we used a time delay of 100 ms. 

```
 double latency = 0.1;

 double steer_value = j[1]["steering_angle"];
 double throttle_value = j[1]["throttle"];

 px = px + v * cos(psi) * latency;
 py = py + v * sin(psi) * latency;
 psi = psi - v * steer_value / 2.67 * latency;
 v = v + throttle_value * latency;
```
## Result

Here is the final video. 

<p align="center">
	<img src="./video.gif">
</p>

