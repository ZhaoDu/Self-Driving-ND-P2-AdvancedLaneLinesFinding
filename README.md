# Project 02: Advanced Lane Finding

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

## Overview of the Project

In this project, a software pipeline was constructed to identify the lane boundaries in a video. Numerical estimation of lane curvature and vehicle position can also be implemented using this pipeline. The main steps of this pipeline are the following:
- Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
- Apply a distortion correction to raw images.
- Use color transforms, gradients, etc., to create a thresholded binary image.
- Apply a perspective transform to rectify binary image ("birds-eye view").
- Detect lane pixels and fit to find the lane boundary.
- Determine the curvature of the lane and vehicle position with respect to center.
- Warp the detected lane boundaries back onto the original image.
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

## Camera Calibration
- Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.

    In this step, images stored in the folder called `cameral_cal` were loaded and converted to grayscale first. As can be found, there are nine and six interior corners in the x direction(`ny=9`) and y direction(`ny=6`) in these calibration images, respctively. The function `cv2.findChessboardCorners()` was used to find these corner points in images. Then, the chessboard indices and corresponding pixel coordinates of those successfully found point were appended into array `objpoints` and `imgpoints`, respectively. The function `cv2.calibrateCamera()` was then utilized to calculate the calibration and distortion coefficients. The function `cv2.undistort()` was finnaly performed based on aboved calculated coefficients to undistort the images(see following image `calibration1.jpg`).

    ![Camera Calibration Image](output_images\calibration1.jpg)

## Pipeline Construction
The pipeline to identify the lane boundaries in single images is implemented by following steps:
### Image undistortion
- Apply a distortion correction to raw images. See `undistortion()`

  The function `cv2.undistort()` was performed based on cameral calibration coefficients to undistort the raw images(See `Undistortion` column of images in ` Test on Images` part)

### Binary Image
- Use color transforms, gradients, etc., to create a thresholded binary image. See `abs_sobel_thresh()`, `mag_thresh()`, `dir_threshold()`, `hls_select()`, and `apply_thresh()`.

    A combination of gradient and color thresholds was used to generate a binary image. More specifically, gradient in x and y direction, magnitude gradient threshold, direction threshold and S channel(from HLS) threshold.( Combined in following form: `((gradx == 1) & (grady == 1)) | ((mag_binary == 1) & (dir_binary == 1)) | (hls_binary == 1)`).  (See `Binary` column of images in ` Test on Images` part)

### Perspective transform
- Apply a perspective transform to rectify binary image ("birds-eye view"). See `bird_view()`

    The code for my perspective transform includes a function called `warp()`. The `warp()` function takes as inputs an image, and runs `cv2.warpPerspective()` using the follwing source (`src`) and destination (`dst`) points. I chose the hardcode the source and destination points in the following manner:
    ```python
    src = np.float32(
    [[(img_size[0] / 2) - 62, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 62), img_size[1] / 2 + 100]])

    dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
    ```
    This resulted in the following source and destination points:

    | Source        | Destination   | 
    |:-------------:|:-------------:| 
    | 585, 460      | 320, 0        | 
    | 203, 720      | 320, 720      |
    | 1127, 720     | 960, 720      |
    | 695, 460      | 960, 0        |

    I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. (See `Bird_view` column of images in ` Test on Images` part)

### Lane boundary  detection and fitting

- The function `find_lane_pixels()` uses a slide window to find the lane line in pixles. After get the lane line pixels, these pixels was grouped into left and right set. 
- The function `search_around_poly()` searches around previouly detected lane line because consecutive frames are likely to have lane lines in roughly similar positions. We search around a margin of 80 pixels of the previously detected lane lines.
- Fit polynomials(degree=2) to both left and right set of points found in the previous step.

### Lane curvature determination
- Determine the curvature of the lane and vehicle position with respect to center. See `measure_curvature_real()` and `measure_vehicle_offset`.

    The offset of the vehicle was calculated by fining the `center_of_lane` values of the left and right fitted lines and then took the difference from the center column of the image `center_of_image`, converted to meters: `offset = (center_of_image - center_of_lane)*xm_per_pix`. If the `offset` value is negative, the vehicle is right of the lane center, else left (See `Final` column of images in ` Test on Images` part).

### Output visual display of the lane boundaries and information
- Warp the detected lane boundaries back onto the original image. See `project_fit_lane()`. (See `Line` column of images in ` Test on Images` part).
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position. See `project_fit_lane_info()`. (See `Final` column of images in ` Test on Images` part).

## Test on Images
`straight_lines1_undis.jpg`
![straight_lines1_undis.jpg](output_images\straight_lines1_undis.jpg)
`straight_lines1.jpg`
![straight_lines1.jpg](output_images\straight_lines1.jpg)
`straight_lines2.jpg`
![straight_lines2.jpg](output_images\straight_lines2.jpg)
`test1.jpg`
![test1.jpg](output_images\test1.jpg)
`test2.jpg`
![test2.jpg](output_images\test2.jpg)
`test3.jpg`
![test3.jpg](output_images\test3.jpg)
`test4.jpg`
![test4.jpg](output_images\test4.jpg)
`test5.jpg`
![test5.jpg](output_images\test5.jpg)
`ctest6.jpg`
![test6.jpg](output_images\test6.jpg)

## Test on Videos
### `project_video.mp4` [link to result](test_videos_output\project_video.mp4)
![project_video.mp4](test_videos_output\project_video.gif)

###  `challenge_video.mp4` [link to result](test_videos_output\challenge_video.mp4)
![challenge_video.mp4](test_videos_output\challenge_video.gif)
###  `harder_challenge_video.mp4` [link to result](test_videos_output\harder_challenge_video.mp4)
![harder_challenge_video.mp4](test_videos_output\harder_challenge_video.gif)

## Discussion 
The approach adopted in current pipeline worked pretty well for `project_video.mp4`. However, it fails to detect the lane boundary correctly in `challenge_video.mp4` and `harder_challenge_video.mp4`. In `challenge_video.mp4`, the pipeline had some problems in case the surface of the roads changes, there is much shadow on the road, lane lines are missing, etc. Besides, the current pipeline might struggle with lines in the middle of the lane between the light and dark asphalt surface. In `harder_challenge_video.mp4`, the pipeline fails to follow the lane boundary since the curature and slope of the road changes fast.

## Further Work
However, there are still room for improment on this project. Following aspects can be taken into consideration in near future:

- There might be better values for min and max thresholds of the threshold calculation. More finetuning might produce even better results.
- Consider provious polynoms that were fitted onto the lane lines in order to smoothen lane line detection. Average over a number of frames to get better results since the shape and info of lanes changes quite fast.
- Add sanity checks that kick out values that do nor make sense. In that case the previous polynoms could be reused.