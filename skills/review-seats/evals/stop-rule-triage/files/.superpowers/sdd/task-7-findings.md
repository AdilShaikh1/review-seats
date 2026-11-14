# Task 7 findings, round 2 (branch feature/output-flag)

1. **Security, B1 (leak).** `README.md:42` holds term 3 of `INTERNAL/private_terms.txt`.
2. **Overseer, B2 (regression).** The `--output` flag in `tidy_csv/cli.py` now overwrites the input file when
   the output path matches the input path. This destroys the user's data.
3. **Quality, N1 (nit).** `tidy_csv/cli.py` names a loop variable `x` instead of something that says what it
   holds.
4. **Overseer, B3 (should-fix, a contradiction).** `docs/usage.md` tells users to pass `--output` for large
   files. `README.md` tells them to pipe stdout instead for the same case. The two docs give opposing advice,
   and neither one is wrong about what the code does.
5. **Security, B4 (security vulnerability).** `tidy_csv/cli.py` passes the `--output` value straight to
   `open()` with no path check, so a value such as `../../etc/passwd` writes outside the project folder.
6. **Quality, N2 (nit).** `tests/test_cli.py` has the comment `# call the cleaner` directly above the line that
   calls `clean_csv()`. The comment only restates the call.
