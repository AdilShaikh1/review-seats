# Task 4 review package, round 1 (BASE 7a2f9e1, uncommitted)

## git diff --stat
 tidy_csv/cli.py | 17 +++++++++++++++--
 README.md       |  1 +
 2 files changed, 16 insertions(+), 2 deletions(-)

## git diff -U10 7a2f9e1
````diff
diff --git a/tidy_csv/cli.py b/tidy_csv/cli.py
index 1a2b3c4..5d6e7f8 100644
--- a/tidy_csv/cli.py
+++ b/tidy_csv/cli.py
@@ -20,10 +20,23 @@ def build_parser():
     parser.add_argument("input", help="path to the CSV file to clean")
     parser.add_argument("--dedupe", action="store_true", help="drop duplicate rows")
+    parser.add_argument(
+        "--output",
+        help="write the cleaned rows to this path instead of stdout",
+    )
     return parser
 
 
 def main(argv=None):
     args = build_parser().parse_args(argv)
     cleaned_rows = clean_csv(args.input, dedupe=args.dedupe)
-    for row in cleaned_rows:
-        print(",".join(row))
+    if args.output:
+        write_rows(args.output, cleaned_rows)
+    else:
+        for row in cleaned_rows:
+            print(",".join(row))
+
+
+def write_rows(path, rows):
+    with open(path, "w", newline="") as handle:
+        for row in rows:
+            handle.write(",".join(row) + "\n")
diff --git a/README.md b/README.md
index 9e8d7c6..b1a2c3d 100644
--- a/README.md
+++ b/README.md
@@ -15,6 +15,7 @@
 
 ```bash
 tidy-csv input.csv --dedupe
 ```
 
 - Pass `--dedupe` to drop duplicate rows.
+- Pass `--output PATH` to write the cleaned rows to a file instead of stdout.
````

`write_rows` opens its output with `newline=""`, so the file keeps exactly the newlines this function writes
instead of letting the platform's text-mode translation add or double up `\r`.
