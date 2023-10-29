# SFND 2D Feature Tracking

<img src="images/keypoints.png" width="820" height="248" />

The idea of the camera course is to build a collision detection system - that's the overall goal for the Final Project. As a preparation for this, you will now build the feature tracking part and test various detector / descriptor combinations to see which ones perform best. This mid-term project consists of four parts:

* First, you will focus on loading images, setting up data structures and putting everything into a ring buffer to optimize memory load. 
* Then, you will integrate several keypoint detectors such as HARRIS, FAST, BRISK and SIFT and compare them with regard to number of keypoints and speed. 
* In the next part, you will then focus on descriptor extraction and matching using brute force and also the FLANN approach we discussed in the previous lesson. 
* In the last part, once the code framework is complete, you will test the various algorithms in different combinations and compare them with regard to some performance measures. 

See the classroom instruction and code comments for more details on each of these parts. Once you are finished with this project, the keypoint matching part will be set up and you can proceed to the next lesson, where the focus is on integrating Lidar points and on object detection using deep-learning. 

## Dependencies for Running Locally
1. cmake >= 2.8
 * All OSes: [click here for installation instructions](https://cmake.org/install/)

2. make >= 4.1 (Linux, Mac), 3.81 (Windows)
 * Linux: make is installed by default on most Linux distros
 * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
 * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)

3. OpenCV >= 4.1
 * All OSes: refer to the [official instructions](https://docs.opencv.org/master/df/d65/tutorial_table_of_content_introduction.html)
 * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors. If using [homebrew](https://brew.sh/): `$> brew install --build-from-source opencv` will install required dependencies and compile opencv with the `opencv_contrib` module by default (no need to set `-DOPENCV_ENABLE_NONFREE=ON` manually). 
 * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)

4. gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using either [MinGW-w64](http://mingw-w64.org/doku.php/start) or [Microsoft's VCPKG, a C++ package manager](https://docs.microsoft.com/en-us/cpp/build/install-vcpkg?view=msvc-160&tabs=windows). VCPKG maintains its own binary distributions of OpenCV and many other packages. To see what packages are available, type `vcpkg search` at the command prompt. For example, once you've _VCPKG_ installed, you can install _OpenCV 4.1_ with the command:
```bash
c:\vcpkg> vcpkg install opencv4[nonfree,contrib]:x64-windows
```
Then, add *C:\vcpkg\installed\x64-windows\bin* and *C:\vcpkg\installed\x64-windows\debug\bin* to your user's _PATH_ variable. Also, set the _CMake Toolchain File_ to *c:\vcpkg\scripts\buildsystems\vcpkg.cmake*.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./2D_feature_tracking`.

## Steps

### MP.1 Data Buffer Optimization

Construct a vector for dataBuffer objects with a size constraint (e.g., 2 elements), by adding new elements at one end while eliminating elements at the other end.


### MP.2 Keypoint Detection

Incorporate detectors such as HARRIS, FAST, BRISK, ORB, AKAZE, and SIFT, and ensure their selection via setting a string accordingly.


### MP.3 Keypoint Removal

Eliminate all keypoints outside a pre-defined rectangle, retaining only the keypoints within the rectangle for further processing.

### MP.4 Keypoint Descriptors

Employ descriptors such as BRIEF, ORB, FREAK, AKAZE and SIFT, and enable their selection by setting a string accordingly.


### MP.5 Descriptor Matching

Implement FLANN matching along with k-nearest neighbor selection. Both methods should be selectable through their respective strings in the main function.


### MP.6 Descriptor Distance Ratio

Utilize K-Nearest-Neighbor matching to execute the descriptor distance ratio test, which evaluates the ratio of the best versus second-best match to determine whether to retain a pair of associated keypoints.


### MP.7  Performance Evaluation 1

#### Keypoints Counting

Count the number of keypoints on the preceding vehicle across all 10 images, utilizing the different detectors that have been implemented. The table below shows the average time and number of keypoints for different detectors.

It's clear that **FAST** provides the best detection speed and fairly good accuracy.

| Detector   | Average time (ms) | Average keypoints amount |
| :--------- | ----------------- | ------------------------ |
| Harris     | 15.5229           | 24.8                     |
| SHI-TOMASI | 15.1395           | 78.2                     |
| **FAST**   | **1.24024**       | **149.1**                |
| BRISK      | **264.814**       | **276.2**                |
| ORB        | 25.4274           | 116.1                    |
| AKAZE      | 55.6996           | 167                      |
| SIFT       | 63.2617           | 138.7                    |

#### Neighborhood Distribution

**Harris, Shi-Tomasi, and FAST** have relatively small neighborhood size and spatial distribution with no overlapping.

On the contrary, **BRISK and ORB** exhibit a very large neighborhood size and compact distribution resembling clusters with many overlapping areas.

Lastly, **AKAZE and SIFT** possess a medium neighborhood size and relatively uniform distribution with minimal overlapping.


### MP.8 & MP.9 Performance Evaluation 2&3

Calculate the number of matched keypoints across all 10 images using all possible combinations of detectors and descriptors. In the matching phase, the BF approach is utilized with the descriptor distance ratio set to 0.8.

Record the time taken for keypoint detection and descriptor extraction. The results should be logged into a spreadsheet, and based on this data, the TOP3 detector/descriptor combinations are recommended for the specific goal of detecting keypoints on vehicles.

#### NOTES

- BINARY descriptors :BRISK, BRIEF, ORB, FREAK, and AKAZE ----NORM_L2
- HOG descriptors : SIFT (and SURF and GLOH, all patented) ---- NORM_HAMMING
- NORM_HAMMING2 should be used with ORB when WTA_K==3 or 4
- Some combinations resulted in errors:
  - KAZE/AKAZE descriptors will only work with KAZE/AKAZE detectors.
  - SIFT detector and ORB descriptor do not work together

- The matcher is "brute force (BF)" and selector is"k nearest neighbors (KNN)".
- Only keypoints on the preceding vehicle in image are processed.



The results of all possible combinations of detectors and descriptors are shown below, with the results being averaged processing time and number of keypoints for each step. Note, there are 10 detector results, 10 descriptor results, but only 9 matcher results, as keypoints matching requires 2 frame images simultaneously.

| Descriptor extraction   | Average time |    (ms)     |         |         |         |
| :---------------------- | :----------: | :---------: | :-----: | :-----: | :-----: |
| **Detector/Descriptor** |    BRIEF     |     ORB     |  FREAK  |  AKAZE  |  SIFT   |
| SHI-TOMASI              |   0.988691   |  0.970024   | 34.8317 |    X    | 11.7726 |
| HARRIS                  |   0.52873    |   0.82866   | 35.7314 |    X    | 10.5432 |
| FAST                    | **1.27674**  | **1.34534** | 36.0595 |    X    | 13.9776 |
| BRISK                   | **0.993699** |   4.50749   | 33.6946 |    X    | 23.5365 |
| ORB                     |   0.653052   |   4.97422   | 34.6063 |    X    | 28.4616 |
| AKAZE                   |   0.742632   |   3.20843   | 37.2606 | 45.8597 | 16.7173 |
| SIFT                    |   1.03263    |      X      | 38.4262 |    X    | 53.3684 |



| Matched keypoints       |   Average   | number  |         |         |         |
| ----------------------- | :---------: | :-----: | :-----: | :-----: | :-----: |
| **Detector/Descriptor** |    BRIEF    |   ORB   |  FREAK  |  AKAZE  |  SIFT   |
| SHI-TOMASI              |   60.7778   | 56.7778 | 40.5556 |    X    | 70.1111 |
| HARRIS                  |   19.2222   |   18    |   16    |    X    | 18.1111 |
| FAST                    | **122.111** | **119** | 97.5556 |    X    | 116.222 |
| BRISK                   | **189.333** | 168.222 | 169.333 |    X    | 182.889 |
| ORB                     |   60.5556   | 84.7778 | 46.6667 |    X    | 84.7778 |
| AKAZE                   |   140.667   | 131.333 | 131.889 | 130.222 | 141.111 |
| SIFT                    |   78.2222   |    X    | 66.1111 |    X    | 89.1111 |



| Total time              | (ms)        |             |         |         |         |
| ----------------------- | ----------- | ----------- | ------- | ------- | ------- |
| **Detector/Descriptor** | BRIEF       | ORB         | FREAK   | AKAZE   | SIFT    |
| SHI-TOMASI              | 15.9493     | 16.0352     | 46.2981 | X       | 24.3293 |
| HARRIS                  | 16.1311     | 18.3939     | 49.146  | X       | 24.953  |
| FAST                    | **2.76489** | **2.89337** | 37.4923 | X       | 15.4976 |
| BRISK                   | **266.379** | 266.203     | 294.392 | X       | 287.341 |
| ORB                     | 26.3151     | 23.6431     | 54.9679 | X       | 48.6818 |
| AKAZE                   | 56.5897     | 58.8057     | 89.2376 | 102.072 | 75.7282 |
| SIFT                    | 64.4931     | X           | 100.142 | X       | 114.903 |

**First Impression according to the above table statistics:**

| **Detector** | Descriptor | Pros                                                         | Cons                                      |
| ------------ | ---------- | ------------------------------------------------------------ | ----------------------------------------- |
| HARRIS       |            |                                                              | Less detected keypoints                   |
| SHI-TOMASI   |            | Relatively many keypoints and  less detection time           |                                           |
| FAST         |            | Very fast detection speed and large amount of detected keypoints |                                           |
| BRISK        | BRISK      | Very good detection precious with larges amount of detected keypoints/ Less extraction time | Long detection time                       |
| ORB          | ORB        | Relatively many keypoints and  less detection time/ Medium extraction time |                                           |
| AKAZE        | AKAZE      |                                                              | Long detection time/ Long extraction time |
|              | FREAK      |                                                              | Long extraction time                      |
| SIFT         | SIFT       |                                                              | Long detection time/ Long extraction time |
|              | BRIEF      | Less extraction time                                         |                                           |


The initial impressions based on the table statistics are:

**FAST + BRIEF**: Higher speed and relative good accuracy
**BRISK + BRIEF**: Higher accuracy
**FAST + ORB**: relatively good speed and accuracy