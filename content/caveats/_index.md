+++
date = "2015-07-18T14:08:35+02:00"
draft = false
title = "B) Caveats"
+++
# Limitations

The following limitations apply when using TagSLAM:

- No duplicate tag ids allowed. A tag can only exist in the
world once.

- No mixing of AprilTag families. For a single data set, all tags must
be of the same family

- All tags must have the same number of border bits.

Note: the last two restrictions are due to the tag
detector, and if fixed there, TagSLAM should be able to handle it.

