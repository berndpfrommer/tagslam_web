+++
date = "2015-07-18T14:08:35+02:00"
draft = false
title = "A. Hints for Better Localization"
+++
Paying attention to the details when using tags for localization can
make a big difference for the quality of the results!

# Camera and Lighting
Some important points when selecting a camera:

- Lighting: illuminate the tags as brightly as possible.
  The more light, the shorter the shutter times, the less motion blur. Avoid
  backlight when possible.
- Check that your camera is focused correctly for the distance you
  need. When in doubt, set focus to a distances a little longer than
  you expect (focus is most important for far away tags)
- Use a camera with global shutter, not rolling shutter. Most consumer
  cameras (webcams, GoPros etc) have rolling shutters that
  can [distort objects](https://www.youtube.com/watch?v=LVwmtwZLG88)
  that are in motion.
- Short shutter times are crucial for avoiding motion blur. To this end, you want:
   - a large sensor with high quantum efficiency. The FLIR machine
	 vision website (formerly PointGrey) has excellent sensor
	 comparisons.
   - a large aperture lens (but beware of focus!)
- Auto focus cameras are to be avoided because their calibration could
  change while recording.
- If using multiple cameras, they must be synchronized (use hardware
  sync!), because TagSLAM cannot handle unsynchronized camera streams!
- Higher resolution is better. If the resolution is too low, you may
  not be able to detect any tags.
- Generally wide-angle lenses (up to 120deg) are preferred, but they
  must be used in combination with a high resolution sensor.

# Tag placement
Here some tips on tag placement:

- always have multiple tags in view. Localizing from a single tag is very fragile.
- make sure tags are visible (unoccluded) from all points on the camera path.
- avoid placing tags where there is a lot of backlight
- space tags as far from each other as possible, but in such a way
  that always multiple tags are still visible.
- resist the temptation to arrange your tags in a linear fashion, for example leaning them against the wall on the floor. TagSLAM uses a triangulation based approach, which works best with ... triangles. The following video shows that better results can be obtained with 3 tags in a triangular arrangement than from 5 tags that are collinear.

<div style="position: relative; padding-bottom: 56.25%; height: 0;">
<iframe src="//www.youtube.com/embed/ti4PFfSBZyk?autoplay=0" style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; border:0;" allowfullscreen title="TagSLAM on linear vs triangular tags"></iframe>
</div>
