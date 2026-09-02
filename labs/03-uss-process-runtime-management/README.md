# Lab 03 — USS Process and Runtime Management

## Objective

Build an evidence-based understanding of process execution and runtime control in z/OS UNIX System Services (USS), focusing on process inspection, parent/child relationships, background jobs, shell job control, process identifiers, controlled termination, waiting, and exit status interpretation.

## Environment

- z/OS UNIX System Services (USS)
- OMVS interactive shell (`/bin/sh`)
- ADCD/Hercules laboratory environment
- Commands and behavior documented from the actual system rather than assumed from a modern GNU/Linux environment

## Scope

This lab deliberately remains focused on USS process/runtime behavior. RACF authorization analysis is not duplicated here, and JCL/BPXBATCH integration is reserved for a later lab.

## Workflow

1. Inspect the current shell and transient commands with `ps`.
2. Request fuller process information with `ps -f` and correlate PID/PPID relationships.
3. Start `sleep` in the background with `&` and verify that the shell remains interactive.
4. Inspect shell-managed jobs with `jobs`.
5. Terminate a background job with `kill %1` rather than manually transcribing a long PID.
6. Verify that `$!` exposes the PID of the most recently started background process.
7. Use `wait $!` and `$?` to capture normal completion status.
8. Compare normal completion with termination by the default `kill` signal.
9. Confirm the final shell state contains no remaining test `sleep` process.

## Key Results

- `ps -f` exposed the relationship between the interactive `/bin/sh` and child commands through PID/PPID fields.
- `sleep 60 &` and `sleep 300 &` demonstrated asynchronous background execution.
- `jobs` distinguished shell job identifiers from USS process identifiers.
- `kill %1` provided a safer job-control mechanism than manually retyping long PIDs from terminal evidence.
- `$!` returned the PID of the latest background process in this shell.
- A normally completed background `sleep`, collected with `wait`, produced status `0`.
- A process terminated by the default `kill` signal produced status `143` after `wait`; in the observed shell behavior this is consistent with `128 + 15`, where signal 15 is SIGTERM.
- Final `jobs`/`ps -f` verification showed no remaining laboratory `sleep` process.

## Evidence

The `evidence/screenshots/` directory contains 13 sequential screenshots covering the complete exercise. The cumulative original DOCX supplied during the lab is preserved unchanged under `evidence/source/`.

## Operational Lesson

Long numeric PIDs should not be reconstructed from unclear screenshots. Where shell job control is available, `%job` and `$!` reduce transcription risk and make process targeting reproducible. The lab therefore preserves the initial PID-reading issue as a troubleshooting lesson rather than hiding it.

## Status

**COMPLETED — practical objectives validated with terminal evidence.**
