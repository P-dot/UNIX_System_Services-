# 01 — Command Walkthrough

## `ps`
Lists processes visible in the current terminal/session context. It established the interactive shell and the short-lived `ps` child process.

## `ps -f`
Requests a fuller process listing. The observed output included UID, PID, PPID, start time, terminal, CPU time and command. The PPID of the invoked `ps -f` matched the PID of `/bin/sh`, demonstrating the parent/child relationship.

## `sleep N &`
`sleep` creates a simple process that waits for the requested interval. Appending `&` asks the shell to run it in the background, so the prompt returns without waiting for completion.

## `jobs`
Displays jobs tracked by the current shell. This demonstrates that a shell job identifier such as `[1]` is not the same concept as a USS PID.

## `kill %1`
Targets shell job 1. This was deliberately preferred after a PID was initially misread from a screenshot, avoiding manual transcription of a long process identifier.

## `$!`
The shell parameter `$!` was experimentally verified to contain the PID of the most recently started background process.

## `wait $!`
Waits for that background process and returns its termination status to the shell.

## `echo $?`
Displays the status of the immediately preceding command. It was therefore executed directly after `wait` when the lab needed to preserve the process completion result.

## Final verification
`jobs` and `ps -f` were executed at the end to verify that no test `sleep` process remained active.
