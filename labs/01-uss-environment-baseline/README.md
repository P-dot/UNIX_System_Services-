# Lab 01 - z/OS UNIX System Services Environment Baseline

## Objective

Establish a read-only baseline of the active z/OS UNIX System Services environment and prove how the interactive shell, RACF identity, mounted HFS/zFS filesystems, PARMLIB selection and effective OMVS configuration relate to each other.

This lab intentionally avoids configuration changes. The goal is to discover what the system is actually running before modifying anything.

## What this lab proves

| Area | Evidence | Result |
|---|---|---|
| OMVS access | `OMVS` | Interactive USS shell is available. |
| POSIX identity | `id` | Effective numeric identity is UID 0 and GID 0; displayed names resolve as `START2` and `SYS1`. |
| Working directory | `pwd` | Session starts in `/u/ibmuser`. |
| Home | `echo $HOME` | `HOME=/u/ibmuser`. |
| Shell | `echo $SHELL` | `SHELL=/bin/sh`. |
| Home contents | `ls -la` | Existing USS files and directories are visible under `/u/ibmuser`. |
| Filesystem map | `df -k` | The active USS tree contains both HFS and zFS filesystems. |
| RACF user segment | `LU IBMUSER OMVS` | `IBMUSER` has UID 0, HOME `/u/ibmuser`, PROGRAM `/bin/sh`. |
| RACF group segment | `LG SYS1 OMVS` | `SYS1` has GID 0. |
| Effective OMVS settings | `D OMVS,O` | OMVS is ACTIVE and current UNIX configuration values are displayed. |
| IPL chain | `D IPLINFO` | z/OS 1.11 is active and `IEASYS LIST = DB`. |
| PARMLIB selection | `IEASYSDB` | `OMVS=DB` selects `BPXPRMDB`. |
| USS configuration | `BPXPRMDB` | Limits, FILESYSTYPE entries, HFS/zFS mounts and network domains are declared in PARMLIB. |

## Key architecture reconstructed

```text
IPL
 |
 +-- IEASYS LIST = DB
       |
       +-- ADCD.Z111S.PARMLIB(IEASYSDB)
             |
             +-- OMVS=DB
                   |
                   +-- ADCD.Z111S.PARMLIB(BPXPRMDB)
                         |
                         +-- USS limits
                         +-- FILESYSTYPE definitions
                         +-- HFS/zFS MOUNT statements
                         +-- NETWORK definitions
                               |
                               v
                         active z/OS UNIX
                               |
                               +-- D OMVS,O
                               +-- OMVS shell
                               +-- df -k
```

RACF supplies the UNIX identity attributes used by the session:

```text
IBMUSER OMVS segment
  UID     = 0
  HOME    = /u/ibmuser
  PROGRAM = /bin/sh

SYS1 OMVS segment
  GID     = 0
```

## Important findings

### 1. UID/GID resolution

`id` displayed:

```text
uid=0(START2) gid=0(SYS1)
```

RACF separately proved that `IBMUSER` has `UID=0` and `SYS1` has `GID=0`. Therefore the numeric identity and the name returned by UID resolution must be kept conceptually separate. The lab does not claim that `IBMUSER` is `START2`; it records that UID 0 resolves to `START2` in the observed shell output.

### 2. `/u` is backed by HFS

`BPXPRMDB` contains an HFS mount for the users filesystem at `/u`, and `df -k` shows the corresponding users filesystem. This proves that `/u/ibmuser` is under an HFS-mounted `/u` in this environment.

### 3. Mixed HFS/zFS environment

The active system is not exclusively HFS or zFS. `df -k` and `BPXPRMDB` together show a mixed layout, with zFS used for several product/application paths and HFS used for paths including `/u`, `/etc`, `/tmp`, `/web` and other product areas.

### 4. `mount` troubleshooting retained

Running `mount` returned `FSUM7351 not found`. The session `PATH` contains `/bin`, but `whence mount` returned no path and follow-up checks did not locate a `mount` file in the tested context. The lab retains this as an observed limitation/troubleshooting result instead of fabricating a successful command.

## Evidence

Screenshots are stored in [`evidence/screenshots/`](evidence/screenshots/). The original consolidated DOCX used during the lab is preserved in [`evidence/source/`](evidence/source/).

## Documentation

- [Command walkthrough](docs/01-command-walkthrough.md)
- [Evidence analysis](docs/02-evidence-analysis.md)
- [Troubleshooting](docs/03-troubleshooting.md)
- [Security and operational notes](docs/04-security-and-operational-notes.md)
- [References](docs/05-references.md)

## Result

**Completed.** The lab establishes a version-specific USS baseline for this ADCD z/OS 1.11 system without changing RACF, PARMLIB, mounts, permissions or shell configuration.
