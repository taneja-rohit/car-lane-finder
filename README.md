# **Autonomous vehicle lane finder project|** 

## How I got it working

### This project is developed using Python3 and OpenCV APIs. The jupyter notebook file in this repository contains the code for piepline and also modification of the drawing function.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road for images
* Make improvements/ Smoothen / extrapolate the lines identified in order to have continuity
* Apply the pipeline to video.


[//]: # (Image References)

[image1]: ./images/grayscale.jpg "Grayscale"
[image2]: ./images/grayscale.jpg "Grayscale"
[image3]: ./images/grayscale.jpg "Grayscale"
[image4]: ./images/grayscale.jpg "Grayscale"
[image5]: ./images/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Pipeline description.

My pipeline consisted of 5 steps:

* Initialization:  Importing the necessary packages and images. Here as an example, we have imported an image from dataset. 
  
  ![alt text][image1]

* Grayscale conversion: The color image is converted to 8-bit grayscale image, thus pixel values ranges between 0 and 255. This process helps in proper setting of thresholds for the next step of edge detection. Image 2 below the result after convertingg RGB image to grayscale. 

  ![alt text][image2]

* Gaussian smoothening: We can optionally run a gaussian smoothening function over the grayscale image we generated in previous step in order to supress noise and remove any spurious gradients by averaging. This is useful before applying edge detection algorithm. 

* Canny edge detection: We use Canny edge algorithm to detect the edges in our smoothened image. Canny edge algorithm detects strong edge (strong gradient) pixels above the high_threshold parameter, and reject pixels below the low_threshold parameter. For our case of detecting lanes, we set high_threshold as 150 and low_threshold to 50. Image 3 below shows resulting image after applying Canny edge algorithm. 

 ![alt text][image3]

* Region of Interest: This takes in the human reasoning of where we will find the lanes on road and thus we can apply mathematical region selection APIs from OpenCV like FillPoly which fills the pixels only on the region defined by vertices ( as in the code ) and masks the other region. Image 4 shows how we only kept the lane information/pixels and ignored the rest. 

 ![alt text][image4]

* Hough transform: In order to detect the lanes after edge detection, we utilize Hough transform. Here, we use it from OpenCV and tune several parameters like rho and theta to smoothen the line. Theta can be tuned to value greater than 1 degree for smoother lane detection. As a part of this transform, we also average or extrapolate the lines in order to generate the continuous line for both left and right side. In order to do so, first step is to group the lines found by hough transform into left side and right side. This can be done by calculating the slope of line, and if slope > 0, it forms the part of right line, otherwise it forms the part of left lane; as shown in code in draw_lines function. After grouping the co-ordinates into left and light lines, ployfit function from numpy is utilized in order to find slope(m) and intercept(b) values for left and right lines. Now, we can generate a linear line y = m*x + b using the values found by polyfit function. In order to do so, we use poly1d function from numpy. 
      For lane detection, we use reasoning that it starts from bottom of the image and runs up till a co-ordinate which can give the clear indication of lane found. So in order to draw a line, we use x co-ordinates and calculate y co-ordinates using the poly1d function 1 above and use those y-cordinates in OpenCV's line function to draw a continous line on a blank image. Image 5 below illustrates the lines formed. 
      
  ![alt text][image5]   
  
  
  * Final Image: Now we overlay the original image with the image from last step which had a lane detected. Image 6 below shows the final output. 
  
  ![alt text][image6]




### 2. Identify potential shortcomings with your current pipeline


* This approach doesn't take in to account for roads which doesn't have lane markings clearly marked of if they are faded.
* On a circular arching intersection, the smoothening function may need to be tuned to clearly mark the lines. 


### 3. Suggest possible improvements to your pipeline

* Playing with value  of theta can result in precise lane identification
