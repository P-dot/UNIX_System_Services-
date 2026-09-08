# UNIX System Services — Ecosystem Integration

## Purpose

This document defines the architectural role of `P-dot/UNIX_System_Services-` inside the wider z/OS Engineering Laboratory.

The repository is the USS / OMVS / POSIX bridge in the ecosystem.

It owns the practical relationship between:

```text
TSO/ISPF
   |
   v
OMVS shell
   |
   v
POSIX identity
   |
   +--> files / directories / permissions
   |
   +--> processes / jobs / signals
   |
   +--> HFS / zFS
   |
   +--> shell utilities
   |
   v
RACF / SAF
   |
   v
z/OS system configuration
```

The repository does not replace the dedicated RACF, Communications Server, JCL, or core z/OS engineering repositories. Instead, it provides the UNIX-facing operational layer where those components meet.

---

# 1. Repository role

`UNIX_System_Services-` should be understood as the z/OS UNIX engineering layer.

Its responsibility is to demonstrate how z/OS behaves when UNIX semantics are exposed through USS.

The key themes are:

- OMVS shell access;
- POSIX UID / GID identity;
- RACF OMVS segments;
- HFS / zFS filesystems;
- BPXPRM / PARMLIB configuration;
- UNIX files and directories;
- POSIX permission metadata;
- hard and symbolic links;
- shell execution;
- processes and parent/child relationships;
- shell jobs;
- signals and termination;
- exit-status interpretation;
- future shell scripting;
- future MVS-to-USS data integration;
- future JCL-driven USS execution;
- future network-service integration.

This repository is therefore positioned between core system engineering and application/runtime integration.

---

# 2. Current validated lab progression

The repository currently contains three completed practical labs.

```text
Lab 01
USS environment baseline
        |
        v
Lab 02
Filesystem + files + links + permissions
        |
        v
Lab 03
Process and runtime management
```

The progression is coherent:

```text
understand the environment
        |
        v
understand the filesystem model
        |
        v
understand the process model
```

This is the correct foundation before introducing shell scripting, BPXBATCH, TCP/IP-dependent USS services, or non-privileged identity testing.

---

# 3. Lab 01 — USS environment baseline

## Objective

Establish a read-only baseline of the active z/OS UNIX System Services environment and prove how the interactive shell, RACF identity, mounted filesystems, PARMLIB selection and effective OMVS configuration relate to each other.

## Validated areas

The lab demonstrates:

- OMVS interactive shell availability;
- current working directory;
- shell and HOME values;
- effective POSIX UID / GID;
- RACF user OMVS attributes;
- RACF group OMVS attributes;
- mounted HFS and zFS filesystems;
- active OMVS configuration;
- IPL / IEASYS selection;
- BPXPRM selection;
- BPXPRM filesystem and network declarations.

## Reconstructed configuration chain

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
                         +-- FILESYSTYPE
                         +-- MOUNT
                         +-- NETWORK
                               |
                               v
                         active z/OS UNIX
```

This is an important architectural milestone because it proves that USS is not merely a shell running on z/OS.

The shell depends on system-level configuration selected during IPL.

## RACF identity boundary

The lab also establishes the RACF-to-POSIX identity relationship.

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

The observed shell used numeric UID 0 and GID 0.

The lab correctly distinguishes numeric identity from the symbolic name returned by UID resolution.

It does not claim that two different RACF user IDs are equivalent simply because the environment resolves the same numeric UID to a particular name.

---

# 4. Lab 02 — filesystem, links and POSIX permissions

## Objective

Build and validate a controlled USS workspace and demonstrate filesystem operations, POSIX permission metadata, redirection, hard links, symbolic links and executable-file behavior.

## Validated capabilities

The lab validates:

- directory creation;
- file creation;
- `umask`;
- default file and directory modes;
- symbolic chmod;
- octal chmod;
- shell redirection;
- append behavior;
- file copying;
- renaming;
- hard links;
- symbolic links;
- link-count / inode-style correlation;
- broken symlink behavior;
- execute-bit changes;
- execution of a shell command file;
- final RC 0.

## Key POSIX model

The permission logic is correctly documented as:

```text
directory base mode = 777
file base mode      = 666
        |
        v
apply umask
        |
        v
effective creation mode
```

The current lab uses `umask 0022`.

That gives:

```text
new directory -> 755
new regular file -> 644
```

This makes the repository useful as a bridge between traditional z/OS dataset protection concepts and UNIX file permission semantics.

## Hard links vs symbolic links

The lab demonstrates the difference between:

```text
hard link
  |
  +--> another directory entry for same underlying object

symbolic link
  |
  +--> separate object containing pathname reference
```

This is a foundational USS concept and should remain owned by this repository.

## Important authorization limitation

The lab also documents an important constraint: the observed session is privileged.

Therefore the lab proves permission metadata manipulation, but it does not claim that all denial behavior reflects a normal non-privileged user.

That distinction should remain visible in future security work.

---

# 5. Lab 03 — process and runtime management

## Objective

Understand USS process execution and runtime control.

## Validated capabilities

The lab demonstrates:

- `ps`;
- `ps -f`;
- PID;
- PPID;
- shell parent/child relationships;
- background execution using `&`;
- shell job control;
- `jobs`;
- `kill %job`;
- `$!`;
- `wait`;
- `$?`;
- normal exit status;
- signal-based termination;
- final process cleanup.

## Process model

```text
interactive shell
      |
      +--> child command
      |
      +--> background process
                |
                +--> PID
                +--> shell job ID
```

The lab deliberately keeps shell job IDs and UNIX PIDs conceptually separate.

This is operationally important because targeting `%1` from job control can be safer than manually transcribing long PIDs from terminal evidence.

## Exit-status interpretation

The lab observes:

```text
normal wait -> status 0

SIGTERM termination -> status 143
```

The observed 143 result is consistent with:

```text
128 + 15
```

where signal 15 is SIGTERM.

This gives the repository a direct link to later batch/runtime integration, because exit status will eventually become relevant to BPXBATCH and JCL return-code handling.

---

# 6. Current architecture

The current USS architecture can be represented as:

```text
                 z/OS system configuration
                         |
                   IEASYS / BPXPRM
                         |
                         v
                      USS / OMVS
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
      identity       filesystem       processes
          |              |              |
          v              v              v
      RACF OMVS       HFS / zFS      PID / PPID
      UID / GID       chmod/umask      jobs
      HOME/SHELL      links            signals
```

The next architectural layer should be:

```text
USS
 |
 +--> shell scripting
 |
 +--> MVS <-> USS data movement
 |
 +--> JCL / BPXBATCH
 |
 +--> TCP/IP-dependent services
 |
 +--> non-privileged identity testing
```

---

# 7. Relationship with RACF

RACF owns effective authorization and identity administration.

USS consumes RACF-defined UNIX identity attributes.

The boundary is:

```text
RACF
 |
 +--> user OMVS segment
 +--> group OMVS segment
 +--> UNIXPRIV / security controls
        |
        v
USS
 |
 +--> UID/GID
 +--> HOME
 +--> PROGRAM
 +--> runtime identity
```

This repository should not duplicate the deep RACF labs.

Instead, it should document what RACF identity means from the USS runtime perspective.

Examples:

- what UID/GID the shell receives;
- how HOME and shell program affect the session;
- how privileged and non-privileged identities behave differently;
- how file ownership and permissions are observed from USS.

The dedicated RACF repository remains authoritative for:

- effective RACF authority;
- group hierarchy;
- UNIXPRIV;
- STARTED mappings;
- privileged delegation;
- least privilege;
- security remediation.

---

# 8. Relationship with Communications Server

USS and Communications Server intersect at network-facing UNIX services.

The relationship is:

```text
TCP/IP
  |
  v
network listener
  |
  v
USS process
  |
  +--> /etc configuration
  +--> UNIX identity
  +--> process runtime
  +--> filesystem permissions
```

Examples include SSH, HTTP and FTP-related USS process/configuration behavior.

Ownership should remain clear:

```text
Communications Server repo
   -> ports, listeners, TCP/IP profile, AT-TLS, Policy Agent

USS repo
   -> process, shell, /etc files, UNIX ownership, permissions, runtime

RACF repo
   -> identity and authorization
```

This three-way boundary is one of the most important cross-repository relationships in the ecosystem.

---

# 9. Relationship with JCL and BPXBATCH

A future key integration point is JCL-driven USS execution.

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

This will connect:

```text
JCL_LABS
   +
UNIX_System_Services-
```

without duplicating either repository.

JCL owns:

- job structure;
- DD statements;
- step sequencing;
- COND / restart logic;
- JES2 execution context.

USS owns:

- shell command behavior;
- UNIX pathnames;
- POSIX process behavior;
- UNIX return status.

A future integration branch could be:

```text
integration/jcl-bpxbatch-uss
```

---

# 10. Relationship with scheduler

Later, the scheduler can orchestrate JCL that enters USS through BPXBATCH.

Conceptually:

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
USS script/process
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

This is planned architecture.

It is not yet validated in this repository.

---

# 11. Relationship with core z/OS engineering

The central z/OS engineering repository owns system-level configuration topics such as:

- IPL;
- PARMLIB;
- BPXPRM selection;
- HFS/zFS administration;
- system-wide filesystem configuration;
- system-level diagnostics;
- storage infrastructure.

USS consumes those facilities.

The correct boundary is:

```text
central engineering
   -> system config

USS repo
   -> runtime behavior inside that config
```

Lab 01 is the best example because it traces the selected BPXPRM member into the active USS environment without trying to turn this repository into a general PARMLIB repository.

---

# 12. HFS and zFS role

The current environment is mixed HFS/zFS.

The repository should preserve that fact rather than pretending the environment is exclusively one or the other.

Current validated architecture:

```text
USS pathname tree
    |
    +--> HFS-mounted areas
    |
    +--> zFS-mounted areas
```

Future storage work can include:

- creating a controlled zFS;
- mounting it;
- validating ownership and permissions;
- persistence across restart;
- interaction with BPXPRM;
- backup / restore of USS data.

Those system-wide operations should be coordinated with the central engineering repo.

---

# 13. POSIX permission model vs RACF dataset protection

USS permissions and RACF dataset profiles are not the same security mechanism.

This distinction must remain explicit.

```text
MVS dataset
   -> RACF DATASET class

USS file
   -> owner / group / other mode bits
   -> file ownership
   -> RACF UNIX identity
   -> additional z/OS UNIX security controls
```

Future labs should avoid suggesting that `chmod` replaces RACF.

Instead, the repositories should show how both models coexist.

---

# 14. Privileged identity constraint

The current environment has demonstrated privileged UID/GID behavior.

This creates a lab-quality limitation.

Current labs can prove:

- metadata;
- identity mapping;
- ownership;
- mode changes;
- runtime behavior.

They should not overclaim:

- ordinary user denial behavior;
- least-privilege enforcement;
- real separation of user capabilities.

The logical future milestone is to use a controlled non-privileged RACF identity with an OMVS segment.

That work should be coordinated with the RACF repository.

A future integration chain could be:

```text
RACF controlled identity
        |
        v
USS non-privileged shell
        |
        v
POSIX permission validation
```

---

# 15. Shell scripting roadmap

The repository has not yet validated shell scripting as a dedicated lab.

The next progression can include:

```text
variables
conditionals
loops
functions
redirection
pipelines
return status
simple operational automation
```

However, this should build on the process and filesystem knowledge already validated.

A future lab should emphasize z/OS USS `/bin/sh` behavior rather than assume GNU/Linux Bash semantics.

That version-aware approach is consistent with the existing repository methodology.

---

# 16. MVS-to-USS data integration roadmap

A future milestone should demonstrate controlled data movement between MVS datasets and USS files.

Potential areas:

```text
MVS sequential dataset
        |
        v
USS file

USS file
        |
        v
MVS dataset
```

The exact utilities should be validated on the actual ADCD environment before documenting them as available.

The main architectural objective is to prove that USS is not isolated from traditional MVS data processing.

---

# 17. Runtime and process roadmap

Lab 03 provides the first process-management baseline.

Future progression can include:

- longer-running processes;
- stdout/stderr capture;
- pipeline behavior;
- shell traps;
- process cleanup;
- daemon-style behavior;
- USS service diagnostics;
- runtime correlation with SDSF or operator-visible processes where supported.

These should remain controlled and evidence-based.

---

# 18. Validated vs planned capability matrix

| Capability | Status |
|---|---|
| OMVS shell access | Validated |
| POSIX UID/GID observation | Validated |
| RACF OMVS segment correlation | Validated |
| HOME and shell-program observation | Validated |
| HFS / zFS mixed filesystem observation | Validated |
| BPXPRM selection correlation | Validated |
| file / directory operations | Validated |
| chmod / umask | Validated |
| hard links | Validated |
| symbolic links | Validated |
| shell command-file execution | Validated |
| PID / PPID inspection | Validated |
| background jobs | Validated |
| job control | Validated |
| kill / wait | Validated |
| exit-status interpretation | Validated |
| dedicated shell-scripting lab | Planned |
| non-privileged USS authorization test | Planned |
| BPXBATCH integration | Planned |
| MVS-to-USS data integration | Planned |
| scheduler-to-USS integration | Planned |
| service-specific network/USS integration | Planned |
| dedicated zFS lifecycle lab | Planned |

---

# 19. Evidence methodology

Every USS lab should answer:

```text
What command was executed?
What environment was active?
What identity executed it?
What object changed?
What result was returned?
What evidence proves it?
What limitations affect interpretation?
```

Evidence should include, where appropriate:

- terminal output;
- command transcripts;
- `id`;
- `pwd`;
- `ls -l`;
- `ls -li`;
- `df -k`;
- `ps`;
- `jobs`;
- RC / exit status;
- RACF OMVS displays;
- operator output;
- relevant PARMLIB excerpts;
- screenshots;
- troubleshooting notes.

Failed attempts should be preserved when they explain:

- z/OS UNIX command differences;
- missing utilities;
- release-specific behavior;
- shell limitations;
- environment-specific restrictions.

---

# 20. Publication security

USS evidence can expose host, account, filesystem and network information.

Public material should avoid unnecessary exposure of:

- host IP addresses;
- MAC addresses;
- hostnames;
- Windows usernames;
- local host paths;
- terminal/session identifiers;
- credentials;
- secrets;
- tokens;
- private keys;
- sensitive certificate material;
- nonessential user-specific paths outside the controlled lab scope.

System and RACF identifiers should be documented only when they are necessary to explain the lab and are suitable for public publication.

The existing security scan process should continue before commits.

---

# 21. Repository structure

Current high-level repository structure:

```text
.
├── README.md
├── security-scan.txt
└── labs/
    ├── 01-uss-environment-baseline/
    ├── 02-uss-filesystem-posix-permissions/
    └── 03-uss-process-runtime-management/
```

Each lab follows a practical evidence structure with README, completion marker, documentation and evidence assets.

---

# 22. Branch strategy

Recommended normal development pattern:

```text
main
 |
 +-- lab/<number>-<slug>
 |
 +-- docs/<topic>
 |
 +-- integration/<cross-repo-topic>
 |
 +-- fix/<slug>
```

For cross-repository integration:

```text
integration/uss-racf-identity
integration/jcl-bpxbatch-uss
integration/uss-tcpip-service
integration/uss-storage-zfs
integration/scheduler-uss-batch
```

Branches should remain short-lived:

```text
branch
 -> implement
 -> validate
 -> scan
 -> document
 -> PR
 -> merge
 -> delete
```

---

# 23. Recommended next labs

The most coherent next sequence is:

```text
Lab 04
controlled shell scripting
        |
        v
Lab 05
non-privileged RACF/OMVS identity validation
        |
        v
Lab 06
MVS-to-USS data movement
        |
        v
Lab 07
BPXBATCH from JCL
        |
        v
Lab 08
USS runtime/logging/troubleshooting
        |
        v
Lab 09
network-service correlation
```

Exact numbering can be adjusted if the repository already has a separate roadmap.

The important part is the dependency sequence.

---

# 24. Learning path position

Within the wider ecosystem:

```text
MVS_TSO_ISPF
    |
    v
UNIX_System_Services-
    |
    +--> RACF security
    |
    +--> Communications Server
    |
    +--> JCL / BPXBATCH
    |
    +--> automation
```

USS is therefore a bridge technology.

It teaches users how the traditional z/OS environment intersects with POSIX concepts.

---

# 25. Cross-repository integration tracks

## Track A — USS identity

```text
RACF
  |
  v
OMVS segment
  |
  v
USS shell
  |
  v
UID/GID + file permissions
```

## Track B — USS batch execution

```text
JCL
  |
  v
BPXBATCH
  |
  v
USS script
  |
  v
exit status
```

## Track C — USS network service

```text
Communications Server
  |
  v
TCP listener
  |
  v
USS daemon
  |
  v
/etc config + UNIX identity
```

## Track D — USS storage

```text
zFS/HFS
  |
  v
mount
  |
  v
USS pathname tree
  |
  v
files / permissions / processes
```

---

# 26. Integration with automation

The repository should eventually support automation work without becoming a generic scripting repository.

Useful future USS automation topics include:

- shell health checks;
- filesystem checks;
- process checks;
- log collection;
- return-code normalization;
- safe cleanup;
- JCL-driven shell execution.

REXX remains the main TSO/ISPF automation track.

USS shell scripting should focus on UNIX-side operational automation.

---

# 27. Non-goals

This repository should not become:

- a general RACF administration repo;
- a Communications Server configuration repo;
- a generic Linux tutorial;
- a JCL fundamentals repo;
- a zFS systems-programming repo;
- a scheduler repo.

Its role is specifically the UNIX System Services runtime and POSIX layer on z/OS.

---

# 28. Target maturity

Current state:

```text
environment baseline
   ->
filesystem and permission model
   ->
process/runtime model
```

Near-term target:

```text
shell scripting
   ->
non-privileged identity validation
   ->
MVS/USS data integration
   ->
BPXBATCH
```

Long-term target:

```text
USS runtime
   +
RACF identity
   +
TCP/IP services
   +
JCL orchestration
   +
scheduler integration
```

That gives a complete operational view of USS as part of z/OS rather than as an isolated shell.

---

# 29. Master architecture relationship

Master repository:

```text
P-dot/zos-adcd-hercules-engineering-lab
```

USS fits into the wider architecture as:

```text
                    z/OS
                     |
       +-------------+-------------+
       |             |             |
       v             v             v
   RACF/SAF      JES2/JCL       TCP/IP
       |             |             |
       +-------------+-------------+
                     |
                     v
                 USS / OMVS
                     |
          +----------+----------+
          |          |          |
          v          v          v
       files      processes    shell
          |
          v
       HFS/zFS
```

This model intentionally shows USS as integrated with the rest of z/OS.

---

# 30. Repository status summary

The repository currently has three completed, evidence-based USS labs:

```text
01 — environment baseline
02 — filesystem / POSIX permissions
03 — process / runtime management
```

The current documentation establishes a valid foundation for the next stage.

No claim is made yet for:

- complete shell automation;
- non-privileged UNIX security validation;
- BPXBATCH integration;
- MVS/USS data movement;
- scheduler integration;
- production-like network-service operation.

Those remain future milestones.

---

# 31. Architectural role summary

`UNIX_System_Services-` is the **POSIX runtime and MVS-to-UNIX bridge** of the z/OS lab ecosystem.

Its progression is:

```text
system-selected USS environment
        |
        v
RACF-backed POSIX identity
        |
        v
HFS/zFS pathname space
        |
        v
files / permissions / links
        |
        v
processes / jobs / signals
        |
        v
shell automation
        |
        v
MVS / USS integration
        |
        v
JCL / BPXBATCH
        |
        v
network and scheduler integration
```

The repository should continue to distinguish clearly between what has been validated in the current ADCD environment and what is planned for future labs.
