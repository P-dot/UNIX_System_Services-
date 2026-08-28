# z/OS UNIX System Services Engineering Labs

Hands-on laboratory repository for IBM z/OS UNIX System Services (USS) on an ADCD z/OS 1.11 environment.

The goal is to document USS as an integrated part of z/OS rather than as an isolated UNIX shell. Labs connect OMVS, POSIX identity, RACF, HFS/zFS, PARMLIB, IPL configuration, operator commands, shell utilities, and later MVS/USS batch integration.

## Environment

- IBM z/OS ADCD 1.11
- Hercules-based laboratory system
- TSO/ISPF and SDSF
- z/OS UNIX System Services / OMVS
- RACF
- HFS and zFS

## Labs

| Lab | Topic | Status |
|---|---|---|
| [01](labs/01-uss-environment-baseline/) | USS environment baseline: OMVS, RACF identity, filesystems and BPXPRM | Completed |

## Repository principles

- Evidence-first: claims are tied to captured system output.
- Read-only discovery before configuration changes.
- Version-aware: procedures are validated against the ADCD z/OS 1.11 environment.
- Troubleshooting is retained when it teaches something useful.
- No DASD images, z/OS binaries, passwords, secrets, or proprietary IBM software are distributed.
- Network-sensitive host information is excluded from portfolio material.

## Planned progression

1. USS environment baseline.
2. Files, directories and POSIX permissions.
3. RACF and USS security.
4. Shell scripting and utilities.
5. MVS-to-USS data integration.
6. JCL-driven USS execution with BPXBATCH.
7. Operations, processes, logging and troubleshooting.
