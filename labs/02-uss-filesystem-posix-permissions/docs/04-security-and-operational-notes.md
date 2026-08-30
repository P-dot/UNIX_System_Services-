# 04 - Security and Operational Notes

- All filesystem modifications are confined to `/u/ibmuser/uss-lab02`.
- No PARMLIB, RACF, mount, system directory or network configuration was changed.
- The evidence contains no private IPv4 address or MAC-address requirement for this lab; publication packaging should still run the repository security grep before commit.
- The effective shell identity observed in the preceding baseline is privileged (UID 0/GID 0). Permission-mode demonstrations are valid, but ordinary-user access-denial conclusions must be tested later with a non-privileged USS identity.
- The lab restores `logs` to mode `755`, removes the temporary diagnostic `test.txt`, removes the intentionally broken symlink and leaves a clean final workspace.
- `runlab` is intentionally minimal. Shell scripting, pipelines and automation belong to the dedicated scripting lab rather than being expanded here.
