+++
date = "2015-07-18T14:08:35+02:00"
draft = false
title = "4. Intrinsic calibration"
+++
# Intrinsic calibration
TagSLAM can *not* perform intrinsic calibration. You must supply
the intrinsic parameters in the ``cameras.yaml`` file.

If you are dealing with a "normal" lens, it is recommended that you use the
ROS [camera_calibration](http://wiki.ros.org/camera_calibration)
package for intrinsic calibration. It can do the standard ``radtan``
(``plumb_bob``) radial-tangential distortion model and is the easiest tool to
use. However, it's output file must be converted to work with TagSLAM

If you are dealing with a fisheye (``equidistant``)
lens, consider using  [Kalibr](https://github.com/ethz-asl/kalibr) or the
[multicam_calibration](https://github.com/KumarRobotics/multicam_calibration)
tool. Kalibr and multicam_calibration will already give you the output in a
format that is close to the one that TagSLAM uses.


## Converting ROS calibration files
After running the ROS calibration you will end up with a file that is
called ``ost.yaml``. It will look like this:

    image_width: 1920
    image_height: 1200
    camera_name: pg_17408093
    camera_matrix:
      rows: 3
      cols: 3
      data: [2168.758281, 0.000000, 990.688651, 0.000000, 2166.979687,
    583.914090    , 0.000000, 0.000000, 1.000000]
    distortion_model: plumb_bob
    distortion_coefficients:
      rows: 1
      cols: 5
      data: [-0.189067, 0.124915, 0.000043, 0.000296, 0.000000]
    rectification_matrix:
      rows: 3
      cols: 3
      data: [1.000000, 0.000000, 0.000000, 0.000000, 1.000000,
    0.000000, 0.000000    , 0.000000, 1.000000]
    projection_matrix:
      rows: 3
      cols: 4
      data: [2094.321777, 0.000000, 993.054257, 0.000000, 0.000000, 2136.192139, 583.488844, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000]

This file must now be converted to a format that TagSLAM can process:

    rosrun tagslam camerainfo_to_kalibr.py -i ost.yaml -o cameras.yaml

The resulting file should look like this:

    cam0:
      camera_model: pinhole
      intrinsics: [2168.75828, 2166.97969, 990.68865, 583.91409]
      distortion_model: plumb_bob
      distortion_coeffs: [-0.18907, 0.12491, 0.00004, 0.00030, 0.00000]
      resolution: [1920, 1200]
      tagtopic: /pg_17408093/tags
      rig_body: XXX_NAME_OF_RIG_BODY

You will need to adjust the ``tagtopic`` to match the actual ROS topic
under which the extracted tags are published. The ``rig_body`` must be
set to match the name of the TagSLAM body to which the camera is
attached.
