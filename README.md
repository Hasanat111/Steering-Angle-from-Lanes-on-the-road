## Steering-Angle-from-Lanes-on-the-road
[Lane New Demo](https://youtu.be/KwClFVjNqJM)
[Own video Demo](https://youtu.be/fdMS_apcQHo)


Algorithm that steers a vehicle by using just the front-facing camera as an input feed.
Human drivers Identifying lanes on the road to ensure their vehicles are within lane constraints when driving, so as to make sure traffic is smooth and minimize chances of collisions with other cars due to lane misalignment. Similarly, it is a critical task for an self_driving  vehicle to detect lanes and find radius of curvature of the road from lanes.
Video from the front facing camera is fed to this pipeline and the output is steering angle  and car offcenter from lanes center to ensure that car steer with lanes curvature and stay in the center of the lanes. Pipeline is as follows:
 
![1.1 original](https://user-images.githubusercontent.com/51098423/59150956-c213d680-8a44-11e9-8726-334819b1a73d.png)

### 1.1.1	Gray-Scale
 Convert original image to Gray scale
![2 gray-scale](https://user-images.githubusercontent.com/51098423/59150965-07d09f00-8a45-11e9-89dd-ee02b75fa435.png)

### 1.1.2	Gaussian Blur
Gaussian Blur ( Gaussian smoothing) is a pre-processing technique used to smoothen the edges in an image to reduce noise. This step is needed to reduce the number of lines we detect, as we only want to focus on the most significant lines (the lane ones), not on other than road lanes. We must be careful as to not blur the images too much otherwise it will become hard to make up a line.
In Opencv built-in function of  Gaussian Blur takes a integer kernel parameter which represents  the intensity of the smoothing. In our case we choose a value of 7. 
![3 blurred](https://user-images.githubusercontent.com/51098423/59150969-1dde5f80-8a45-11e9-85ff-fc93ad68b42a.png)

### 1.1.3	Canny Edge Detector
Canny Edge Detector identifies  edges (sharp changes in brightness) in an image and discard all other data. The resulting image ends up being wiry, which enables us to focus on lane detection even more, since we are concerned with lines.
The OpenCV built-in function  requires two parameters in addition to our blurred image, a low and high threshold which determines whether to include a given edge or not. A threshold captures the intensity of change of a given point. Any point beyond the high threshold will be included in our resulting image, while points between the threshold values will be included if they are next to edges beyond our high threshold. Edges that are below our low threshold are discarded. We use values 50 and 150 respectively for low and high thresholds.
![4 Canny edge detector](https://user-images.githubusercontent.com/51098423/59150971-2df63f00-8a45-11e9-99a8-2c62ac9cdf56.png)

### 1.1.4	Region of interest
In this step we determine a region of interest  in the form of isosceles trapezoidal and discard any lines outside of this polygon. We assume that the camera remains in the same place across all these image, and lanes are flat, therefore we can identify the critical region we are interested in. 
![4 ROI](https://user-images.githubusercontent.com/51098423/59150977-3d758800-8a45-11e9-9dc8-59ecf2f4bf94.png)

## 2	Finding Lanes And Drawing The Lane Area

### 2.1 Perspective Transform
We now need to define a trapezoidal region in the  2D image that will go through a perspective  transform to convert into a bird’s eye view. This transform changes the view from car front view to car top view (bird view). This transform keeps straight lines straight

### 2.2 Histogram
We then compute a histogram of our resulting  images in the y direction, to identify the x positions where the pixel intensities are highest.

### 2.3 Sliding Windows
Since we now know the starting x position of pixels (from the bottom of the image) most likely to yield a lane line, we run a sliding window search  an attempt to “capture” the pixel coordinates of our lane lines.
Then, we compute a second degree polynomial, via numpy’s  polyfit, to find the coefficients of the curves that best fit the left and right lane lines.

### 2.4 Inverse Perspective Transform
Finally, we draw the inside the of the lane in green and unwarp the image, thus moving from bird’s eye view to the original undistorted image. 

## 3	 Lane Curvature

### 3.1 Radius of curvature
compute the lane curvature by calculating the radius of the smallest circle that could be a tangent to our lane lines. Draw a circle that closely fits nearby points on a local section of a curve and  curves have the same tangent and curvature at the point where they meet. This radius changes as we move along the curve  and on a straight lane the radius would be quite big.
The radius of curvature of the curve at a particular point is defined as the radius of the approximating circle. 
 
### 3.2 Angle of curvature And Steering Angle
The degree of curvature is defined as the central angle to the ends of an arc or chord of agreed length. Various lengths are commonly used in different areas of practice. This angle is also the change in forward direction as that portion of the curve is traveled. Where degree of curvature is based on 100 units of arc length, the conversion between degree of curvature and radius is Dr = 18000/π ≈ 5729.57795, where D is degree and r is radius.
 ![vlcsnap-2019-04-24-14h18m40s912](https://user-images.githubusercontent.com/51098423/59151011-8af1f500-8a45-11e9-81f5-59935e1f3d59.png)

