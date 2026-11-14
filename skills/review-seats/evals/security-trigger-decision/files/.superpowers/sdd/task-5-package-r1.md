# Task 5 review package, round 1 (BASE c4d5e6f, uncommitted)

## git diff --stat
 tidy_csv/cleaner.py | 6 +++---
 1 file changed, 3 insertions(+), 3 deletions(-)

## git diff -U10 c4d5e6f
```diff
diff --git a/tidy_csv/cleaner.py b/tidy_csv/cleaner.py
index 2b3c4d5..6e7f8a9 100644
--- a/tidy_csv/cleaner.py
+++ b/tidy_csv/cleaner.py
@@ -28,10 +28,10 @@ def clean_csv(path, dedupe=False):
 
 def normalize_row(row):
     """Strip whitespace and stray carriage returns from one row's cells."""
-    tmp = [cell.strip() for cell in row]
-    tmp = [cell.replace("\r", "") for cell in tmp]
-    return tmp
+    stripped_cells = [cell.strip() for cell in row]
+    stripped_cells = [cell.replace("\r", "") for cell in stripped_cells]
+    return stripped_cells
```

`normalize_row` is a pure function. It takes a list of strings and returns a list of strings, with no file,
network or subprocess access. The rename touches no other function, no doc and no shipped config.
