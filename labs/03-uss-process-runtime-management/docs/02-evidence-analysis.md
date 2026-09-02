# 02 — Evidence Analysis

The screenshots provide a sequential record of the runtime experiment.

1. Basic `ps` output identifies `/bin/sh` and the transient `ps` process.
2. `ps -f` adds PID/PPID information and demonstrates the process hierarchy.
3. A background `sleep 60` is visible alongside the shell and `ps -f`.
4. After completion, `jobs` reports the background job as done.
5. The shell returns status 0 for the immediately preceding `jobs` command.
6–7. A longer `sleep 300` is started and confirmed as running before termination.
8. `kill %1` terminates the shell job; the evidence records SIGTERM-related shell/runtime messages and subsequent process inspection.
9. `$!` is shown returning the same PID assigned to the newly started background `sleep`.
10. `wait $!` followed immediately by `echo $?` records normal completion status 0.
11. Another `sleep 300` is confirmed running before the signal test.
12. `kill $!`, `wait $!`, and `echo $?` produce status 143, consistent in the observed environment with termination by signal 15 (SIGTERM).
13. Final `jobs` and `ps -f` inspection shows no laboratory `sleep` process remaining.

The evidence supports the practical distinction between a process, its PID, the shell's job abstraction, and the termination status later collected by `wait`.
