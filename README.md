# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program
   
## First Tests

To start off the problem, I followed the project description video to test the different behaviours of the car. This gave a good insight on how to tackle the later problems imposed by obstructing vehicles or how to design the finite state machine required.

## Model Generation

### Jerk // Acceleration restrictions

In order to have a smooth driving behaviour splines can help a lot. Because we use the 2 old points of the vehicle, the spline will have a derivative constraint on the beginning of the curve. This constraint will ensure, that there are no rapid changes in the steering wheel angle, and will actually allow us for a smooth lane transitioning as well.

In order to keep the acceleration in check, rather than setting speed directly, we can go and set the acceleration to a specific value. because we know the absolute value of the acceleration, and the time increment, we can also ensure that the jerk is within it's limits, because the maximum jerk will be:

max_acc / 0.02 sec

Using a predefined value of 0.224 m/s^2 for the acceleration will keep both values within the limit. To keep the speed limit, no acceleration is allowed above a predefined max_vel, which is set to 49.5 mph.

### Collision Detection

In order to not collide with other vehicles, we have to get the data from the sensor fusion vector. This gives us information on all other vehicles on the road. Using Frenet coordinates, we can easily identify which lanes vehicles are on, and if there is a vehicle ahead of us on our lane.

In order to slow down, the vehicle closeness is defined as distance / reference_distance, whereaas reference_distance defines the distance we want to keep in order to stay save. as soon as this value gets too small, we can actually decelerate at a higher pace, to keep our velocity in a save region. We can also define a distance relative to our velocity, which often increases the safety at higher speeds.

### Lane change state machine

In order to change lanes, we need to be aware of vehiles to the left or right of us. To do this, we check any vehicles which are close to us in lanes left and right from us (expect the outer lanes, which reduce to either left or right of us) and follow the same restrictions as the collision detection. 

Of course, in order to change lanes, we also need a clearance to the back, so the constaint is a bit tighter, before we can deceide to change lanes. As soon as we have a vehicle in front of us, and the adjacient lanes are clear, we can issue a lane changing command.

Because we do a spline interpolation between our current position and our desired future point, the lane transision will be smooth.