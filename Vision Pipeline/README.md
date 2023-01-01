## Vision Pipeline

For the 2021-2022 FTC Challenge, one of the main tasks that our robot had to perform was color detection in 3D space.
Each team had their own custom physical marker that they could use for vision detection. Before the round started, the marker was randomly placed either to the left, center, or right of the robot as illustrated in the colored squares on the field in the image below.

In the example image, a model traffic cone is used as the marker. We decided to base our vision detection on color and so we made our marker a lime green cone. In order to actually capture video, we used a small, low-end camera and integrated it into our software using [EasyOpenCV](https://github.com/OpenFTC/EasyOpenCV), a library that allows for easier integration with the popular computer vision library, [OpenCV](https://opencv.org/). 

Our idea was to apply a filter that converted the BGR video data into HSV data where we could limit what hue, saturation, and value for each pixel that is processed. We would detect where only the lime pixels were using the vision pipeline. If there were the most lime pixels on the left side of the camera's view, then that must mean that the marker is on the left. We decided to only check the left and right side of the camera's view for lime pixels, determining that the marker was in the center if there was not a presence of lime pixels in the left or right side.


```
private Mat workingMatrix = new Mat();

Imgproc.GaussianBlur(workingMatrix, workingMatrix, new Size(5.0, 15.0), 0.00);
Imgproc.cvtColor(workingMatrix, workingMatrix, Imgproc.COLOR_BGR2HSV);
```

I first applied a Gaussian Blur to the incoming video data to reduce some of the noise and detail. I then converted the color formatting as seen with the 
filter called "COLOR_BGR2HSV." I then had to initialize several variables in order to detect the correct range of color (lime green). We used trial and error to determine the range of HSV values in order to accurately detect the lime green marker.

```
public static double hueMin = 50;
public static double hueMax = 75;
public static double saturationMin = 60;
public static double saturationMax = 200;
public static double valueMin = 90;
public static double valueMax = 250;
```
