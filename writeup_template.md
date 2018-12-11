# **Finding Lane Lines on the Road** 


## Goals

* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[before_draw_lines]: ./test_images_intermediate/solidYellowCurve2.png "before_draw_lines"
[after_draw_lines]: ./test_images_output/solidYellowCurve2.png "after_draw_lines"
[higher_poly]: ./test_images_intermediate/solidYellowCurve.png "higher_poly"

---

## Reflection

### How the pipeline works

My pipeline consisted of 5 steps:
1. Converted the images to grayscale.
2. Add Gaussian smoothing to the grayscaled image in order to reduce noice.
3. Detect Canny edges.
4. Apply Hough transform to selected region of interest from the Canny edges, and draw the lines on a blank image.
5. Draw the lines on the original image using the `weighted_img` method.

After the 5 steps the output image looks like this:

![alt text][before_draw_lines]


In order to draw a single line on the left and right lanes, I modified the `draw_lines` function. The steps are:
1. Separate out lines into the left and right lane by inspecting their slope. 
   * I introduced some thresholds for the positive and negative slopes to remove noise.
   * The output is a list of points for the left lane, and another list for the right lane. 
2. Extrapolate the points in each lane to one single line.
   * I used the `polyfit` method from the `numpy` library with `degree = 1` to get the coefficient and intercept for the
   list of points.
   * I used the `poly1d` method to get the polynomial function.
   * I applied the polynomial function to the minimal and maximum `x` coordinates to get the start and end points to 
   draw the line on.
3. Draw the line between the two points returned from the above step using the `cv2.line` method, for both left and right 
   lanes.

After modifying the `draw_line` method the output image is:

![alt text][after_draw_lines]

For the optional challenge I changed to use higher degree polynomical functions, then used the `linspace` method from 
`numpy` to slice the between the maximum and minimum `x` coordinates, and then connected dots in between with `cv2.line`.
It didn't break the straight lane cases, unfortunately it isn't sufficient for the curves case. (The changes start from 
the `draw_lines_higher_poly` method)

### Potential shortcomings with the pipeline

One shortcoming is the pipeline still cannot handle curved lanes. In the output vedio the left lane drawing is off the 
lane and the right one the convex isn't in the expected direction.

### Possible improvements

A possible improvement is to tune parameters for Canny, Hough, etc, to see if things improve 
(after I used the higher polynomial method I didn't go back to adjust the params).

Another possible improvement is to look more into the `numpy` and `cv2` libraries to see what helps. 

Notice that for the `solidYellowCurve.jpg` test image the output isn't detecting the left curve in the correct direction:

![alt text][higher_poly]

I can use this image as a test file to tune and iterate.