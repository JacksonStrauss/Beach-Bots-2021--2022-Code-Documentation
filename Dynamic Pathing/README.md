## Dynamic Pathing

A key component of every annual FTC challenge is the 30 seconds of "autonomous mode" at the beginning of a match. In this mode, the robot is not controlled
by a person but rather runs a set of pre-programmed tasks. After the 30 seconds are up, the robot is then allowed to be controlled manually.

Programming the robot to perform automated tasks can be challenging because one has to keep track of the robot's position the entire time. If the robot's location tracking system is not accurate then cumulative errors will build and the robot's positioning system will become increasingly worse as more time passes. This is similar to the issue found when using the [dead reckoning navigation technique](https://en.wikipedia.org/wiki/Dead_reckoning).

We utilized a library called [Road Runner](https://learnroadrunner.com/#frequently-asked-questions) which allowed us to use the combination of an [IMU](https://en.wikipedia.org/wiki/Inertial_measurement_unit) (internal measurement unit) and [odometry dead wheels](https://gm0.org/en/latest/docs/common-mechanisms/dead-wheels.html) to determine the location of our robot in autonomous mode and thus create custom trajectories that the robot could follow. Road Runner generates its own pathing functions with custom PID control to ensure that the robot travels smoothly but rapidly from one point to another.

An issue that we faced using Road Runner was that we could only create a trajectory for the robot if we knew the exact starting and end positions of the robot. Our robot could keep track of its location, but if we ordered it to travel to a certain position, it would not be able to accurately travel to that position. In hindsight, we believe that we did not correctly tune Road Runner and so the robot was unable to follow the instructions given. But we still needed the robot to travel to the desired position...

The preliminary solution was to tell the robot to travel further than it really should have been traveling. For example, if the robot needed to travel to (10, 10) from (0, 0) but was only making it to (8, 8), then we could tell the robot to try and travel to (12, 12). This sort of worked and the robot would usually end up around (10, 10) but this was, to put it plainly, a bad way of solving the problem. We weren't sure where our robot was going to end up after we gave it certain commands to move and this posed a problem for creating our trajectories. Because of the constraints of Road Runner, we had to create our trajectories before the program was run. As such, we couldn't rely on the end position of our robot after each trajectory and the compounding errors became a major issue.

The solution was to dynamically create trajectory paths while the robot was running in autonomous mode. We could now create trajectories whenever we wanted based on our true position rather than having to define all our trajectories before the program could run. 

```java
public TrajectorySequence createTrajectory(SampleTankDrive drive) {

  TrajectorySequence trajectory1 = drive.trajectorySequenceBuilder(drive.getPoseEstimate())
    .splineTo(new Vector2d(-10, -64), Math.toRadians(180))
    .build();
  return trajectory1;
}
```
By using the PoseEstimate of the robot (the location of the robot) in real time, we could much more accurately create paths for the robot to follow. There isn't really too much code here to showcase but this solution ended up being extremely helpful. I think that looking towards the the future, better solutions would have been to either tune Road Runner correctly so that the robot could follow the correct path or to create a custom autonomous motion controller and not have to rely on Road Runner.
