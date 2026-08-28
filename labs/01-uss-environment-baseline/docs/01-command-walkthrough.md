# Command walkthrough

This lab follows a read-only discovery sequence. Each command answers one specific question before moving to the next layer.

| Step | Command / action | Purpose | Observed result |
|---:|---|---|---|
| 1 | `OMVS` | Enter the z/OS UNIX shell from TSO/E. | USS shell opened at `/u/ibmuser`. |
| 2 | `id` | Display effective POSIX UID/GID and resolved names. | `uid=0(START2) gid=0(SYS1)`. |
| 3 | `pwd` | Confirm current working directory. | `/u/ibmuser`. |
| 4 | `ls -la` | Inspect home directory including hidden entries and permissions. | Existing files/directories owned mainly by `START2 SYS1` were listed. |
| 5 | `echo $HOME` | Read the HOME environment variable. | `/u/ibmuser`. |
| 6 | `echo $SHELL` | Read the configured shell path. | `/bin/sh`. |
| 7 | `df -k` | Display mounted filesystem usage. | Mixed HFS/zFS layout visible; `/u` maps to the users HFS filesystem. |
| 8 | `mount` | Attempt to list mounts with the expected UNIX utility. | `FSUM7351 not found`. |
| 9 | `echo $PATH` | Check search path used by the shell. | `/bin` is present along with z/OSMF, Print Server and Java paths. |
| 10 | `whence mount` | Ask the shell where `mount` resolves. | No path returned. |
| 11 | follow-up path/file checks | Determine whether the missing `mount` was a simple PATH issue. | No usable `mount` utility was located in the tested context. |
| 12 | `LU IBMUSER OMVS` | Read RACF OMVS attributes for the user. | UID 0, HOME `/u/ibmuser`, PROGRAM `/bin/sh`. |
| 13 | `LG SYS1 OMVS` | Read RACF OMVS attributes for the group. | GID 0. |
| 14 | `D OMVS,O` | Display active z/OS UNIX configuration settings. | OMVS ACTIVE; limits and runtime settings displayed. |
| 15 | `D IPLINFO` | Identify IPL and IEASYS selection. | z/OS 01.11.00; `IEASYS LIST = DB`. |
| 16 | Browse `ADCD.Z111S.PARMLIB(IEASYSDB)` | Identify which BPXPRM suffix is selected. | `OMVS=DB`. |
| 17 | Browse `ADCD.Z111S.PARMLIB(BPXPRMDB)` | Inspect declared z/OS UNIX configuration. | Limits, FILESYSTYPE, MOUNT and NETWORK entries confirmed. |

No RACF changes, filesystem writes, mount operations or PARMLIB edits were performed.
