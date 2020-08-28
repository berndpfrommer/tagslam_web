+++
date = "2015-07-18T14:08:35+02:00"
draft = false
title = "Caveats and Limitations"
weight = 30
+++
# Limitations
<a name="limitations"></a>
The following limitations apply when using TagSLAM:

1. No duplicate tag ids are allowed! A tag can only exist in the
world once. You must make sure that there are no duplicate tags
anywhere.
- To use odometry with TagSLAM, the odometry updates should preferably
be synchronized with the tag detections, for example the odometry
should be computed from the same camera that does the camera
recording. Starting release 2.1 TagSLAM accepts *approximately*
synced data as well. This means that TagSLAM first filters the data
approximately by running a filter identical to the [ROS
ApproximateTime
synchronizer](http://wiki.ros.org/message_filters/ApproximateTime). The
frequency of the slowest sensor will determine the frequency of
TagSLAM pose updates, and higher-frequency data inbetween will be
dropped. If you want to have higher frequency data, you need to insert
for instance *empty* tag detection messages. Note that by default the
``use_approximate_sync`` feature is disabled, so if you feed in
unsynchronized data *all* of it will be dropped.
- No mixing of AprilTag families! For a single data set, all tags must
be of the same family
- All tags must have the same number of border bits.
- Real-time usage is limited to situations where either the graph is
  so small that it can be solved quickly (maybe a thousand frames
  maximum!), or the pose of the tags can be determined beforehand,
  offline, with a "mapping run" that builds the full graph, and then
  stores the optimized tag poses for a subsequent real-time run. To
  get real-time performance, use the "amnesia" feature such that old
  information gets thrown away and the graph stays small.


Note: restrictions 3. and 4. are due to the tag
detector, and if fixed there, TagSLAM should be able to handle it.

