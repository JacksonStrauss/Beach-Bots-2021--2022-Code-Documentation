## Vision Pipeline

For the 2021-2022 FTC Challenge, one of the main tasks that our robot had to perform was color detection in 3D space.
Each team had their own custom physical marker that they could use for vision detection. Before the round started, the marker was randomly placed either to the left, center, or right of the robot as illustrated in the colored squares on the field in the image below.

In the example image, a model traffic cone is used as the marker. We decided to base our vision detection on color and so we made our marker a lime green cone. In order to actually capture video, we used a small, low-end camera and integrated it into our software using [EasyOpenCV](https://github.com/OpenFTC/EasyOpenCV), a library that allows for easier integration with the popular computer vision library, [OpenCV](https://opencv.org/). 
