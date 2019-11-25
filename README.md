# Advanced Lane Finding

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

## Overview of the Project

In this project, a software pipeline was constructed to identify the lane boundaries in a video. Numerical estimation of lane curvature and vehicle position can also be implemented using this pipeline. The main steps of this repo are the following:
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
`calibration1.jpg`
![Lanes Image](output_images\calibration1.jpg)

## Pipeline Construction
The pipeline to identify the lane boundaries in single images is implemented by following steps:
### Image undistortion
- Apply a distortion correction to raw images.
### Binary Image
- Use color transforms, gradients, etc., to create a thresholded binary image.
### Perspective transform
- Apply a perspective transform to rectify binary image ("birds-eye view").
- The code for my perspective transform includes a function called `warp()`. The `warp()` function takes as inputs an image, and runs `cv2.warpPerspective()` using the follwing source (`src`) and destination (`dst`) points. I chose the hardcode the source and destination points in the following manner:
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

    I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

### Lane boundary  detection and fitting
- Detect lane pixels and fit to find the lane boundary.
### Lane curvature determination
- Determine the curvature of the lane and vehicle position with respect to center.
### Output visual display of the lane boundaries and information
- Warp the detected lane boundaries back onto the original image.
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

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
The approach adopted in current pipeline worked pretty well for `project_video.mp4`. However, it fails to detect the lane boundary correctly in `challenge_video.mp4` and `harder_challenge_video.mp4`. In `challenge_video.mp4`, the pipeline had some problems in case the surface of the street changes, there is much shadow on the road, lane lines are missing, etc. In `harder_challenge_video.mp4`, the pipeline fails to follow the lane boundary since the curature of the road changes fast.

## Further Work
Improvements could be:
- consider provious polynoms that were fitted onto the lane lines in order to smoothen lane line detection
- add sanity checks that kick out values that do nor make sense. In that case the previous polynoms could be reused.
- the values of the curvature and the position of the car are hardly readable since they change so often. Less frequent update of the values and calculation of the average over several frames of the video could help.
- In order to reduce effort for calculation of the thresholds, etc. the images reduced to the area of interest (the street only)
- There might be a better values for min and max thresholds of the threshold calculation. More finetuning might produce even better results.

