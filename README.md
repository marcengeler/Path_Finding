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

### Safety and Closeness

While discussing the approach taken above, two words were mentioned. Safety and Closeness to other vehicles. In oder to get a clearer view how those two metrics were implemented, this section will discuss this topic.

First of all, a safe distance is considered to be a distance of 2 seconds in time. To translate this distance it suffices to take the velocity [m/s] * 2 [sec]. This is the minimal safety distance, or reference distance the car should have to the front vehicle. In order to decelerate accordingly, any vehicle in the same lane within this reference distance is considered to be close, and the car will start decelerating.

The closeness parameter is defined as a linear parameter which is 0, when the vehicle is reference_distance away from the car and 1, if the car collides with the front vehicle. Using this parameter, the deceleration taken into action, when driving upon a front vehicle is proportional to this closeness parameter. This ensures a smooth deceleration curve, rather than maximum deceleration everytime a vehicle is found in front of us.

The same reference distance is taken into account before changing lanes.

|--- reference distance ------ ||||| --------- reference distance + 30 m ---------------------|
|------------------------------CAR ------------reference distance ---- FRONT CAT -------------|
|--- reference distance ------ ||||| --------- reference distance + 30 m ---------------------|

in order to pass in either lane left or right, there needs to be a space equal to this reference distance. The front facing clearance needs to be 30m larger to avoid frequent lane changes in "traffic jams". This could be changed with checking the vehicle speed, however, the simulation doesn't seem to follow any logic speed distribution, and overtaking on the right hand side seems to be allowed, so this was not integrated.

### Lane Shifting

If both lanes are considered save, the left hand lane is preferred. For the most left lane and the most right lane, a car to the left, or to the right is always detected. This avoids a lane shift in prohibited space. As soon as a lane shift is engaged, the lane number is changed in the algorithm, which will switch the desired position to the desired lane, and the splines will interpolate accordingly. However, a new car may be detected during the lane shift, due to this fact, a new state was introduced. This lane shifting state will stay true until the car is within 0.5m of the center of the road. During this lane shifting state no further lane shift will be possible.
