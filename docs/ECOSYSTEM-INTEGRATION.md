# UNIX System Services — Ecosystem Integration

This document defines the architectural role, ownership boundaries and cross-repository integration model of `P-dot/UNIX_System_Services-`.

[Portfolio](https://github.com/P-dot/P-dot) · [Repository README](../README.md) · [Architecture V2](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/architecture/v2) · [Engineering Control](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/engineering-control)

---

## 1. Domain role

USS is the UNIX-facing runtime layer inside z/OS:

```text
z/OS system configuration
        |
        v
USS / OMVS
        |
        +--> RACF-backed POSIX identity
        +--> HFS / zFS pathname space
        +--> files / directories / permissions
        +--> processes / signals
        +--> /bin/sh
        +--> shell scripts
```

This repository owns evidence about how those runtime mechanisms behave in the tested environment. It does not absorb the ownership of RACF, Communications Server, JCL/JES2, workload automation or core system configuration.

---

## 2. Evidence state

The repository currently contains this evidence chain:

| Lab | Scope | State |
|---|---|---|
| 01 | USS environment baseline | **VALIDATED LOCALLY** |
| 02 | Filesystem, links and POSIX permissions | **VALIDATED LOCALLY** |
| 03 | Process and runtime management | **VALIDATED LOCALLY** |
| 04 Part 1 | Controlled shell-scripting fundamentals | **VALIDATED LOCALLY** |
| 04 Part 2 | Advanced shell-scripting continuation | **PENDING** |

### Lab 01

Evidence establishes OMVS access, effective UID/GID observation, RACF OMVS attribute correlation, HFS/zFS observation and the active IPL → IEASYS → BPXPRM configuration relationship.

The laboratory identity is privileged. The evidence therefore describes the observed identity and configuration; it does not establish ordinary-user authorization behavior.

### Lab 02

Evidence establishes files/directories, `umask`, `chmod`, redirection, copy/rename, hard links, symbolic links and executable-file behavior.

Permission metadata is validated, but non-privileged denial behavior is not.

### Lab 03

Evidence establishes PID/PPID observation, foreground/background execution, shell job control, `$!`, `kill`, `wait`, `$?`, normal completion, signal termination and cleanup.

### Lab 04 Part 1

Evidence establishes `/bin/sh` script execution, variables, positional parameters, POSIX conditionals, explicit return-code control and `for` iteration.

The laboratory's deliberate `RC=8` is an application-level test choice, not a universal z/OS return-code definition.

Part 2 remains pending.

---

## 3. Validation vocabulary

Cross-repository documentation should use evidence states consistently.

| State | Meaning |
|---|---|
| **VALIDATED LOCALLY** | Demonstrated by evidence in this repository |
| **VALIDATED IN TARGET REPOSITORY** | Demonstrated elsewhere; this repository may consume the capability but does not own its evidence |
| **CROSS-DOMAIN / REQUIRES EVIDENCE** | Architectural relationship is understood, but the end-to-end path has not yet been demonstrated |
| **PLANNED** | Roadmap work with no current validation claim |

A component appearing in an architecture diagram does not make its integration validated.

---

## 4. Ownership matrix

| Engineering area | Owning repository/domain | USS relationship |
|---|---|---|
| OMVS/POSIX runtime | `UNIX_System_Services-` | **Owned here** |
| UNIX files/processes/shell | `UNIX_System_Services-` | **Owned here** |
| RACF policy and authorization | `mainframe-racf-security-evidence` | USS consumes RACF-backed identity |
| JCL/JES2 batch orchestration | `JCL_LABS` | Future BPXBATCH integration |
| Workload scheduling | `zos-batch-scheduler` | Future scheduler → batch → USS chain |
| TCP/IP/network policy | `zos-communications-server-network-lab` | Future USS service/runtime correlation |
| IPL/PARMLIB/BPXPRM system engineering | `zos-adcd-hercules-engineering-lab` | USS consumes selected system configuration |
| TSO/ISPF interactive environment | `MVS_TSO_ISPF` | Operational entry/context |
| TSO-side automation | `Rexx` | Complementary automation domain |

---

## 5. RACF ↔ USS

RACF remains the authority for identity and authorization. USS consumes RACF-backed UNIX identity information.

```text
RACF user / group
       |
       v
OMVS attributes
       |
       +--> UID / GID
       +--> HOME
       +--> PROGRAM
       |
       v
USS runtime identity
```

### Validated here

Lab 01 correlates observed USS numeric identity with RACF OMVS information.

### Not validated here

This repository does not currently claim end-to-end validation of:

- ordinary non-privileged access denial;
- UNIXPRIV behavior;
- privileged delegation;
- STARTED mappings;
- RACF policy administration.

Those security-policy concerns belong primarily to the RACF domain.

---

## 6. POSIX permissions ↔ RACF protection

USS pathname protection and traditional MVS dataset protection are related to the same broader security environment but are not the same mechanism.

```text
MVS dataset
   |
   +--> RACF DATASET protection

USS pathname
   |
   +--> owner
   +--> group
   +--> mode bits
   +--> RACF-backed UNIX identity
   +--> additional z/OS UNIX security controls
```

Lab 02 validates POSIX permission metadata and behavior available in the tested privileged context. It does not use `chmod` as a substitute for RACF policy.

---

## 7. Core z/OS ↔ USS

Core system engineering owns system-wide configuration and lifecycle concerns:

```text
IPL
 |
 v
IEASYS / PARMLIB
 |
 v
BPXPRM
 |
 +--> limits
 +--> FILESYSTYPE
 +--> MOUNT
 +--> NETWORK
 |
 v
active USS environment
```

Lab 01 observes and correlates this configuration chain from the USS side.

System-level changes, IPL work, PARMLIB engineering, filesystem lifecycle, storage recovery and platform recovery remain responsibilities of the core z/OS engineering domain.

---

## 8. HFS/zFS ↔ USS runtime

The tested environment exposes a mixed HFS/zFS pathname space.

USS owns runtime interaction with the pathname tree; deeper filesystem lifecycle engineering should remain coordinated with the core/storage domain.

Current evidence supports observation and runtime use. It does not claim a complete zFS administration lifecycle covering creation, persistent mounts, backup and recovery.

---

## 9. JCL/JES2 ↔ BPXBATCH ↔ USS

The target cross-domain path is:

```text
JCL / JES2
     |
     v
  BPXBATCH
     |
     v
USS command / script / process
     |
     v
UNIX exit status / output
     |
     v
JCL step result
```

Ownership remains separated:

**JCL/JES2 owns**

- job structure;
- step sequencing;
- DD definitions;
- JES execution context;
- batch restart/condition logic.

**USS owns**

- UNIX pathnames;
- shell semantics;
- POSIX processes;
- script behavior;
- UNIX exit status.

### Current evidence state

**CROSS-DOMAIN / REQUIRES EVIDENCE.**

Lab 04 Part 1 does not validate BPXBATCH or JCL-driven USS execution.

---

## 10. Workload automation ↔ USS

A future production-style chain is:

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
JCL result
   |
   v
scheduler decision
```

The scheduler repository owns workload definitions, readiness/dependency semantics and scheduler state. USS owns the UNIX-side runtime.

### Current evidence state

**CROSS-DOMAIN / REQUIRES EVIDENCE.**

The diagram is an integration target, not a statement that the complete path has already executed successfully.

---

## 11. Communications Server ↔ USS

A network-facing USS service can involve several ownership domains:

```text
Communications Server
        |
        +--> TCP/IP profile
        +--> listener / port
        +--> Policy Agent / AT-TLS
        |
        v
USS-hosted process
        |
        +--> process runtime
        +--> /etc configuration
        +--> pathname permissions
        +--> UNIX identity
        |
        v
RACF / SAF authorization
```

Communications Server owns network configuration and policy. USS owns the UNIX process/runtime side. RACF owns identity and authorization policy.

### Current evidence state

**CROSS-DOMAIN / REQUIRES EVIDENCE.**

No production-like USS network service or end-to-end AT-TLS path is claimed by the current USS labs.

---

## 12. REXX ↔ USS shell automation

REXX and USS shell scripting are complementary rather than competing automation tracks.

```text
REXX
 -> TSO/E and z/OS-oriented automation

USS /bin/sh
 -> UNIX-side operational automation
```

Lab 04 Part 1 now provides local evidence for basic `/bin/sh` scripting. This does not turn the USS repository into a generic scripting repository.

Future shell automation should remain tied to operational USS use cases such as controlled filesystem work, process checks, return-code handling, cleanup and eventual batch integration.

---

## 13. MVS ↔ USS data movement

A future integration track should demonstrate controlled movement between traditional MVS data and USS pathnames.

Possible architecture:

```text
MVS dataset
    |
    v
controlled transfer mechanism
    |
    v
USS file
    |
    +--> pathname
    +--> ownership / mode
    +--> shell or process consumption
```

### Current evidence state

**PLANNED.**

No completed lab in this repository currently establishes this end-to-end path.

---

## 14. Current capability boundaries

### VALIDATED LOCALLY

- OMVS shell access;
- effective POSIX UID/GID observation;
- RACF OMVS attribute correlation;
- HFS/zFS observation;
- BPXPRM/PARMLIB selection correlation;
- files and directories;
- `umask` and `chmod`;
- hard/symbolic links;
- executable command-file behavior;
- PID/PPID;
- background processes;
- shell job control;
- signals;
- `wait` and exit-status interpretation;
- `/bin/sh` script execution;
- variables and positional parameters;
- POSIX conditionals;
- explicit application return codes;
- POSIX `for` iteration.

### PENDING / REQUIRES NEW EVIDENCE

- Lab 04 Part 2;
- non-privileged authorization behavior;
- MVS-to-USS data movement;
- JCL/BPXBATCH integration;
- scheduler-driven USS execution;
- complete USS runtime/logging/SMF correlation;
- production-like USS network services;
- end-to-end AT-TLS integration;
- full zFS lifecycle administration.

---

## 15. Production-track relationships

USS can participate in several wider portfolio tracks without owning them.

| Production track | USS contribution | Current USS state |
|---|---|---|
| Enterprise Batch Operations | future BPXBATCH runtime | Requires evidence |
| Secure Batch Application | UNIX identity/runtime boundary | Requires end-to-end evidence |
| Secure Network Service | USS-hosted process/runtime | Requires evidence |
| Storage Recovery | pathname/filesystem consumer | System recovery owned elsewhere |
| Operations Automation | `/bin/sh` scripting | Part 1 locally validated |
| Problem Determination | process/exit-status evidence | Foundation locally validated |
| End-to-End Production Cycle | UNIX runtime stage | Requires cross-domain evidence |

---

## 16. Engineering lifecycle

The portfolio-wide engineering lifecycle is:

```text
Discover
   |
Baseline
   |
Configure
   |
Operate
   |
Observe
   |
Diagnose
   |
Recover
   |
Improve
   |
Automate
   |
Integrate
```

USS evidence currently covers strong foundational work in discovery, baseline, operation and observation, with controlled runtime diagnosis and initial automation evidence.

Maturity should advance only when new evidence justifies it.

---

## 17. Evidence method

USS labs follow the portfolio evidence method:

```text
BUILD → EXECUTE → OBSERVE → DIAGNOSE → CORRECT → VALIDATE → DOCUMENT
```

Evidence should preserve:

- executed commands;
- relevant environment state;
- execution identity;
- returned status;
- object/state changes;
- troubleshooting;
- limitations;
- screenshots or source artifacts when they add proof.

Observed failures should remain documented when they explain release-specific or environment-specific behavior.

---

## 18. Publication security

Before publication, inspect evidence for unnecessary disclosure of:

- host IP addresses;
- MAC addresses;
- hostnames;
- workstation usernames;
- host-side paths;
- credentials or secrets;
- tokens and private keys;
- sensitive certificate material;
- terminal/session identifiers;
- nonessential network details.

Publication sanitization must not alter the technical meaning of the evidence.

---

## 19. Development and branch model

Normal work should remain isolated and reviewable:

```text
main
 |
 +-- lab/<number>-<slug>
 +-- docs/<topic>
 +-- integration/<cross-repo-topic>
 +-- fix/<slug>
```

Typical lifecycle:

```text
branch
  -> implement
  -> validate
  -> security review
  -> document
  -> PR
  -> merge
```

Integration branches should be created only when there is actual cross-repository work to validate, not merely to advertise planned architecture.

---

## 20. Roadmap

A coherent evidence progression from the current state is:

```text
Lab 04 Part 2
advanced controlled shell scripting
        |
        v
non-privileged RACF / OMVS validation
        |
        v
MVS-to-USS data movement
        |
        v
JCL / BPXBATCH
        |
        v
runtime / logging / troubleshooting
        |
        v
network-service correlation
        |
        v
scheduler / production-track integration
```

Exact future lab numbering may evolve. Evidence dependencies matter more than preserving a speculative numbering scheme.

---

## 21. Learning journey vs runtime architecture

The portfolio learning journey can place USS after foundational TSO/ISPF, JCL, scheduler and REXX work:

```text
MVS_TSO_ISPF
      |
      v
JCL_LABS
      |
      v
zos-batch-scheduler
      |
      v
Rexx
      |
      v
UNIX_System_Services-
```

This is a **learning sequence**, not a runtime dependency diagram.

Runtime architecture should instead show the actual component relationships relevant to a given scenario.

---

## 22. Navigation

### Portfolio and governance

- [IBM z/OS Mainframe Engineering Portfolio](https://github.com/P-dot/P-dot)
- [Architecture V2](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/architecture/v2)
- [Engineering Control](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/engineering-control)
- [Core z/OS Engineering](https://github.com/P-dot/zos-adcd-hercules-engineering-lab)

### Related domains

- [RACF Security](https://github.com/P-dot/mainframe-racf-security-evidence)
- [Communications Server](https://github.com/P-dot/zos-communications-server-network-lab)
- [JCL/JES2](https://github.com/P-dot/JCL_LABS)
- [Workload Automation](https://github.com/P-dot/zos-batch-scheduler)
- [TSO/ISPF](https://github.com/P-dot/MVS_TSO_ISPF)
- [REXX](https://github.com/P-dot/Rexx)

### Local evidence

- [Lab 01 — USS Environment Baseline](../labs/01-uss-environment-baseline/)
- [Lab 02 — Filesystem & POSIX Permissions](../labs/02-uss-filesystem-posix-permissions/)
- [Lab 03 — Process & Runtime Management](../labs/03-uss-process-runtime-management/)
- [Lab 04 — Controlled Shell Scripting](../labs/04-uss-shell-scripting/)

---

## 23. Architectural summary

`UNIX_System_Services-` is the **USS / OMVS / POSIX runtime engineering layer** of the portfolio.

```text
system-selected z/OS environment
          |
          v
RACF-backed UNIX identity
          |
          v
HFS/zFS pathname space
          |
          v
files / permissions / links
          |
          v
processes / signals / status
          |
          v
/bin/sh scripting
          |
          v
future cross-domain integration
   +------+------+------+
   |      |      |      |
  JCL   RACF   TCP/IP scheduler
```

The repository should continue to distinguish rigorously between **local evidence**, **evidence owned by another domain**, **cross-domain work requiring validation**, and **planned work**.
