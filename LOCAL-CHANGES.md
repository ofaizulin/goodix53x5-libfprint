# Goodix 53x5 reliability changes

These changes improve repeated fingerprint authentication on a Dell XPS 9570
with a Goodix 27c6:5395 reader, tested on Fedora 44.

## Behavior

- Smooth sensor images with Gaussian sigma 1.2 before CLAHE/SIFT extraction.
- Require template format version 2; re-enroll fingerprints after upgrading.
- Store 24 enrollment samples to cover more fingertip placements.
- Capture up to three images during one authentication press, spaced 150ms
  apart after failures, reusing the original no-finger reference.
- Preserve the SIGFM acceptance threshold of 150 and geometric matching.
- Check cancellation before each authentication capture.

Keep the finger in contact until the result. The extra captures may add time
to an unsuccessful authentication. More enrollment samples and capture attempts
increase the number of comparisons; an unchanged per-comparison threshold
does not establish an unchanged overall false-accept rate.

## Validation

The driver builds against libfprint v1.94.10. Existing SIGFM extraction and
geometric matcher tests, plus libfprint device and state-machine tests, pass.
These tests do not simulate the new capture burst on physical hardware.

Small local offline comparisons improved scores for usable enrolled-finger
captures while rejecting the tested unenrolled-finger captures. Live testing
completed five screen unlocks with two failed attempts total across those
unlocks. First-touch accuracy is still imperfect. This is a convenience driver,
not a comprehensive biometric security evaluation.

## Private local diagnostics

By default, the driver does not save fingerprint images. Setting
`GOODIX_DEBUG_CAPTURE_DIR` opts into saving raw, reference, and processed PGM
images, capped at 32 captures per daemon process. New files use mode 0600 and
new directories use mode 0700. Use a private directory and unset the variable
after diagnosis. Do not publish these images or serialized fingerprint prints.

This repository contains source code and documentation only; local diagnostic
captures, saved prints, and system logs are excluded. Upstream copyright and
LGPL-2.1-or-later licensing apply.
