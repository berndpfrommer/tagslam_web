+++
date = "2015-07-18T14:08:35+02:00"
draft = false
title = "Input Files and Parameters"
weight = 50
+++
# Launch parameters

- ``outbag`` (default: "out.bag") name of output bag file.
- ``playback_rate`` (default: 5) rate for playback of optimized data.
- ``pixel_noise`` (default: 1.0) penality (in pixels) for corner
  projection error. This is how much error you get *per corner per
  tag*. Roughly speaking, the error grows like  ``pixel_noise *
  pixel_error^2``. This is for each corner for each tag observed. So
  if after optimization you are off by 2 pixels on 5 tag corners, your
  total graph error will go up by ``5 * pixel_noise * 4``. Setting a
  smaller pixel_noise will force the optimizer to try harder to move
  the tags corners to the correct position, and consequently care less
  about the prior pose specified, or the odometry measurements.
- ``output_directory`` (default ".") where to write the output files
  to. Note that some files are always written to the default ROS
  directory "~/.ros".
- ``bag_file`` (default: "") name of input bag file. If empty, run in
    online mode.
- ``fixed_frame_id`` (default: "map") ROS frame id of the
- ``max_number_of_frames`` (default: 1000000) stop playing bag after
    this number of frames.

# cameras.yaml
Here's how a typical camera.yaml file looks:

    cam0:
      camera_model: pinhole
      intrinsics: [470.338, 470.521, 639.698, 524.42]
      distortion_model: equidistant
      distortion_coeffs: [-0.013211, -0.00988947, 0.0121051, -0.00452971]
      resolution: [1280, 1024]
      tagtopic: /quadruple/bottom_left/tags
      rig_body: camera_rig
      wiggle_rotation: 0.01
      wiggle_translation: 0.01
    cam1:
      camera_model: pinhole
      intrinsics: [1405.76, 1404.8, 958.042, 607.615]
      distortion_model: radtan
      #                     k1         k2         p1        p2       k3
      distortion_coeffs: [-0.167373, 0.110745, 8.1e-05, 7.34e-05, -0.028]
      resolution: [1920, 1200]
      tagtopic: /pg_17274483/tags
      rig_body: camera_rig

The format is largely identical to
the [Kalibr](https://github.com/ethz-asl/kalibr) format:

- ``camera_model``: must be "pinhole", nothing else is supported
- ``intrinsics``: camera intrinsic parameters: [fx, fy, cx, cy]
- ``distortion_model``:
    - "radtan" or "plumb_bob" for
      the
      [standard radial/tangential model](https://docs.opencv.org/3.4/d9/d0c/group__calib3d.html). The
      parameters are identical in meaning and order to the ones used
      by opencv: $k_1$, $k_2$, $p_1$, $p_2$,  $k_3$.
    - "equidistant" for a fisheye lens. The parameters $k_1 ... k_4$
      are identical in meaning and order to
      [the corresponding OpenCV parameters](https://docs.opencv.org/3.4/db/d58/group__calib3d__fisheye.html) 
- ``resolution``: camera resolution [width, height]
- ``tagtopic:``: the topic under which the tags are published. This
   must match the output topic of the tag detector, or else TagSLAM
   will not receive input data!
- ``rig_body``: the body to which the camera is attached. This must
   match the body name in the ``tagslam.yaml`` configuration file.
- ``wiggle_rotation``: allow the camera to rotate a bit from frame to
  frame. You only need to set this parameter when your rig is not
  flexible, or in particular when your stereo images are not correctly
  synchronized. The "wiggle" will make the camera calibration time
  dependent (within the specified ``wiggle_rotation`` and
  ``wiggle_translation`` noise parameters).
- ``wiggle_translation``: allow the camera pose to translate a bit,
  see ``wiggle_rotation``.


# camera_poses.yaml

At least one of the camera poses most be specified. The pose is the
transform from camera to rig, i.e, $\ctrans{T}{cam}{rig}$.

    cam0:
      pose:
        position:
          x: 0.0
          y: 0.0
          z: 0.0
        rotation:
          x: 0.0
          y: 0.0
          z: 0.0
        R:
          [ 1.0e6, 0.000, 0.000, 0.000, 0.000, 0.000, 
            0.000, 1.0e6, 0.000, 0.000, 0.000, 0.000, 
            0.000, 0.000, 1.0e6, 0.000, 0.000, 0.000, 
            0.000, 0.000, 0.000, 1.0e6, 0.000, 0.000, 
            0.000, 0.000, 0.000, 0.000, 1.0e6, 0.000, 
            0.000, 0.000, 0.000, 0.000, 0.000, 1.0e6]

The pose $\ctrans{T}{cam}{rig}$ is given as rotation and translation
vector.

The R matrix is the square root of the inverse of the noise covariance matrix:
$$
  \Sigma = (R^TR)^{-1}
$$
It reflects the uncertainty (noise) with which the camera pose is
known. The matrix above thus specifies a position uncertainty of about
1mm (first 3 diagonal elements), and a rotational uncertainty of 0.001
rad (diagonal elements 4,5,6). Typically the pose and $R$ matrix are
obtained from a previous extrinsic calibration run using TagSLAM.


# tagslam.yaml

This is the main TagSLAM configuration file.

    tagslam_parameters:
      # optimizer mode: full (full optimization, no iSAM2 used)
      #                 slow (use iSAM2, but test for error
      #                       and use full GTSAM if error is large)
      #                 fast (iSAM2 only, no test for error)
      # default: slow
      optimizer_mode: fast

      # minimum angle [in degrees] between optical axis and tag surface
      minimum_viewing_angle: 12.0

      # number of incremental updates before running
      # a full graph optimization (default: 50)
      max_num_incremental_opt: 50

      # estimated error of tag corner detector
      pixel_noise: 1.0

      # maximum allowed subgraph error. If error is above that,
      # the frame is thrown away, the measurement ignored.
      max_subgraph_error: 50.0

      # Noise with which the subgraph absolute priors will be
      # pinned down. This parameter usually does not need to be
      # touched. Only modify it if there is large odom drift inbetween
	  # tag sightings, and the subgraph error test fails upon loop closure
      subgraph_abs_prior_position_noise: 0.1
      subgraph_abs_prior_rotation_noise: 0.1

	  # by default the header.stamp time stamps of all incoming
	  # image and odometry data must be perfectly synchronized.
	  # Unsynchronized data will be dropped!
	  # If you "set use_approximate_sync" to "true", you can feed
	  # in unsynchronized data as well, and TagSLAM will try to
      # time-align it as well as possible.
	  
      use_approximate_sync: false
  
    body_defaults:
      position_noise: 0.05
      rotation_noise: 0.01
    default_body: lab
    bodies:
       - lab: # name of the body (can be anything!)
         is_static: true
         default_tag_size: 0.125
         pose:
           position:
             x: 0
             y: 0
             z: 0
           rotation:
             x: 0
             y: 0
             z: 0
           position_noise:
             x: 0.0001
             y: 0.0001
             z: 0.0001
           rotation_noise:
             x: 1.0e-05
             y: 1.0e-05
             z: 1.0e-05
         type: simple
         tags:
           - id: 44
             size: 0.15
             pose:
               position:
                 x: 0
                 y: 0
                 z: 0.7
               rotation:
                 x: 0.05732203
                 y: 0.74746230
                 z: 0.02373698
               position_noise:
                 x: 0.001
                 y: 0.001
                 z: 0.001
               rotation_noise:
                 x: 0.1
                 y: 0.1
                 z: 0.1
     - camera_rig:
         is_static: false
         type: simple
         fake_odom_translation_noise: 1.0
         fake_odom_rotation_noise: 1.0

	
The supported keywords are as follows:

- ``tagslam_parameters``
   - ``minimum_viewing_angle`` (default: 20.0) minimum viewing angle (in
     degrees), at which an observed tag is accepted. If the viewing angle
     is smaller than that, the tag will be ignored.
   - ``max_subgraph_error`` (default: 50.0). Before a new measurement
 (i.e. tag observation) is included in the full graph, a subgraph is 
 formed that contains the new measurement, plus the minimum necessary
 part of the existing graph. That subgraph is then optimized. If the
 error is below ``max_subgraph_error``, the new measurement is inserted
 into the full graph. If tag measurements are rejected because they
 exceed ``max_subgraph_error``, you should get a warning in the log
 file. Such warnings are usually a sign of trouble.
   - ``optimizer_mode``
     After a new measurement (e.g. tag observation) has been inserted
     into graph, the graph is optimized. The ``optimizer_mode``
     parameter determines how that optimization is done. Note that
     irrespective of the mode you set here, when you do the rosservice
     "dump" call at the end of the run, TagSLAM will perform a "full"
     optimization no matter what, to make sure that any pose data
     written to the file is fully optimized.
      - "full": non-incremental (full) optimization after each time step.
                This is the slowest and most conservative mode. The
                computation effort quickly grows with time. This mode
                should generally be avoided.
      - "slow": (default) incremental optimization, using iSAM2. A
        single iSAM2 iteration is done, after which the total graph
        error is checked to see if it has increased above a
        threshold. Usually a large increase in error is a sign of
        something going wrong, so in that case, further iSAM2
        iterations are performed to reduce the error. Note that even
        just switching on the error check requires GTSAM to compute
        the error of the graph, which is a big performance hit,
        although still much faster than a full graph optimization. In
        practice, if ``slow`` mode works, then ``fast`` mode usually
        does, too, so you definitely want to try ``fast``.
      - "fast": single incremental iSAM2 optimization step, without computing the
                total graph error. This is the fastest mode, but since
                the total graph error is not known, you won't learn
                right away if some bad detections have entered the
                graph. Use this if the data is clean and you need 
                speed. 
   - ``max_num_incremental_opt`` (default: 100) run full optimizer
       (rather than incremental iSAM2) after every ``max_num_incremental_opt`` frames.
        This is sometimes required to avoid error build up for long
       sequences.
   - ``use_approximate_sync`` (default: false) synchronize data in an
     approximate way. This will allow running TagSLAM on
     unsynchronized data, but beware that lack of synchronization
     usually results in increased error if cameras or bodies move rapidly. 

- ``body_defaults``: here you can specify a default ``position_noise``
  (in meters) and ``rotation_noise`` (rads). This is convenient in
  case you have many bodies with known poses, but don't want to
  specify the pose uncertainty (noise) for every one.
- ``default_body``: specify which body (see below) is the default. If
  tags are  discovered that are not explicitly associated with any body, they
  will be attached to the default body. *If no default body is
  specified, all unknown tags will be ignored*.
- ``bodies``: specifies the list of bodies. See below for keywords
  used to describe a body.
- ``distance_measurements``, ``coordinate_measurements`` etc: specify
  measurements. See the section on measurements for a complete list
  and explanation.
- ``amnesia``: If set to ``true`` (default is ``false``), TagSLAM will
  only maintain a single instance of any time dependent poses
  around. This means that all previous observations are thrown away,
  and only the most recent tag observations are used to determine the
  pose of bodies. Use this option for e.g. state estimation, where all
  static poses are already known in advance, and only non-static poses
  are of interest. This option is also useful for real-time SLAM, but
  you need to have a full map, i.e. the poses of the tags need to be
  given in ``tagslam.yaml``
  
- ``squash``: in case your data set has one particular observation at
  one specific time that throws off the optimizer, you can remove it
  with this option. For example to squash observations of tags 85 and 8 at
  different times, use this snippet:

        squash:
          - time: "1527206556.891942748"
            tags: [85]
          - time: "1527206601.617928627"
            tags: [8]

- ``remap``: remap an observed tag id to another one during certain
  time periods. This feature is very helpful if you have goofed up and
  have the same tag twice in your dataset, but not in the same image
  frame. The following example will remap the observed tag 20 to tag
  id 420 and tag id 520, depending on the time the tag was seen:

        tag_id_remap:
          - id: 20
            remaps:
              - remap_id: 420
                start_time: 1541686885.999522770
                end_time:   1541686936.513937363
              - remap_id: 520
                start_time: 1541687179.947268789
                end_time:   1541687229.942551085

## Body parameters

The following are valid parameters for a body:

- ``is_static``: Must be set to ``false`` for a body that has a time
  dependent world pose, like for instance a moving camera rig. If set
  to ``true``, TagSLAM will only have a single, time-indepent pose for
  this body. Use ``is_static: true`` for any body that does not move.
- ``default_tag_size``: specifies the size of a newly discovered tag
  for which no tag size has been explicitly given. If a body is a default body,
  it must have a valid default tag size.
- ``pose``: the body's world pose $\ctrans{T}{map}{body}$ and pose
  uncertainty.
- ``type``: what type the body is. Currently allowed are:
    - ``simple``: this body has no tags attached to it by default, and
      you must specify attached tags individually
	- ``board``: comes with a set of automatically generated boards,
      specified by parameters like a
      [Kalibr Aprilgrid](https://github.com/ethz-asl/kalibr/wiki/calibration-targets),
      i.e tag size, tag starting id, tag family (``bits``), and
      relative spacing between the tags. Example:

            type: board
            board:
              tag_start_id: 12
              tag_size: 0.12
              tag_bits: 6  # tag family (ignored)
              tag_spacing: 0.25
              tag_rows: 2
              tag_columns: 3
              tag_rotation_noise: 0.001 # rot noise between body and tag
              tag_position_noise: 0.001 # pos noise between body and tag
              pose: # optional: T_board_body (pose of tag 0 wrt body)
                position: # means tag 0 is at body coord (0.0735, 0.0735, 0.0) 
                  x: 0.0735
                  y: 0.0735
                  z: 0
                rotation:
                  x: 0
                  y: 0
                  z: 0
	
- ``tags``: specifies which tags are known to be
  attached to this body. You must specify id and size, but not
  necessarily the pose $\ctrans{T}{tag}{body}$. However, for every
  body at least one tag pose must be specified, or else the body
  cannot be localized. In the 
  following example, tag 44 is specified explicitly, but the pose tag
  45 will be deterimined from the camera observations:

           tags:
           - id: 44
             size: 0.15
             pose:
               position:
                 x: 0
                 y: 0
                 z: 0.7
               rotation:
                 x: 0.05732203
                 y: 0.74746230
                 z: 0.02373698
               position_noise:
                 x: 0.001
                 y: 0.001
                 z: 0.001
               rotation_noise:
                 x: 0.1
                 y: 0.1
                 z: 0.1
           - id: 45
             size: 0.15

- ``fake_odom_translation_noise``: Specify the translational noise of a fake
   odometry pose update for a non-static body. Set this parameter to
   the maximum translation you would expect the body to undergo
   inbetween two measurements. This will impose an identity prior for
   the pose difference
   $$\ctrans{T}{map}{body}(t)\ctrans{T}{body}{map}(t-1),$$ which
   corresponds to an odometry update of identity.
   In practice, this means that TagSLAM will use a body's pose from the
   previous frame to initialize the current frame. Usually that
   helps against poor camera rig initialization when only a single tag
   is visible. However, ``fake_odom`` will break down when body poses
   change while no tags are observed.
- ``fake_odom_rotation_noise``: Specify the rotational noise of a fake
   odometry pose update, see above.

- If odometry pose updates are available (for instance from a VIO
     algorithm running parallel to TagSLAM), they can be fed into
     TagSLAM, which will then perform loop closure. You need to
     specify the ROS topic under which the odometry is stored in the
     bag, the frame id under which the odom should be published, and
     the transform $\ctrans{T}{odom}{body}$ that takes the frame in
     which the odometry updates are expressed (typically the IMU) to
     the body frame. Note that the odometry messages *must have the
     same header time stamps* as the tag detections!

        odom_topic: "/fla/vio/odom"
        odom_frame_id: odom
        odom_acceleration: 1.0
        odom_angular_acceleration: 1.0
        T_body_odom:
           position:
             x:  0.02568531
             y:  0.0238219
             z:  0.026066910
           rotation:
             x:  -7.66219656e-04
             y:  2.84301166e-02
             z:  3.14080874
