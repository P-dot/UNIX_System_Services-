# 03 — Troubleshooting

## PID transcription error

During the lab, a long PID was initially read incorrectly from screenshot evidence. No process should be terminated from a PID that has not been verified exactly.

### Corrective action

The procedure was changed to use shell-native references:

- `%1` to address the shell-managed background job.
- `$!` to obtain the PID of the most recently started background process.

This removed the need to manually reconstruct long numeric PIDs and made subsequent commands safer and reproducible.

## Version-aware execution

The environment is an older z/OS USS implementation. The lab therefore tested capabilities incrementally (`ps -f`, `$!`, `jobs`, `wait`) instead of assuming GNU/Linux command-line options or behavior.

## Runtime messages during termination

The termination evidence contains the actual shell/runtime response to SIGTERM. These messages were retained as evidence rather than normalized or hidden because they document the observed platform behavior.
