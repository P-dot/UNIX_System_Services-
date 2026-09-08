# z/OS UNIX System Services Engineering Labs

Hands-on engineering labs for **z/OS UNIX System Services (USS)** on an ADCD / Hercules environment.

This repository documents USS as an integrated part of z/OS rather than as an isolated UNIX shell. The labs focus on the relationship between OMVS, POSIX identity, RACF, HFS/zFS, PARMLIB-selected USS configuration, filesystem behavior, process management and future MVS-to-USS integration.

---

## Repository role

`UNIX_System_Services-` is the **USS / OMVS / POSIX runtime layer** of the wider z/OS Engineering Laboratory.

Its role is to show how UNIX semantics are exposed inside z/OS and how they connect with:

- RACF identities and OMVS segments;
- HFS and zFS filesystems;
- PARMLIB-selected USS configuration;
- UNIX files, directories and permissions;
- shell execution;
- processes, jobs and signals;
- future shell scripting;
- future MVS-to-USS data movement;
- future JCL / BPXBATCH integration;
- future network-service correlation.

This repository does not replace the dedicated RACF, Communications Server, JCL, scheduler or core z/OS engineering repositories. It focuses on the USS runtime boundary where those areas meet.

---

## Environment

Current lab environment:

- z/OS 1.11 ADCD
- Hercules
- TSO/E
- ISPF
- SDSF
- UNIX System Services / OMVS
- RACF
- HFS / zFS
- shell utilities available in the tested environment

The repository documents the behavior actually observed in this environment. It does not assume that modern GNU/Linux behavior or newer z/OS facilities are automatically available.

---

## Current lab progression

| Lab | Topic | Status |
|---|---|---|
| 01 | USS Environment Baseline | Completed |
| 02 | USS Filesystem and POSIX Permissions | Completed |
| 03 | USS Process and Runtime Management | Completed |

The current sequence is intentional:

```text
understand the environment
        |
        v
understand the filesystem model
        |
        v
understand the process model
```

These three labs establish the baseline required before introducing shell scripting, BPXBATCH, non-privileged identity testing or network-facing USS services.

---

# Lab 01 — USS Environment Baseline

## Objective

Establish a read-only baseline of the active USS environment and prove the relationship between:

- OMVS shell access;
- current USS identity;
- RACF OMVS attributes;
- mounted filesystems;
- active OMVS configuration;
- PARMLIB selection;
- BPXPRM configuration.

## What was validated

The lab confirms:

- OMVS shell availability;
- current working directory;
- HOME and shell values;
- effective numeric UID and GID;
- RACF OMVS segment attributes;
- mixed HFS / zFS filesystem usage;
- active OMVS state;
- IPL / IEASYS selection;
- BPXPRM selection;
- effective USS configuration.

## Configuration chain

```text
IPL
 |
 +-- IEASYS LIST = DB
       |
       +-- IEASYSDB
             |
             +-- OMVS=DB
                   |
                   +-- BPXPRMDB
                         |
                         +-- USS limits
                         +-- FILESYSTYPE definitions
                         +-- MOUNT statements
                         +-- NETWORK definitions
                               |
                               v
                         active z/OS UNIX
```

This demonstrates that USS is part of the z/OS system configuration selected during IPL.

## Identity observation

The lab correlates the shell session with RACF OMVS attributes.

Conceptually:

```text
RACF user
   |
   +--> OMVS segment
            |
            +--> UID
            +--> HOME
            +--> PROGRAM

RACF group
   |
   +--> OMVS segment
            |
            +--> GID
```

The observed session used privileged numeric UID/GID values.

The lab keeps numeric identity and symbolic name resolution conceptually separate and does not treat them as interchangeable identities.

## Important limitation

This lab is primarily observational and read-only.

It does not modify:

- RACF profiles;
- PARMLIB;
- filesystem mounts;
- system-wide OMVS configuration;
- UNIX permission policy.

---

# Lab 02 — USS Filesystem and POSIX Permissions

## Objective

Build a controlled USS workspace and demonstrate practical POSIX filesystem behavior.

The lab covers:

- directories;
- regular files;
- shell redirection;
- `umask`;
- permission bits;
- `chmod`;
- copy and rename;
- hard links;
- symbolic links;
- executable-file behavior.

## What was validated

The lab validates:

- directory creation;
- regular-file creation;
- default creation modes;
- symbolic chmod;
- octal chmod;
- output redirection;
- append behavior;
- copy and rename;
- hard links;
- symbolic links;
- link-count / object correlation;
- broken symlink behavior;
- execute-bit changes;
- successful shell-command execution;
- final return code 0.

## Permission model

With the tested `umask 0022`:

```text
directory base mode = 777
file base mode      = 666
        |
        v
apply umask
        |
        v
directory -> 755
file      -> 644
```

This introduces the POSIX permission model used by USS.

## Hard links vs symbolic links

```text
hard link
  |
  +--> another directory entry for the same underlying object

symbolic link
  |
  +--> separate object containing a pathname reference
```

The lab demonstrates both models directly.

## Security limitation

The shell environment observed in Lab 01 is privileged.

Therefore Lab 02 proves:

- permission metadata;
- ownership-related observations;
- mode changes;
- link semantics;
- executable-file behavior.

It does **not** claim to validate ordinary non-privileged denial behavior.

That should be tested later with a controlled non-privileged RACF identity.

---

# Lab 03 — USS Process and Runtime Management

## Objective

Demonstrate process execution and runtime control inside USS.

The lab focuses on:

- process listing;
- PID and PPID;
- background execution;
- shell job control;
- signal-based termination;
- wait behavior;
- exit status interpretation.

## What was validated

The lab demonstrates:

- `ps`;
- `ps -f`;
- PID;
- PPID;
- foreground and background execution;
- `jobs`;
- shell job identifiers;
- `$!`;
- `kill`;
- `wait`;
- `$?`;
- normal completion;
- signal termination;
- process cleanup.

## Process model

```text
interactive shell
      |
      +--> foreground command
      |
      +--> background process
                |
                +--> PID
                +--> shell job ID
```

The lab keeps shell job IDs and UNIX process IDs separate.

This matters operationally because shell job control and process-level signaling are related but distinct mechanisms.

## Exit status

Observed behavior:

```text
normal wait -> status 0

SIGTERM termination -> status 143
```

The observed status 143 is consistent with:

```text
128 + 15
```

where signal 15 is SIGTERM.

This becomes important later when USS execution is driven from JCL and return status must be interpreted outside the shell.

---

## Current validated capabilities

| Capability | Status |
|---|---|
| OMVS shell access | Validated |
| POSIX UID / GID observation | Validated |
| RACF OMVS segment correlation | Validated |
| HOME / shell-program observation | Validated |
| HFS / zFS observation | Validated |
| BPXPRM selection correlation | Validated |
| file / directory operations | Validated |
| `umask` / `chmod` | Validated |
| hard links | Validated |
| symbolic links | Validated |
| executable command file | Validated |
| PID / PPID inspection | Validated |
| background process execution | Validated |
| shell job control | Validated |
| signal termination | Validated |
| `wait` / exit-status interpretation | Validated |

---

## Current boundaries

The repository does **not** currently claim full validation of:

- dedicated shell scripting;
- non-privileged POSIX authorization behavior;
- JCL / BPXBATCH integration;
- MVS-to-USS dataset/file movement;
- scheduler-driven USS execution;
- production-like USS network services;
- end-to-end AT-TLS-protected USS services;
- full zFS lifecycle administration;
- complete USS logging / SMF correlation.

These are future integration areas.

---

## Relationship with RACF

RACF remains the authority for identity and authorization.

USS consumes RACF-defined UNIX identity attributes.

```text
RACF
 |
 +--> user OMVS segment
 +--> group OMVS segment
        |
        v
USS
 |
 +--> UID
 +--> GID
 +--> HOME
 +--> PROGRAM
 +--> runtime identity
```

The dedicated RACF repository owns the deeper security topics:

- effective authority;
- UNIXPRIV;
- STARTED mappings;
- least privilege;
- privileged delegation;
- RACF policy changes.

This repository focuses on how those identities appear and behave from the USS runtime side.

---

## Relationship with Communications Server

USS and Communications Server meet when UNIX-hosted processes or configuration participate in network services.

Conceptually:

```text
TCP/IP
  |
  v
listener / network service
  |
  v
USS process
  |
  +--> /etc configuration
  +--> UNIX identity
  +--> filesystem permissions
  +--> runtime process state
```

Ownership remains separated:

```text
Communications Server
 -> TCP/IP profile, listeners, ports, Policy Agent, AT-TLS

USS
 -> process runtime, shell, /etc files, permissions, ownership

RACF
 -> identity and authorization
```

---

## Relationship with JCL and BPXBATCH

A major future integration milestone is JCL-driven USS execution.

Target architecture:

```text
JCL
 |
 +--> EXEC PGM=BPXBATCH
          |
          v
        USS
          |
          +--> shell command
          +--> script
          +--> file operation
          +--> process
          |
          v
     exit status / output
          |
          v
       JCL step RC
```

This will connect `JCL_LABS` and `UNIX_System_Services-` without duplicating their roles.

JCL remains responsible for:

- job structure;
- step sequencing;
- DD statements;
- JES2 execution context;
- restart / condition logic.

USS remains responsible for:

- shell behavior;
- UNIX pathnames;
- POSIX processes;
- UNIX exit status.

---

## Relationship with the scheduler

Later integration can extend the execution chain:

```text
Scheduler
   |
   v
JCL / JES2
   |
   v
BPXBATCH
   |
   v
USS script / process
   |
   v
exit status
   |
   v
JCL RC
   |
   v
Scheduler decision
```

This architecture is planned.

It is not yet validated by the current USS labs.

---

## Relationship with core z/OS engineering

The central engineering repository owns the system-level configuration that USS depends on.

Examples:

- IPL;
- IEASYS;
- PARMLIB;
- BPXPRM;
- system-wide HFS/zFS configuration;
- storage;
- diagnostics;
- restart and recovery.

The USS repository consumes those facilities and demonstrates runtime behavior inside the selected environment.

```text
core z/OS engineering
       |
       v
system-selected USS configuration
       |
       v
UNIX_System_Services-
       |
       v
runtime observation and practical POSIX behavior
```

---

## HFS and zFS

The tested environment contains a mixed HFS / zFS pathname space.

The repository preserves that fact rather than assuming one filesystem technology.

```text
USS pathname tree
    |
    +--> HFS-backed areas
    |
    +--> zFS-backed areas
```

Future system-level zFS creation, mount persistence, backup or recovery work should be coordinated with the central engineering repository.

---

## POSIX permissions vs RACF dataset protection

USS permissions and RACF dataset profiles are different mechanisms.

```text
MVS dataset
   -> RACF DATASET class

USS file
   -> owner
   -> group
   -> mode bits
   -> RACF-backed UNIX identity
   -> additional z/OS UNIX security controls
```

`chmod` does not replace RACF.

Future labs should continue to document how the two security models coexist.

---

## Planned progression

The next practical progression should move from the current runtime baseline toward controlled integration.

Suggested order:

```text
Lab 04
shell scripting
        |
        v
Lab 05
non-privileged RACF / OMVS identity validation
        |
        v
Lab 06
MVS-to-USS data movement
        |
        v
Lab 07
JCL / BPXBATCH
        |
        v
Lab 08
runtime / logging / troubleshooting
        |
        v
Lab 09
network-service correlation
```

Exact numbering can be adjusted as the repository evolves.

The important point is the dependency sequence.

---

## Evidence methodology

Each lab should answer:

```text
What command was executed?
What environment was active?
What identity executed it?
What object changed?
What result was returned?
What evidence proves it?
What limitation affects interpretation?
```

Useful evidence includes:

- command output;
- terminal captures;
- `id`;
- `pwd`;
- `ls -l`;
- `ls -li`;
- `df -k`;
- `ps`;
- `jobs`;
- return codes;
- RACF OMVS information;
- operator displays;
- relevant PARMLIB excerpts;
- troubleshooting notes;
- screenshots where they add value.

Failed attempts should be preserved when they explain release-specific or environment-specific behavior.

---

## Publication security

Before publication, review evidence for unnecessary exposure of:

- host IP addresses;
- MAC addresses;
- hostnames;
- Windows user information;
- local host filesystem paths;
- terminal/session identifiers;
- credentials;
- secrets;
- tokens;
- private keys;
- sensitive certificate material;
- host-side network details.

Only include system identifiers when they are relevant to the lab and suitable for public documentation.

---

## Repository structure

Current high-level structure:

```text
.
├── README.md
├── security-scan.txt
├── docs/
│   └── ECOSYSTEM-INTEGRATION.md
└── labs/
    ├── 01-uss-environment-baseline/
    ├── 02-uss-filesystem-posix-permissions/
    └── 03-uss-process-runtime-management/
```

---

## Ecosystem integration

For the detailed architectural role of this repository, see:

[`docs/ECOSYSTEM-INTEGRATION.md`](docs/ECOSYSTEM-INTEGRATION.md)

That document defines:

- repository ownership boundaries;
- validated vs planned capabilities;
- RACF integration;
- Communications Server integration;
- JCL / BPXBATCH integration;
- scheduler integration;
- storage and zFS relationships;
- cross-repository branches;
- long-term maturity path.

---

## Wider z/OS lab ecosystem

This repository is part of the broader engineering environment centered around:

[`P-dot/zos-adcd-hercules-engineering-lab`](https://github.com/P-dot/zos-adcd-hercules-engineering-lab)

Relevant companion repositories include:

- `P-dot/mainframe-racf-security-evidence`
- `P-dot/zos-communications-server-network-lab`
- `P-dot/JCL_LABS`
- `P-dot/zos-batch-scheduler`
- `P-dot/MVS_TSO_ISPF`
- `P-dot/Rexx`
- `P-dot/COBOL`
- `P-dot/vsam01`
- `P-dot/DB2-`
- `P-dot/CICS`
- `P-dot/PL-I`
- `P-dot/z_Assembly`

Within that architecture, this repository provides the practical USS / OMVS / POSIX runtime layer.

---

## Target architecture

```text
z/OS
 |
 +--> RACF / SAF
 |
 +--> JES2 / JCL
 |
 +--> TCP/IP
 |
 +--> storage / HFS / zFS
 |
 +--> USS / OMVS
        |
        +--> POSIX identity
        +--> files / directories
        +--> permissions
        +--> processes
        +--> shell
        +--> scripts
        +--> BPXBATCH
        +--> network-facing runtime
```

Long-term integration path:

```text
RACF identity
    |
    v
USS runtime
    |
    +--> filesystem
    +--> process model
    +--> scripting
    |
    v
JCL / BPXBATCH
    |
    v
scheduler / operational integration
```

---

## Status

Current completed foundation:

```text
Lab 01
environment baseline

Lab 02
filesystem and POSIX permissions

Lab 03
process and runtime management
```

This repository now has a validated USS baseline across environment, filesystem and process/runtime behavior.

The next step is to build controlled automation and MVS-to-USS integration on top of that foundation.
