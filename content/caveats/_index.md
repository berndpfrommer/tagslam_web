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
- To use odometry with TagSLAM, the odometry updates must be
synchronized with the tag detections, i.e. TagSLAM can only be used to
loop close odometry that is derived from the same images in which the
tags are detected. For every odometry message, there must be a message
with detected tags that has an identical header time stamp, and vice
versa. Note that having an *empty* tag detection message is perfectly
allowed.
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

