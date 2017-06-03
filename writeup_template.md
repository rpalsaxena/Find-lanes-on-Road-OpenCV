# **Finding Lane Lines on the Road** 
$$Submitted  By: Rahul Saxena(@rpalsaxena)$$
## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

This was a pretty interesting project. I followed these steps:
1. **RGB to Grayscale**: The first step was to convert the images to grayscale. It converts the images to Black and White. This way I reduced the complexity of RGB. 
    ```
    img_gray = grayscale(image)
    ```
2. **Gaussian Blur**: The first step was to add some smoothing effect in the images/video. This reduces the noise in our edge predictions. 
    ```
    img_gblur = gaussian_blur(img_gray, 5)
    ```
3. **Canny-Edge Detection**: Our images are now in black & white color and smoothened. To detect edges around the lane lines, I used Canny Edge detection method by opencv lib.
    ```
    img_canny = canny(img_gblur, 5, 190)
    ```
4. **Region of Interest**: The canny-edge method detected all the edges in the whole frame even if the edges belong to lanes or not. For selecting region specific to lanes, we need to mask a particular region of interest. I selected the region on the basis of dimensions of image. 
    ```
    vertices = np.array([[(imshape[1]*0.15,imshape[0]),
                          (imshape[1]*0.45, 540*0.605),
                          (imshape[1]*0.55,imshape[0]*0.605),
                          (imshape[1]*0.95,imshape[0])]],
                        dtype=np.int32)
    img_mask = region_of_interest(img_canny, vertices=vertices)
    ```
5. **Hough Lines**: This is one of the trickiest steps. To map lines over the lanes for helping our car to understand its path, I used Hough Transform. The HoughLinesP() method needs to be passed with multiple hyperparameters. THe hyperparameter should be tuned to get good results. 
    ```
    rho = 15 # distance resolution in pixels of the Hough grid
    theta = np.pi/180 # angular resolution in radians of the Hough grid
    threshold = 65     # minimum number of votes (intersections in Hough grid cell)
    min_line_length = 10 #minimum number of pixels making up a line
    max_line_gap = 10 
    line_image = np.copy(image)*0 
    line_img = hough_lines(img_mask,  rho, theta, threshold,
                           min_line_length, max_line_gap)
    ```
    
6 **Weighted image**: Till this step, both the lane lines have been detected by our model. Now, its time to put these lines on top of our images or frames.
    ``` 
    final_img = weighted_img(line_img, image)
    ```

### 2. Identify potential shortcomings with your current pipeline

The images and both the 'solidWhiteRight' and 'solidYellowLeft' videos properly detected the lane lines. Although, there are 2 shortcomings in my model: 
* The lanes are not perfectly detected in case of challenge video.
* The illumination of light i.e, due to sunlight in the video. It started to detect edges incorrectly.
 

### 3. Suggest possible improvements to your pipeline

A possible improvement for correcting this light illumination effect can be done by precisely implementing our region selection task. We can also try making 2 regions, like this:

![](file:///D:/CarND/Projects/CarND-LaneLines-P1-master/p1_improvements.png) { width: 500px; height: 700px }
 