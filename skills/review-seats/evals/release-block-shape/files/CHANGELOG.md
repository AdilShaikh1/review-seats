# Changelog

## [0.3.0] - 2026-11-24

This release adds an output flag and makes the cleaner safer on large files. The cleaner now streams each file
instead of loading it whole, so memory use stays flat on files of any size.

- The new --output flag writes the cleaned rows to a path you choose. The path must sit inside the current
  working directory, and the CLI stops with a clear message when it does not.
- The duplicate-row check now compares rows after whitespace is stripped, so two rows that differ only by
  trailing spaces count as one.
- The usage guide shows the --output flag with one worked example.

## [0.2.0] - 2026-10-30

- The CLI normalises CRLF and LF line endings to LF.
- The CLI drops duplicate rows.

## [0.1.0] - 2026-10-12

- The first release strips extra whitespace from every cell.
