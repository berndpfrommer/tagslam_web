+++
date = "2015-07-18T14:08:35+02:00"
draft = false
title = "7. Output"
+++

# Console output

If you run TagSLAM, you will get output like this on the console


    [ INFO] [1565030387.934526496]: sum of subgraph err: 0.136667, full graph error: 0.142
    [ INFO] [1565030387.934555291]: graph after update: opt fac: 22 unopt fac: 0 opt vals: 17 unopt vals: 0


Note that the full graph error is the total error over all frames, all
tags, all cameras, so this can easily grow into the thousands after a
while. If you have set the pixel noise to about 1.0, the error will
roughly correspond to the sum of all pixel errors. To understand the
where the error comes from, look at ``error_map.txt`` (see below).

To get more detailed output, switch on debug logging for tagslam by
putting the following file into ``~/.ros/config/rosconsole.config``
(or wherever your ``ROSCONSOLE_CONFIG_FILE`` environment variable
points to):

    # Set the logging to debug
    log4j.logger.ros.tagslam=DEBUG

# ROS topics published

TagSLAM publishes the following standard ROS messages:

 - ``/clock [rosgraph_msgs/Clock]`` when running from a bag
 - ``odom/body_xxxxx [nav_msgs/Odometry]`` the odometry of body xxxxx
 - ``/tf`` transforms

# ROS services

The following ROS service calls are implemented:

 - ``dump``: causes TagSLAM to write all output files to ``~/.ros/``
 - ``replay``: replays all odometry and transform messages at fixed
     rate (configurable via parameter "playback_rate")

Example usage:

    rosservice call /tagslam/replay


# Output files

Here's a list of output files that are generated in ``~/.ros/``

## tag_corners.txt

Has the tag corner image coordinates for each time stamp

     # time_stamp tagid cameraid corner u v
     1560889276.319019864 1 0 0 526.064 392.177
     1560889276.319019864 1 0 1 598.609 394.595
     1560889276.319019864 1 0 2 601.158 323.382
     1560889276.319019864 1 0 3 529.04  321.199
     1560889276.319019864 0 0 0 334.639 386.202
     1560889276.319019864 0 0 1 406.204 387.176
     1560889276.319019864 0 0 2 409.254 316.627
     1560889276.319019864 0 0 3 338.006 315.4

## error_map.txt

This file is very useful for figuring out what's causing errors. It
gives the optimizer error for all factors in the graph. For tag
projection factors, and error of 1 corresponds to about 1 pixel
reprojection error.

    # format:
    # optimzer_err timestamp factor_name
	#
	# absolute pose prior example: 
    0.000 0.000000000  app:cam:cam0,t:0
	# 
	# relative pose prior example
    0.000 1560889304.217737610  rpp:cam:cam0,t:304217
	#
	# projection factor example:
    0.142 1560889286.623750816  proj:cam0-tag:0,t:286623
    0.001 1560889297.422034803  proj:cam0-tag:2,t:297422

## poses.yaml

Has the latest optimized poses of all static bodies. Also has all
optimized tag poses.

## camera_poses.yaml

Has the latest optimized camera poses.

## out.bag

Bag with odometry and transform messages. Needs a ros service call to
``dump``.
