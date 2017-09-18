# **Finding Lane Lines on the Road**

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**


[//]: # (Image References)

[image1]: ./test_images/solidWhiteRight.jpg "Test input image"
[image2]: ./test_images_output/solidWhiteRight.jpg "Output results"
[image3]: ./test_images_output/parallelLanes.png "Parallel Output"
[gif1]: ./test_videos_output/output.gif "Output gif"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

| def                | purpose                                         |
| -------------------|:-----------------------------------------------:|
| grayscale          | convert the image to a greyscale image          |
| canny              | detect the edges inside the image               |
| region_of_interest | define a region of interest                     |
| draw_lines         | Find slope, extrapolate lane lines **see below* |
| hough_lines        | transform points on the image to lines          |
| weighted_img       | calculate weighted sums of 2 arrays             |

My initial thought process as to extrapolating lane lines was to grab the slope of the lines as they were provided by `hough_lines()` and depending on their positivity, or negativity, I could determine if the line pertained to the left or right lane line. For example, if a slope was negative, it meant that the lane line was belonging to the left of the frame. Whereas a positive slope meant a right lane line. To determine this, I used `(y2 - y1) / (x2 - x1)`, with each `x` and `y` being the coordinates of a hough line.

Having assigned those coordinates to respective variables as arrays, I then proceeded to extrapolate the coordinates of the full lane lines, ignoring gaps in the paint. I knew that the horizon was about 320 pixels down, and that I could use the full height of the image for my constant `y` points.

Using `y = mx + b` I started solving for `x`. Soon I began running into many issues. First being my region of interest, which I'll touch on later, as well as lane lines being parallel.

![alt text][image3]

Obviously, this is unusable. My points were so far off, and it seemed that my formula for extrapolation was off, and that my region of interest was also off.

Soon, I was directed to an article on [Medium](https://medium.com/@mrhwick/simple-lane-detection-with-opencv-bfeb6ae54ec0), and this began to shine a lot of light on my problems, as well as better approaches to my initial attempts.

As it turns out, using `np.polyfit` to fit my left lines, and my right lines to `x, y` coordinates. That way I could grab the `min`, and `max` points of my `x-axis` and then along with my predefined y coordinates I could effectively create accurate lane lines for both video and image.


Turning this image:

![alt text][image1]

Into this image:
![alt text][image2]

I finally had a successful lane detection system in place with proper lane extrapolation.


### 2. Identify potential shortcomings with your current pipeline

There are definitely shortcomings, as this approach is very simple in practice. For instance, this expects clear sunny conditions on the road. This expects that the pavement on the road is evenly colored, without distortions etc. Also take for instance, if the vehicle directly in front of the current on, there is a great chance lane lines will be blocked from the camera's sight and cause the lanes to be disrupted. With that said though, this is a very promising beginning, that allows rom for progression.

Also, the current implementation has parts when the detected lane is no where near the actual lane. Sometimes even inverting the slope all together. Which is not desired at all.

Also curves, especially sharp ones in the road.


### 3. Suggest possible improvements to your pipeline

I imagine that some improvements could include forced width of lanes. I think that that could reduce the possibility of the pipeline being misdirected by one-off frames, and other oddities in the road.

I also think that an improvement would be dynamic `y` coordinate positioning. I believe that would help with gaining and losing elevation, but more importantly with curves in the road.


### All in all
I believe this is a solid first step to lane detection.

![alt text][gif1]
