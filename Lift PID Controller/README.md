## Lift PID Controller

For the 2021-2022 FTC Challenge, one of the main tasks that our robot had to perform was to vertically lift an object up off the ground and then deposit
that object onto a platform. In terms of mechanical components, we decided to use two motors (one on each side of the lift) that would engage with a belt that would then push/pull Misumi Slide Rails to raise and lower the vertical lift. 

It would be easy enough to set the powers of the motors to be equal so that the lift raises evenly, but the problem arises with making sure the lift arrives smoothly at its desired vertical height. We were able to keep track of the vertical height of the lift with the following method:
```java
public static double SPOOL_SIZE_IN =.73818898;
public static double MOTOR_RATIO = 5.2;
public static double TICKS_PER_REV = MOTOR_RATIO * 28.0;
public static double GEAR_RATIO = 1;

public static double encoderTicksToInches(double ticks) {
  return SPOOL_SIZE_IN * 2 * Math.PI * GEAR_RATIO * ticks / TICKS_PER_REV;
}
```
Here, we could convert the ticks that the motor has "traveled" (which the motor keeps track of internally) and convert it to a distance value in inches which would tell us how high our vertical lift goes. But how does the lift know when to stop? Should it cut all power as soon as it hits the required threshold? This causes very abrupt and frankly innacurate positioning of the lift.

The solution is to use a [PID controller](https://en.wikipedia.org/wiki/PID_controller) (proportion-integral-derivative controller). The PID controller utilizes a feedback loop to constantly update the power of the motors to ensure a smooth transition to the desired lift height. The proportion component is proportional to the distance or "error" between the desired target value and the current value. So as the current value reaches closer to the target value, the proportional component decreases. The integral and derivative components also help to create a smoother transition by reducing some of the oscillations that the feedback loop can experience once it reaches the target value. Here is the code for the PID controller:

```java
public static double kP = .5;
public static double kF = 0.05;

public void updatePID(double target) {
  double leftError = target - encoderTicksToInches(leftLift.getCurrentPosition());
  double leftPid = leftError * kP + Math.copySign(kF, leftError);
  
  leftLift.setPower(leftPid);
  rightLift.setPower(leftPid);
}
```
Here, the "error" value is scaled by the kP constant. It required **lots** of testing in order to achieve a suitable kP value. We also found that the integral and derivative components did not have a positive effect and so we decided to remove them from the feedback loop. However, we added another component (kF) called feed forward. This component is always added to the motor power equation and gives the PID a little bit of necessary extra power when the proportional component becomes very small.

Because the integral and derivative components did not seem to work, we had to add in a manual feature that cut the power of the motors when the "zero" height was reached or else the lift motors would oscillate back and forth because they could never perfectly reach the desired height due to the nature of the PID loop and also probably some slipping of the belts.
```java
// If the target height is 0 and the "error" is within a certain threshold, then cut the power of the motors so they don't oscillate.
if (target == 0 && Math.abs(leftError) <= cutEnginePIDMin) {
  leftLift.setPower(0);
  rightLift.setPower(0);
}
```
In conclusion, this PID system for the lift worked really well! We would always reliably and smoothly travel to the correct vertical position with our lift. It could probably be improved a bit by testing more with the integral and derivative components (tuning the constants better).
