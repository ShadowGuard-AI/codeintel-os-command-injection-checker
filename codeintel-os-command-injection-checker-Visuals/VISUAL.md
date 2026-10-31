# OS Command Injection Checker, drawn out

I made these five pictures because the README has to say two things
at once, that the built in check is a plain substring search and that
four outside tools do the heavier reading, and the split is easier
to see drawn. Everything in them comes from main.py and the README
beside it.

## The visuals

- [architecture.svg](architecture.svg): the system shape. The
  substring check at the centre, the four tool runners under it, and
  the combine step that writes the one results file.
- [components.svg](components.svg): the seven functions in main.py
  as cards, wired by who calls whom, with the combine block drawn
  where it actually lives, inside main().
- [flowchart.svg](flowchart.svg): one run down a single spine: file
  check, substring check, verdict, stale output cleanup, one
  decision per tool, then the combine and its exit code.
- [product-map.svg](product-map.svg): the product in six cells: what
  it is, who it serves, the built in check, the outside tools, what
  comes out, and where it stops.
- [statistics.svg](statistics.svg): the numbers from the table
  below, drawn as bars on paper.

Each sheet also ships as a PNG beside its SVG (architecture.png,
flowchart.png, and so on): the same drawing, finished as a photograph
of the paper page.

Every sheet here is drawn to look like what it is: a page sketched by
hand on paper, and each one is signed GK somewhere in the drawing,
never twice in the same place, because that is what I do with a page
I made.

## Written product map

What it is: a checker that reads one Python file, looks for a
command call and a user input marker living in the same file, prints
a verdict, then optionally runs Bandit, Flake8, Pylint, and Pyre on
the same file and combines what they produced into one text file.

Who it serves: me, looking at a script I did not write, deciding
whether it deserves a closer read before I run it, and anyone doing
the same first pass.

Capabilities, grouped:

- The built in check, is_command_injection_vulnerable: a plain
  substring search over the whole file text. A command half
  (os.system(, subprocess.call(, subprocess.Popen(, subprocess.run()
  and an input half (input(, sys.argv[, os.environ[) must both
  appear. Both present logs a warning and returns True.
- The tool runners: run_bandit (Bandit with txt output to
  bandit_output.txt; its exit 1 means findings, not failure),
  run_flake8 and run_pylint (each captures the tool output and
  writes its own file), and run_pyre (pyre check in the target
  file folder, logged only).
- The combine step in main(): deletes stale tool outputs before the
  run so an old report cannot leak into new results, then writes
  the verdict first and the Bandit, Flake8, and Pylint sections
  after it, to output.txt or the -o path. If that file cannot be
  written, the run exits 1.

Inputs: one Python file path, an optional output path, and four
optional disable flags. Outputs: the printed verdict, the combined
results file, and the per tool files left in the current directory.

Boundaries, what it does NOT do: no data flow analysis, so it can
miss a real vulnerability and can flag code where the input never
reaches the command; the README says this in its second paragraph
and I keep it here too. It checks one file at a time, never a
project tree. It writes no fixes. Calls it does not know, such as
os.popen( or eval(, are outside the built in check, though Bandit
may catch some of them. Pyre contributes nothing to the combined
file, and a tool that is not installed logs an error and the run
carries on.

## Statistics, with citations

Counted 2026-10-30, from the clone this folder ships in, at the
commit before this visuals set (13c9767).

| Number | Value | How it was counted, and from what |
|---|---|---|
| Files in the repo | 6 | git ls-files in the clone, counted 2026-10-30 |
| Python lines | 210 | Line count over the 1 tracked .py file (main.py), by wc -l, 2026-10-30 |
| Markdown lines | 57 | Line count over the 1 tracked .md file (README.md), same method, 2026-10-30 |
| LICENSE lines | 21 | Line count over the tracked LICENSE file (no extension), same method, 2026-10-30 |
| Text lines | 5 | Line count over the 1 tracked .txt file (requirements.txt, all comments), same method, 2026-10-30 |
| Commits | 8 | git log in the clone, counted 2026-10-30 |
| First commit | 2025-06-01 | git log, oldest entry, 2026-10-30 |
| Last commit before this one | 2026-10-30 | git log, newest entry, 2026-10-30 |
| Tests | 0 test files; no suite exists | No test file exists in the clone (git ls-files shows none), counted 2026-10-30. As a working check I ran the tool itself on 2026-10-30 against a sample file in /tmp that passes input() to os.system, with all four tools disabled: it printed "Potential OS command injection vulnerability detected in bad_sample.py" and wrote that verdict into the combined file, matching the README example. That is a hand run of the tool, not a test suite, and no suite pass is claimed. |
| Languages (API) | Python 8,569 bytes | GitHub API, GET /repos/ShadowGuard-AI/codeintel-os-command-injection-checker/languages, read 2026-10-30 |
| Repo size (API) | 64 KB | GitHub API, GET /repos/ShadowGuard-AI/codeintel-os-command-injection-checker, size field, read 2026-10-30 |
| Stars / forks (API) | 1 / 0 | Same API response, read 2026-10-30 |

External statistics: none. No outside number says anything honest
about this repo, so the repo's own numbers are the whole table.

## Roadmap summary

Done: the substring check, the four runners with their output
capture fixed, stale report cleanup, and an exit code that tells
the truth when the results file is lost. Working now: nothing in
flight. Next, if I return to it: more command spellings in the
built in check, starting with the ones the README already names as
missed. The full roadmap, with the Later and Not planned sections,
is in [ROADMAP.md](ROADMAP.md).
