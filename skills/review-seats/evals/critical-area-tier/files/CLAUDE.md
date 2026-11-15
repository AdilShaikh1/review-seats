# pose-kit

pose-kit is a small Python library that converts robot poses between quaternion, matrix and position forms. Its
conversion helpers live in `pose_kit/convert.py`.

## Review

The overseer reviews as the "Head of Robotics and 3D Perception" persona.

## Critical areas

- **Coordinate frames and transforms.** Extrinsics, frame names, rotation conventions (quaternion component order,
  Hamilton vs JPL, the q vs -q sign, Euler sequence, active vs passive), handedness, transform direction (`T_a_b`
  vs `T_b_a`), and the frame a covariance is expressed in. A swapped quaternion order or an inverted transform
  still gives a valid rigid transform, so every later pose is wrong with no error.
- **Camera and sensor models.** Intrinsics, distortion, projection and unprojection, pixel-centre conventions,
  z-depth vs ray-range depth. A half-pixel offset shifts reprojections by an amount that looks like noise, and an
  inverted distortion model bends lines near the image edges.
- **Units and scales.** Metres vs millimetres, radians vs degrees, nanoseconds vs seconds, depth scale. A depth in
  millimetres read as centimetres puts a wall at 20 m instead of 2 m, which can still look like a real scene.
- **Time.** Timestamps, clock domains, sensor sync, interpolation, latency compensation. A scan paired with a pose
  from another clock domain is off by a delay that can also drift, and the error grows with speed and angular rate.
- **Calibration data.** Its loading, versioning and defaults. A missing file that falls back to identity extrinsics
  or default intrinsics, or a stale calibration version, loads without an error.
- **Numerical robustness.** float32 vs float64, near-singular solves (SVD, PnP), normalisation, epsilons, NaN and
  Inf handling. A solve on near-degenerate points returns a finite wrong answer, and one unchecked NaN spreads
  through every later estimate.
- **Estimation and registration.** ICP, pose graphs, bundle adjustment, solver settings, outlier thresholds. A
  tighter outlier threshold drops good matches and lowers the residual, so a worse pose looks like a better one.
- **Data layout.** Point-cloud and image memory layout (row vs column major, strides, channel order), (row, col) vs
  (x, y) indexing, serialisation formats. Swapped RGB and BGR, a wrong stride or a padded point field misreads every
  pixel or point.
- **Real-time paths.** Concurrency, queues, GPU kernels, and hot loops where timing is part of correctness. A queue
  that drops or reorders messages under load, or a missing stream sync, passes offline tests and fails live.
- **Sensor and hardware I/O.** Drivers, device config, firmware interfaces. A changed trigger mode, exposure or
  byte order changes the data's timing, noise or values without a code error.
