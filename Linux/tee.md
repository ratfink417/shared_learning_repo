# Introduction
`tee` is a great little tool to read from standard input and write to standard output and a file at the same time.

Command action 2>&1 | `tee` / Filename

`2>&1` means "send any error messages (aka 'stderr') to the same output as any informational messages (aka 'stdout")."