# Critical areas

A critical area is a part of a codebase where a short diff can silently break correctness. The code still runs and
the tests still pass, but the output is wrong. A quaternion read in the wrong component order still gives a valid
rotation, and a scaler fitted before the data split still trains a model. SKILL.md section 5 sets how a touched
area changes the model tiers.

## How a project lists them

Each project lists its own critical areas in its CLAUDE.md, next to the overseer persona and the term list. Use a
`## Critical areas` heading and one bullet per area. Each bullet names the area and what it covers, then says what
can silently break. A bullet may also name the paths it covers. A diff touches an area when it changes the area's
behaviour or a convention its comments or docs state. A typo fix in a comment inside the area doesn't touch it.
Deleting or skipping a test that checks a critical area, or changing its tolerance, threshold, golden value or
input fixture, also touches that area. Renaming or reformatting a test doesn't.

````markdown
- **Coordinate frames and transforms** (`src/geometry/`). Extrinsics, quaternion component order, transform
  direction. An inverted transform still runs and moves every point by the wrong pose.
````

Copy entries from the lists below and trim them to the project. List only the parts where a wrong result would
still pass the tests. A list that names every module sends almost every behaviour change to the most capable model.

## Example: 3D perception

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

## Example: machine learning and data science

- **Data splits and leakage.** Train, validation and test boundaries, time-based or grouped splits, scalers and
  encoders fitted on training data only, and features that leak information from the future. A random split over
  time-ordered or per-scene data, or a scaler fitted before the split, inflates validation scores and nothing fails.
- **Label and class mapping.** Class index order, label encodings, ignore indices. A reordered class list or a
  refitted label encoder swaps classes between training and inference, and an ignore index such as 255 or -100
  remapped onto a real class trains the model on unlabelled pixels.
- **Preprocessing and normalisation statistics.** Mean and std, scalers, tokenisers, and the versions of each.
  Statistics or a tokeniser at inference that differ from training shift every input, with no error.
- **Tensor shapes, dtypes, devices and broadcasting.** NCHW vs NHWC, silent broadcasts, mixed precision. A `(N,)`
  target minus a `(N, 1)` prediction broadcasts to `(N, N)`, and a mean reduction hides the wrong shape.
- **Loss and metric definitions.** Reductions, class weights, micro vs macro averaging, decision thresholds,
  per-rank metric averaging. A sum reduction in place of a mean scales the gradients by the number of summed
  terms, which changes the effective learning rate under SGD and barely changes training under Adam.
- **Randomness and reproducibility.** Seeds, nondeterministic ops, data order, `DistributedSampler` without
  `set_epoch`. A `np.random.Generator` created in the dataset's `__init__` is copied into every data-loader worker,
  so each worker repeats the same augmentations.
- **Augmentation consistency.** Geometric augmentations must also transform the labels, boxes, masks, keypoints or
  poses. A horizontal flip that moves the image but not the boxes, or that doesn't swap left and right keypoints,
  trains on wrong labels.
- **Checkpoints and model I/O.** State-dict keys, strict loading, export formats, inference run in train mode
  (dropout, batch norm). Non-strict loading after a layer rename leaves that layer at its random init with no error.
- **Sampling and weighting.** Stratification, class balancing, deduplication. Oversampling before the split copies
  rows into validation, and near-duplicates across splits inflate scores.
- **Schema and feature drift.** Column order, the handling of absent values, and categorical encodings. A model
  that reads features by position takes a reordered column as a different feature.
- **Evaluation and post-processing.** Box formats (`xyxy` vs `xywh`, pixel vs normalised), IoU and score
  thresholds, NMS settings, the evaluation script. A box format mix-up or a changed IoU threshold moves the
  reported mAP without any change to the model.
