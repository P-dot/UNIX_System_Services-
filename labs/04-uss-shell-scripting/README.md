# Lab 04 - Controlled USS Shell Scripting - Part 1

## Status

**PART 1 COMPLETED**

This repository entry closes Part 1 of Lab 04. Part 2 remains **PENDING** and will continue the same laboratory later.

## Objective

Validate fundamental shell-scripting behavior in the actual z/OS UNIX System Services environment using `/bin/sh`, without assuming Bash or GNU/Linux-specific behavior.

## Environment

- IBM z/OS 1.11 ADCD laboratory
- z/OS UNIX System Services / OMVS
- Shell: `/bin/sh`
- Workspace: `/u/ibmuser/uss-lab04`

## Validated flow

1. Confirmed the active shell and `/bin/sh` executable.
2. Created the dedicated USS workspace.
3. Created a minimal shell script and observed its initial non-executable mode.
4. Added owner execute permission and executed the script successfully (`RC=0`).
5. Validated shell variables and expansion (`RC=0`).
6. Validated positional parameters: `$0`, `$1`, `$2`, and `$#` (`RC=0`).
7. Validated POSIX-style `if`, `test`, `then`, `else`, and `fi`.
8. Demonstrated that a logical result does not automatically imply a non-zero process return code.
9. Added explicit `exit 0` and `exit 8` handling and verified both paths.
10. Validated a POSIX `for ... do ... done` loop over `ONE TWO THREE` (`RC=0`).

## Key results

- `hello.sh` produced `USS-LAB04-HELLO` and returned `0`.
- `variables.sh` produced `USS-LAB04 VARIABLES-OK` and returned `0`.
- `arguments.sh START PROD` reported the script name, both arguments, `ARGCOUNT=2`, and returned `0`.
- `conditional.sh START` produced `ACTION=START`; `conditional.sh STOP` produced `ACTION=UNKNOWN`; both returned `0`.
- `conditional-rc.sh START` produced `ACTION=START` and returned `0`.
- `conditional-rc.sh STOP` produced `ACTION=INVALID` and returned the deliberately selected application RC `8`.
- `loop.sh` produced `ITEM=ONE`, `ITEM=TWO`, and `ITEM=THREE`, then returned `0`.

## Important interpretation

`RC=8` is a laboratory design choice used to demonstrate explicit process-status control. It is not documented here as a universal z/OS meaning.

The test was performed in the existing privileged laboratory identity context. Therefore this part validates shell behavior, not ordinary-user permission-denial behavior.

## Pending - Part 2

Part 2 is intentionally deferred. Planned topics are:

- shell functions
- stdout/stderr and redirection
- pipelines
- combined operational script
- controlled error handling and cleanup
- final end-to-end validation

Future BPXBATCH/JCL integration is a roadmap item only; it has **not** been validated by Part 1.

## Evidence

The original cumulative DOCX is preserved unchanged under `evidence/source/`. Embedded evidence images are extracted under `evidence/screenshots/`.

## Result

**Lab 04 Part 1: COMPLETED**

**Lab 04 Part 2: PENDING**
