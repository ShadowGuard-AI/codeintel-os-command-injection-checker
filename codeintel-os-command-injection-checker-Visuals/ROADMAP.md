# OS Command Injection Checker roadmap

Written 2026-10-30 from the repo as it stands. No dates on the future
items, because I do not know when I will be back in this code, and a
date I invent here would be a small lie in a public file.

## Done

- The built in check: substring search for four command call
  spellings and three input markers, a printed verdict either way,
  and exit 1 when the target file does not exist.
- The four runners: Bandit, Flake8, Pylint, and Pyre, each with a
  disable flag, each logging what it did.
- The output capture fixes: Flake8 and Pylint output is captured and
  written by the program itself (an earlier version passed a shell
  style redirection through as filenames and wrote nothing), Bandit
  exit 1 is read as findings, stale tool outputs are deleted before
  each run, and a failed combined write exits 1 instead of looking
  like success.
- A README that states the limits of the substring check up front,
  and these visuals.

## Working now

- Nothing in flight. The repo is in a finished, working state as of
  the 2026-10-30 campaigns.

## Next

- More spellings in the built in check. The README already names
  what it misses: os.popen(, subprocess.getoutput(, eval(. Adding
  them is a small change to two lists, and the README wording is
  the record that they are missing today.
- Nothing else is queued. The code has no TODO or FIXME markers in
  it (checked 2026-10-30), so this section stays short on purpose.

## Later

- A note in the combined file when a tool was skipped or missing,
  so the results file explains its own gaps instead of the reader
  having to check the log. Worth doing when I next touch the
  combine step, not before.

## Not planned

- Data flow analysis. Tracking whether input actually reaches the
  command is a different class of tool; this one stays a first
  pass that says so in its own README.
- Scanning whole projects in one run. One file in, one file out is
  the shape I use; Bandit already does tree scans for anyone who
  wants them.
