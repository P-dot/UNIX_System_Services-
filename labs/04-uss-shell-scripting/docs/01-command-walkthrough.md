# Command Walkthrough

Part 1 progresses from environment verification to controlled shell logic.

- `echo $SHELL` identifies the shell selected for the session.
- `ls -l /bin/sh` confirms the shell program exists and is executable.
- `mkdir -p`, `cd`, and `pwd` establish the dedicated workspace.
- `echo ... > file` and `>> file` create the scripts while preserving literal shell variables by quoting the interactive creation command.
- `chmod u+x` adds owner execute permission.
- `./script` executes from the current directory; `echo $?` captures the immediately preceding process status.
- Variables are assigned with `NAME=value` and expanded with `$NAME`.
- `$0`, `$1`, `$2`, and `$#` expose script name, positional arguments, and argument count.
- `if test ...; then ... else ... fi` validates POSIX-style conditional control.
- `exit 0` and `exit 8` explicitly communicate application status to the caller.
- `for ITEM in ...; do ...; done` validates controlled iteration.
