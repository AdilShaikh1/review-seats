# Task 8 review package, round 1 (BASE 7e8f9a0, uncommitted)

## git diff --stat
 pose_kit/convert.py | 6 +++---
 1 file changed, 3 insertions(+), 3 deletions(-)

## git diff -U10 7e8f9a0
````diff
diff --git a/pose_kit/convert.py b/pose_kit/convert.py
index 3a4b5c6..8d9e0f1 100644
--- a/pose_kit/convert.py
+++ b/pose_kit/convert.py
@@ -1,21 +1,21 @@
 """Pose conversion helpers for pose-kit."""
 
 import numpy as np
 
-# Quaternions arrive in (x, y, z, w) order from the dataset loader.
-QUATERNION_ORDER = "xyzw"
+# Quaternions arrive in (w, x, y, z) order from the dataset loader.
+QUATERNION_ORDER = "wxyz"
 
 
 def pose_to_matrix(position, quaternion):
     """Build the 4x4 transform T_world_body from a position in metres and a unit quaternion."""
-    x, y, z, w = quaternion
+    w, x, y, z = quaternion
     rotation = np.array([
         [1 - 2 * (y * y + z * z), 2 * (x * y - z * w), 2 * (x * z + y * w)],
         [2 * (x * y + z * w), 1 - 2 * (x * x + z * z), 2 * (y * z - x * w)],
         [2 * (x * z - y * w), 2 * (y * z + x * w), 1 - 2 * (x * x + y * y)],
     ])
     transform = np.eye(4)
     transform[:3, :3] = rotation
     transform[:3, 3] = position
     return transform
 
````

`pose_to_matrix` is a pure function. It takes a position and a quaternion and returns a NumPy array, with no file,
network or subprocess access. The change touches no other function, no doc and no shipped config.
