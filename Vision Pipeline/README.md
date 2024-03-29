## Vision Pipeline

For the 2021-2022 FTC Challenge, a key task that our robot needed to be able to perform was color detection in 3D space.
Each team had their own custom physical marker they could use for vision detection. Before the round started, the marker was randomly placed either to the left, center, or right of the robot, as illustrated in the colored squares on the field in the image below. Depending on where the marker was placed, the robot had to perform a certain specific task.
<p align="left">
  <img src="./Media/GameMarker.png" alt="Game Marker" width="500">
</p>


In the example image, a model traffic cone is used as the marker. We decided to base our vision detection on color, so we made our marker a lime green cone. In order to capture video, we used a small Logitech camera and integrated it into our code using [EasyOpenCV](https://github.com/OpenFTC/EasyOpenCV), a library that allows for easy integration with the popular computer vision library, [OpenCV](https://opencv.org/). 



Our idea was to apply a filter that converted the BGR video data into HSV data, where we could limit the hue, saturation, and value of each pixel that was processed. We detected where the lime pixels were using this filter. If the most lime pixels appeared on the left side of the camera's view, then that must mean that the marker is on the left. We decided to only check the left and right sides of the camera's view for lime pixels, determining that the marker was in the center if there were no lime pixels on the left or right sides.


```java
private Mat workingMatrix = new Mat();

Imgproc.GaussianBlur(workingMatrix, workingMatrix, new Size(5.0, 15.0), 0.00);
Imgproc.cvtColor(workingMatrix, workingMatrix, Imgproc.COLOR_BGR2HSV);
```

I first applied a Gaussian Blur to the incoming video data to reduce some of the noise and detail. I then converted the color formatting as seen with the 
filter called "COLOR_BGR2HSV." I then had to initialize several variables in order to detect the correct range of color (lime green). We used trial and error to determine the range of HSV values in order to accurately detect the lime green marker.

```java
public static double hueMin = 50;
public static double hueMax = 75;
public static double saturationMin = 60;
public static double saturationMax = 200;
public static double valueMin = 90;
public static double valueMax = 250;
```
I then applied these color constraints to the video data. This would create a view where all of the lime green pixels are white and all other pixels appear black. 
```java
Core.inRange(workingMatrix, new Scalar(VisionVariables.hueMin, VisionVariables.saturationMin, VisionVariables.valueMin),
   new Scalar(VisionVariables.hueMax, VisionVariables.saturationMax, VisionVariables.valueMax), workingMatrix);
```
This is what the resulting image data looks like:
<p align="left">
  <img src="./Media/FilteredImage.png" alt="Filtered Image Output" width="200">
</p>

Next, I had to split the video data into two separate views, one for the left side and one for the right side. In order to count the number of "lime" pixels in each view, I only had to check the pixels that were white in the new view.

```java
// The submat method divides the frame into a smaller section using the paramters (rowStart, rowEnd, colStart, colEnd)

Mat matWholeLeft = workingMatrix.submat(10, 319, 10, 110);
Mat matWholeRight = workingMatrix.submat(10, 319, 139, 239);

// matTotalLeft and matTotalRight represent the numerical amount of lime pixels in each side of the camera's view
matTotalLeft = Core.sumElems(matWholeLeft).val[0];
matTotalRight = Core.sumElems(matWholeRight).val[0];

```
All that is left now is to implement the logic to actually determine where the marker is in relation to the robot. I used a process of elimination 
algorithm that returns that the marker is in the center if not enough lime pixels are present in the left or right views of the camera. The numerical "MARGIN" value is added as a buffer to ensure that one side actually has a substantially larger "lime" pixel count than the other side. The "MARGIN" value was chosen using trial and error along with outputs from debugging.
```java
final int MARGIN = 100000;

if (right > left + MARGIN) {
  location = "RIGHT";
} else if (left > right + MARGIN) {
  location = "LEFT";
} else {
  location = "CENTER";
}
```
Based on this location output information, the robot can now complete the correct task when the game starts and achieve the maximum amount of points regarding the vision aspect of the challenge.
