# 04 — Security and Operational Notes

- The lab performs process observation and controlled termination only on processes created specifically for the exercise.
- No system service, daemon, started task, or unrelated process is intentionally targeted.
- Using `%1` and `$!` reduces the operational risk associated with manually entering a PID.
- `kill -9` was intentionally not used. The exercise begins with the normal/default termination signal rather than forced termination.
- The final `jobs` and `ps -f` check is an operational cleanup control confirming that no test `sleep` remains.
- This lab does not make authorization conclusions from the effective identity. RACF/security authorization work belongs in the dedicated security track.
- Evidence was checked for common private IPv4 and MAC-address patterns before packaging.
