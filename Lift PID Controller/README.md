## Lift PID Controller

For the 2021-2022 FTC Challenge, one of the main tasks that our robot had to perform was to vertically lift an object up off the ground and then deposit
that object onto a platform. In terms of mechanical components, we decided to use two motors (one on each side of the lift) that would engage with a belt that would then push/pull Misumi Slide Rails to raise and lower the vertical lift. 

It would be easy enough to set the powers of the motors to be equal so that the lift raises evenly, but the problem arises with making sure the lift arrives smoothly at its desired vertical height. We were able to keep track of the vertical height of the lift with the following method:
```java
public static double encoderTicksToInches(double ticks) {
  return SPOOL_SIZE_IN * 2 * Math.PI * GEAR_RATIO * ticks / TICKS_PER_REV;
}
```
Here, we could convert the ticks that the motor has "traveled" (which the motor keeps track of internally) and convert it to a distance value in inches which would tell us how high our vertical lift goes. But how does the lift know when to stop? Should it cut all power as soon as it hits the required threshold? This causes very abrupt and frankly innacurate positioning of the lift.

The solution is to use a [PID controller](https://en.wikipedia.org/wiki/PID_controller) (proportion-integral-derivative controller). The PID controller utilizes a feedback loop to constantly update the power of the motors to ensure a smooth transition to the desired lift height. The proportion component is proportional to the distance or "error" between the desired target value and the current value. So as the current value reaches closer to the target value, the proportional component decreases. The integral and derivative components also help to 
