+++
date = "2015-07-18T14:08:35+02:00"
draft = false
title = "Examples"
weight = 80
+++

You can find many different usage examples [in the tagslam\_test github repo](https://github.com/berndpfrommer/tagslam_test/tree/master/tests)

1. Simple and quick single-camera test to see that the basics are working</p>
<table><tr><td style="width: 50%;">{{<test_scene test="1">}}</td><td>
<ul>
   <li>cameras: 1 FLIR 1280x1024 monochrome Chameleon 3 USB</li>
   <li>distortion: equidistant</li>
   <li>detector: MIT</li>
   <li>total number of frames: 269</li>
</ul>
</td></tr></table>
- Simple test for stereo extrinsic calibration with about 13 tags
<table><tr><td style="width: 50%;">{{<test_scene test="2">}}</td><td>
   - cameras: 2 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 817
</td></tr></table>
- Same data as test 2, but this time test stereo localization, i.e with known extrinsics
<table><tr><td style="width: 50%;">{{<test_scene test="3">}}</td><td>
   - cameras: 2 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 817
</td></tr></table>
- Wand tracking at the Aviary. Must not set default body to avoid detection of tag boards! This will produce warning messages that are ok
<table><tr><td style="width: 50%;">{{<test_scene test="4">}}</td><td>
   - location: UPenn aviary
   - cameras: 8 PointGrey 1920x1200 GigE BlackFly color
   - distortion: radtan
   - detector: MIT
   - total number of frames: 101
</td></tr></table>
- Tests with InspBot at PERCH. Runs a good while before the first optimization is possible. Does extrinsic calibration on the fly. Short focal length, lots of distortion. A challenging dataset. Runs a long time (20min+), 36000 factors!
<table><tr><td style="width: 50%;">{{<test_scene test="5">}}</td><td>
   - location: PERCH
   - cameras: 4 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 2751
</td></tr></table>
- Tests with InspBot at PERCH: extrinsic camera calibration over april grid board. Takes about 500s for optimization, with about 64000 factors.
<table><tr><td style="width: 50%;">{{<test_scene test="6">}}</td><td>
   - location: PERCH
   - cameras: 4 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 1546
</td></tr></table>
- Tests with InspBot at PERCH: now with pre-calibration, easy. Has about 3800 factors.
<table><tr><td style="width: 50%;">{{<test_scene test="7">}}</td><td>
   - location: PERCH
   - cameras: 4 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 454
</td></tr></table>
- Tests with InspBot at PERCH: extrinsic calibration with low fps. Has about 2712 factors. Pushes the limits of the initialization: you cannot run in "fast"mode. NOTE: this image is WRONG, fix me!
<table><tr><td style="width: 50%;">{{<test_scene test="8">}}</td><td>
   - location: PERCH
   - cameras: 4 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 160
</td></tr></table>
- Tests with OVC: large run with no odom, but tags fixed. Some outlier rig poses. Some really large errors for tags 78, 104, 103, 57. The tags may be out of place. Needs more investigation. Runs reasonably fast.
<table><tr><td style="width: 50%;">{{<test_scene test="9">}}</td><td>
   - location: Building 227 UPenn/Grays Ferry
   - cameras: Open Vision Computer 2 x 1280x1024 monochrome Python 3000, 2.8mm lens
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 16485
</td></tr></table>
- Mapping run for building 227 tags, with high-res monocular camera.
<table><tr><td style="width: 50%;">{{<test_scene test="10">}}</td><td>
   - location: Building 227 UPenn/Grays Ferry
   - cameras: FLIR 1920x1200 USB with 8mm(?) lens
   - distortion: radtan
   - source bag: 2018-11-08-08-57-25_mapping_pg.bag
   - detector: MIT
   - total number of frames: 3579
</td></tr></table>
- First tests at Building 227 with OVC, using odometry. There are some jumps in the odom, order of 0.25m. Make sure to reply, the kinks in the optimized trajectory are much smaller.
<table><tr><td style="width: 50%;">{{<test_scene test="11">}}</td><td>
   - location: Building 227 UPenn/Grays Ferry
   - cameras: Open Vision Computer 2 x 1280x1024 monochrome Python 3000, 2.8mm lens
   - distortion: equidistant
   - source bag: dupont_2018-11-09-10-36-42_short_3m_drift.bag
   - detector: MIT
   - total number of frames: 4499
</td></tr></table>
- Same data as in example 11, but only 1 tag is fixed, the rest are discovered.
<table><tr><td style="width: 50%;">{{<test_scene test="12">}}</td><td>
   - location: Building 227 UPenn/Grays Ferry
   - cameras: Open Vision Computer 2 x 1280x1024 monochrome Python 3000, 2.8mm lens
   - distortion: equidistant
   - source bag: dupont_2018-11-09-10-36-42_short_3m_drift.bag
   - detector: MIT
   - total number of frames: 4499
</td></tr></table>
- Tag board pose discovery at UPenn aviary with hand-held monocular camera.
<table><tr><td style="width: 50%;">{{<test_scene test="13">}}</td><td>
   - location: UPenn Aviary
   - cameras: FLIR 2048x2048 USB 3 at 2.3Hz
   - distortion: radtan
   - source bag: 2018-05-24-20-01-00_aviary_tags.bag
   - detector: MIT
   - total number of frames: 681
</td></tr></table>
- InspBot at the actual pennstock. Uses plane measurements and fake odom. Many frames have no observations. This image is WRONG, fix me!
<table><tr><td style="width: 50%;">{{<test_scene test="14">}}</td><td>
   - location: PERCH
   - cameras: 4 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - detector: MIT
   - total number of frames: 5184
</td></tr></table>
- State estimation experiments for moving block. First to use "amnesia" feature.
<table><tr><td style="width: 50%;">{{<test_scene test="15">}}</td><td>
   - location: 403 Levine, GRASP
   - cameras: 2 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: radtan
   - detector: MIT
   - total number of frames: 235
</td></tr></table>
- Accuracy demo for localization with 5 linearly arranged tags.
<table><tr><td style="width: 50%;">{{<test_scene test="16">}}</td><td>
   - location: 11 Nolen Ln
   - cameras: Ximea MQ013CG-E2 color camera, 1280x1200 Python 3000 color
   - distortion: radtan
   - detector: MIT
   - total number of frames: 297
</td></tr></table>
- Accuracy demo for localization with 3 linearly arranged tags.
<table><tr><td style="width: 50%;">{{<test_scene test="17">}}</td><td>
   - location: 11 Nolen Ln
   - cameras: Ximea MQ013CG-E2 color camera, 1280x1200 Python 3000 color
   - distortion: radtan
   - detector: MIT
   - total number of frames: 297
</td></tr></table>
- State estimation for tumbling block, from the TagSLAM documentation. Uses amnesia feature.
<table><tr><td style="width: 50%;">{{<test_scene test="18">}}</td><td>
   - location: 11 Nolen Ln
   - cameras: Ximea MQ013CG-E2 color camera, 1280x1200 Python 3000 color
   - distortion: radtan
   - detector: MIT
   - total number of frames: 1085
</td></tr></table>
- Long SLAM run, with carefully mapped tags. This is the data used for the performance tests on loop clouse (TagSLAM paper). Can run in "fast" mode without any intermediate optimization.
<table><tr><td style="width: 50%;">{{<test_scene test="19">}}</td><td>
   - location: Building 227 UPenn/Grays Ferry
   - cameras: Open Vision Computer 2 x 1280x1024 monochrome Python 3000, 2.8mm lens
   - distortion: equidistant
   - source bag: dupont_2018-11-09-11-34-55.bag
   - detector: MIT
   - total number of frames: 12714
</td></tr></table>
- THIS TEST EXITS WITH AN ERROR! This is on purpose, to test handling of optimizer going singular. Also exercise planar constraint and camera-selective remapping. To make it run successfully, reduce the rotation_noise on tag 45 to 0.1.
<table><tr><td style="width: 50%;">{{<test_scene test="20">}}</td><td>
   - location: Pennstock
   - cameras: 4 FLIR 1280x1024 monochrome Chameleon 3 USB
   - distortion: equidistant
   - source bag: day2
   - detector: MIT
   - total number of frames: 3000
</td></tr></table>
</td></tr></table>
- Handheld walk up and down stairs with realsense T265 stereo camera
and two TOF sensors. This is the first test of the
approximate sync feature.
<table><tr><td style="width: 50%;">{{<test_scene test="21">}}</td><td>
   - location: 11 Nolen Ln
   - cameras: 2 TOF 224 x 171 @ 5Hz, 1 Intel RealSense T265 848x800 approx-synced to 5Hz
   - distortion: equidistant
   - source bag: t265_pico_2020-05-13-11-30-07_0.bag
   - detector: MIT
   - total number of frames: 431
</td></tr></table>
