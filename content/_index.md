---
date: 2017-10-17T15:26:15Z
lastmod: 2018-12-05T15:26:15Z
---

# TagSLAM: Flexible SLAM with tags

TagSLAM is a ROS based package for simultaneous multi-camera
localization and mapping (SLAM) with the popular
[AprilTags](https://april.eecs.umich.edu/software/apriltag). In
essence TagSLAM is a front-end to
the [GTSAM optimizer](https://borg.cc.gatech.edu/) which makes it easy
to use AprilTags for visual SLAM. For more technical details, have a
look at [this draft paper](media/tagslam.pdf).

If you have a standard visual SLAM problem and want to use fiducial markers,
you should probably *NOT* use TagSLAM because there are packages
out there that are better suited. Consider for instance
using [UcoSLAM](http://ucoslam.com) (disclosure: I haven't actually
used it yet). It runs in real-time, and uses keyframes, which should
make it scale to larger trajectories. Unlike TagSLAM, it can use both,
features in the wild, and tags for loop closure, not just tags. It's a
one-stop shop. Also TagSLAM can really only be used in real-time for
relatively short trajectories, or if the mapping is done beforehand
such that the location of the tags is known.

If TagSLAM is really inferior at hard core SLAM, then why *should* you ever use
it? Here's an example of TagSLAM being used to map tags in a dark tunnel,
and localize a hexacopter, solely off of the tags:

<div style="position: relative; padding-bottom: 56.25%; height: 0;">
<iframe src="//www.youtube.com/embed/51z1V-Jb3c8?autoplay=0" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border:0;" allowfullscreen title="TagSLAM on aerial robot"></iframe>
</div>

There are hardly any features visible on the walls of the tunnel,
meaning SLAM has to work off of the tags only. Few SLAM systems can
support 4 cameras, but TagSLAM supports a virtually arbitrary number
of cameras. In this example, there is no overlap between the field
of view of some cameras, but TagSLAM can handle the extrinsic
calibration of all cameras.

But really, the biggest reason to use TagSLAM is its
flexibility. You can for instance combine VIO odometry from another
component and feed it into TagSLAM, along with *synchronized* images
(see section on [Caveats and Limitations](caveats/)), to arrive at
a loop-closed trajectory. Or you can have multiple robots carrying
cameras. Or you can do state estimation, i.e. stick tags on objects
and track their poses optically, without using a motion capture
system. And you can do this while the camera is moving as well. Have a
look below at some of the examples.

## Mapping
Walk around with a camera and discover tag poses whenever
multiple tags are visible in the same frame.
<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden;">
  <iframe src="//www.youtube.com/embed/BxrG-QItMSo?autoplay=0" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border:0;" allowfullscreen title="TagSLAM mapping"></iframe>
</div>

## Localization
If you have the tags mapped already, you can enter their poses as fixed
input, and now just localize off those tags.

## Loop closing on visual odometry
If a visual/inertial odometry trajectory and the tag detections *are
computed from the same camera frames*, TagSLAM can perform loop
closure. In the following video, the tags have been mapped beforehand
with a laser pointer.
<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden;">
  <iframe src="//www.youtube.com/embed/rLcJFse74X4?autoplay=0"
  style="position: absolute; top: 0; left: 0; width: 100%; height:
  100%; border:0;" allowfullscreen title="TagSLAM loop closure"></iframe>
</div>


## Extrinsic camera calibration
TagSLAM supports *synchronized* (only!)  multi-camera systems, and
therefore can be used to find the relative  poses of multiple
cameras. No special calibration target is needed other than AprilTags
scattered around. No overlap between the cameras field of view is required.
<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden;">
  <iframe src="//www.youtube.com/embed/W7IGJZBSuvM?autoplay=0" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border:0;" allowfullscreen title="TagSLAM extrstate estimation"></iframe>
</div>

## State estimation
### Pose estimation of block from single, moving camera
TagSLAM can estimate the pose of an object that has tags attached to
it. Here an example of a block pose estimation:\\
<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden;">
  <iframe src="//www.youtube.com/embed/ul9THWqCOQY?autoplay=0" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border:0;" allowfullscreen title="TagSLAM state estimation"></iframe>
</div>

### Pose estimation of wand with eight static cameras
Another example of state estimation, at the UPenn Aviary, using 8 stationary cameras that are extrinsically calibrated off AprilTag boards, while
simultaneously localizing a moving wand with tags attached to it:
<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden;">
  <iframe src="//www.youtube.com/embed/4Z2Pf9ClcbI?autoplay=0" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border:0;" allowfullscreen title="TagSLAM at UPenn Aviary"></iframe>
</div>
