## Lift PID Controller

For the 2021-2022 FTC Challenge, our robot had to be able to vertically lift an object up off the ground and then deposit
that object onto a platform. In terms of mechanical components, we decided to use two motors (one on each side of the lift) that would engage with a belt that would then push/pull Misumi Slide Rails to raise and lower the vertical lift. 

In code, we were able to keep track of the vertical height of the lift with the following method:
```java
public static double SPOOL_SIZE_IN =.73818898;
public static double MOTOR_RATIO = 5.2;
public static double TICKS_PER_REV = MOTOR_RATIO * 28.0;
public static double GEAR_RATIO = 1;

public static double encoderTicksToInches(double ticks) {
  return SPOOL_SIZE_IN * 2 * Math.PI * GEAR_RATIO * ticks / TICKS_PER_REV;
}
```
Here, we convert the ticks that the motor has "traveled" (which the motor keeps track of internally) to a distance value in inches which tells us how high our vertical lift travels.

In order to make sure that the lift arrives smoothly and rapidly to the correct desired vertical height, we decided to implement a custom [PID controller](https://en.wikipedia.org/wiki/PID_controller) (proportion-integral-derivative controller). 

The PID controller utilizes a feedback loop to constantly update the power of the motors to ensure a smooth transition to the desired lift height. The proportion component is proportional to the distance, or "error," between the target value and the current value. As the current distance value approaches the target value, the proportional component decreases. The integral and derivative components provide smoother motion by reducing the oscillations that the feedback loop experiences as it reaches the target value. Here is the code for the PID controller:

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
Here, the "error" value is scaled by the kP constant. **Lots** of testing was required in order to achieve a suitable kP value. We also found that the integral and derivative components did not have a beneficial effect, so we decided to remove them from the feedback loop. However, we added another component (kF) called feedforward. This feedforward component gives the PID a bit of necessary extra power when the proportional component becomes very small.

Because the integral and derivative components did not work to our standards, we had to add in a manual feature that cut the power of the motors when the "zero" height was reached. Otherwise, the lift motors would oscillate back and forth because they could never perfectly reach the desired height due to the nature of the PID loop and probably some slipping of the belts.
```java
// If the target height is 0 and the "error" is within a certain threshold, then cut the power of the motors so they don't oscillate.
if (target == 0 && Math.abs(leftError) <= cutEnginePIDMin) {
  leftLift.setPower(0);
  rightLift.setPower(0);
}
```
In conclusion, this PID system for the lift worked well! We would always reliably and smoothly travel to the correct vertical position with our lift. It could probably be improved by testing more with the integral and derivative components (tuning the constants better).
