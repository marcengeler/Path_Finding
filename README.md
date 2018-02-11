# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program
   
## First Tests

To start off the problem, I followed the project description video to test the different behaviours of the car. This gave a good insight on how to tackle the later problems imposed by obstructing vehicles or how to design the finite state machine required.

## Model Generation

### Jerk // Acceleration restrictions

In order to have a smooth driving behaviour splines can help a lot. Because we use the 2 old points of the vehicle, the spline will have a derivative constraint on .

