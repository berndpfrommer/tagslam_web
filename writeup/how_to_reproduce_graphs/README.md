# Notes on how to recreate the graphs

## block with scene

The configuration is straight from test_18 of the ``tagslam_tests``
repository. For visualization, use the ``scene_with_block.rviz`` in the
current directory.

## vio/tagslam/orb-slam2 graph

This is from ``test_19``, original bag: ``dupont_2018-11-09-11-34-55.bag``

### running the odom

The odom was run with the parameters as found in ``vio_ovc.launch``.
The calibration file is linked to via ``calib.yaml``. To record the odom:

    rosbag record -O ./${bag}_odom.bag -e "/vio/odom|/tf"

Run the bag by hand, playing the bag with

   rosbag play -r 0.2 --clock

### running orb-slam2

You need to run the undistort node, make sure to place the
``undistort_calib.yaml`` file in the right place

    roslaunch undistort_ovc.launch 

The orb-slam2 config file and launch files is also here.

Orb slam only produces poses, you need to convert them to odom like so:

    rosrun grasp_multicam pose_to_odom.py -c 10000000 --outbag orbslam2_odom.bag --pose_topic /orb_slam2_stereo/pose --odom_topic /orbslam_2/odom --parent_frame orbslam2_world --child_frame orbslam2 dupont_2018-11-09-11-34-55_orbslam2.bag


### visualization

For visualization, use the file ``tagslam_ovc_topdown.rviz`` in the
current directory. Note: not the latest version, may need adjustment

To provide the static transforms between the TagSLAM2 and the
VIO/orb-slam2 coordinate systems:

    rosrun tf static_transform_publisher -1.12625 0.4480 0.832 0.072 -0.25 0 map orbslam2_world 1000
    rosrun tf static_transform_publisher -1.12625 0.4480 0.832 1.605 0 0 map vision 1000
