# Task 9 review package, round 1 (BASE 7e8f9a0, uncommitted)

## git diff --stat
 pose_kit/convert.py | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

## git diff -U10 7e8f9a0
````diff
diff --git a/pose_kit/convert.py b/pose_kit/convert.py
index 3a4b5c6..5b6c7d8 100644
--- a/pose_kit/convert.py
+++ b/pose_kit/convert.py
@@ -14,12 +14,12 @@ def pose_to_matrix(position, quaternion):
         [2 * (x * y + z * w), 1 - 2 * (x * x + z * z), 2 * (y * z - x * w)],
         [2 * (x * z - y * w), 2 * (y * z + x * w), 1 - 2 * (x * x + y * y)],
     ])
     transform = np.eye(4)
     transform[:3, :3] = rotation
     transform[:3, 3] = position
     return transform
 
 
 def matrix_to_position(transform):
-    """Retrun the translation part of a 4x4 transform, in metres."""
+    """Return the translation part of a 4x4 transform, in metres."""
     return transform[:3, 3].copy()
````

The change fixes one misspelled word in a docstring. It changes no code, no other doc and no shipped config.
