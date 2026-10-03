# z/OS UNIX System Services Engineering Labs

Hands-on engineering labs for **z/OS UNIX System Services (USS)** in an ADCD / Hercules environment.

This repository is the **USS / OMVS / POSIX runtime layer** of the wider IBM z/OS engineering portfolio. It documents how UNIX semantics behave inside z/OS and preserves evidence from the actual tested environment rather than assuming GNU/Linux behavior.

> **Current evidence boundary:** Labs 01–03 are completed. Lab 04 Part 1 is completed; Part 2 remains pending. Cross-domain integrations such as BPXBATCH/JCL, scheduler-driven USS execution and network-facing USS services are roadmap work unless explicitly backed by evidence.

---

## Quick navigation

| Area | Destination |
|---|---|
| Engineering portfolio | [P-dot portfolio](https://github.com/P-dot/P-dot) |
| Detailed ecosystem integration | [docs/ECOSYSTEM-INTEGRATION.md](docs/ECOSYSTEM-INTEGRATION.md) |
| Lab 01 — Environment baseline | [Open lab](labs/01-uss-environment-baseline/) |
| Lab 02 — Filesystem & POSIX permissions | [Open lab](labs/02-uss-filesystem-posix-permissions/) |
| Lab 03 — Process & runtime management | [Open lab](labs/03-uss-process-runtime-management/) |
| Lab 04 — Controlled shell scripting | [Open lab](labs/04-uss-shell-scripting/) |
| Core z/OS engineering | [zos-adcd-hercules-engineering-lab](https://github.com/P-dot/zos-adcd-hercules-engineering-lab) |
| Architecture V2 | [Architecture V2](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/architecture/v2) |
| Engineering Control | [Engineering Control](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/engineering-control) |

---

## Repository role

`UNIX_System_Services-` owns practical engineering evidence for the UNIX-facing runtime inside z/OS:

- OMVS and `/bin/sh`;
- POSIX UID/GID observation;
- RACF-backed UNIX identity as observed from USS;
- HFS/zFS pathname-space observation;
- files, directories, permissions and links;
- processes, PID/PPID, shell jobs and signals;
- exit-status interpretation;
- controlled POSIX shell scripting.

It does **not** replace the repositories that own RACF policy, TCP/IP configuration, JCL/JES2 orchestration, workload scheduling, or system-wide z/OS configuration.

---

## Environment

The published evidence was produced in the tested laboratory environment:

- IBM z/OS 1.11 ADCD;
- Hercules;
- TSO/E, ISPF and SDSF;
- z/OS UNIX System Services / OMVS;
- RACF;
- HFS and zFS;
- `/bin/sh`.

Results are version- and environment-specific where appropriate.

---

## Lab progression

| Lab | Engineering focus | Evidence state |
|---|---|---|
| [01](labs/01-uss-environment-baseline/) | USS environment baseline | **COMPLETED** |
| [02](labs/02-uss-filesystem-posix-permissions/) | Filesystem, links and POSIX permissions | **COMPLETED** |
| [03](labs/03-uss-process-runtime-management/) | Process and runtime management | **COMPLETED** |
| [04](labs/04-uss-shell-scripting/) | Controlled shell scripting | **PART 1 COMPLETED / PART 2 PENDING** |

```text
Environment
    |
    v
Filesystem / permissions
    |
    v
Processes / runtime
    |
    v
Shell scripting
    |
    +--> Part 1: fundamentals validated
    |
    +--> Part 2: pending
```

### Lab 01 — Environment baseline

Establishes the active USS baseline and correlates OMVS access, effective POSIX identity, RACF OMVS attributes, HFS/zFS observations and the IPL/PARMLIB/BPXPRM configuration chain.

### Lab 02 — Filesystem and POSIX permissions

Validates controlled filesystem operations, `umask`, `chmod`, redirection, copy/rename, hard links, symbolic links and executable-file behavior.

Because the observed laboratory identity is privileged, this lab does **not** claim ordinary-user authorization-denial validation.

### Lab 03 — Process and runtime management

Validates PID/PPID observation, foreground/background execution, shell job control, `$!`, `kill`, `wait`, `$?`, normal completion and signal termination.

The observed SIGTERM path returned status `143`; the lab records this as observed shell behavior and relates it to `128 + 15`.

### Lab 04 — Controlled shell scripting

Part 1 validates `/bin/sh` scripting fundamentals:

- executable shell scripts;
- variables and expansion;
- positional parameters;
- POSIX conditionals;
- explicit application return codes;
- `for` iteration.

The deliberately selected `RC=8` demonstrates explicit process-status control; it is **not** presented as a universal z/OS return-code meaning.

Part 2 remains pending and is expected to continue with functions, stdout/stderr, redirection, pipelines, controlled error handling, cleanup and combined operational scripting.

---

## Validated capability map

### VALIDATED LOCALLY

| Capability | Evidence |
|---|---|
| OMVS shell access | Lab 01 |
| Effective UID/GID observation | Lab 01 |
| RACF OMVS attribute correlation | Lab 01 |
| HFS/zFS observation | Lab 01 |
| BPXPRM/PARMLIB selection correlation | Lab 01 |
| File and directory operations | Lab 02 |
| `umask` and `chmod` | Lab 02 |
| Hard and symbolic links | Lab 02 |
| Executable command-file behavior | Lab 02 |
| PID/PPID inspection | Lab 03 |
| Background execution and shell jobs | Lab 03 |
| Signals, `wait` and exit status | Lab 03 |
| `/bin/sh` script execution | Lab 04 Part 1 |
| Variables and positional parameters | Lab 04 Part 1 |
| POSIX conditional logic | Lab 04 Part 1 |
| Explicit application return codes | Lab 04 Part 1 |
| POSIX `for` loop | Lab 04 Part 1 |

### NOT YET CLAIMED AS VALIDATED HERE

- ordinary non-privileged POSIX authorization behavior;
- Lab 04 Part 2 scripting topics;
- MVS-to-USS dataset/file movement;
- JCL/BPXBATCH execution;
- scheduler-driven USS execution;
- production-like USS network services;
- end-to-end AT-TLS-protected USS services;
- full zFS lifecycle administration;
- complete USS logging/SMF correlation.

This distinction is intentional: architecture and roadmap items are not presented as completed evidence.

---

## Architecture and ownership boundaries

```text
                    IBM z/OS
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
   RACF / SAF       JCL / JES2       TCP/IP
       |               |               |
       +---------------+---------------+
                       |
                       v
                   USS / OMVS
                       |
          +------------+------------+
          |            |            |
          v            v            v
       identity     filesystem    processes
                                    |
                                    v
                                   shell
```

Repository ownership remains explicit:

| Domain | Primary ownership |
|---|---|
| **USS** | POSIX runtime, shell, UNIX pathnames, files, processes and UNIX exit status |
| **RACF** | identity and authorization policy, UNIXPRIV, STARTED mappings and security controls |
| **JCL/JES2** | batch job structure, steps, DDs, JES execution and batch control |
| **Workload automation** | scheduling, dependencies, resources and operational decisions |
| **Communications Server** | TCP/IP profile, listeners, ports, Policy Agent and AT-TLS |
| **Core z/OS** | IPL, IEASYS, PARMLIB, BPXPRM and system-level configuration |

See [Ecosystem Integration](docs/ECOSYSTEM-INTEGRATION.md) for the detailed cross-repository model.

---

## Evidence model

Labs follow an evidence-first workflow:

```text
BUILD
  |
  v
EXECUTE
  |
  v
OBSERVE
  |
  v
DIAGNOSE
  |
  v
CORRECT
  |
  v
VALIDATE
  |
  v
DOCUMENT
```

A useful lab record answers:

- what was executed;
- which environment and identity were active;
- what changed;
- what result or return status was produced;
- what evidence proves the result;
- what limitations affect interpretation.

Failed or environment-specific attempts are retained when they explain real behavior rather than being rewritten as artificial success.

---

## Integration roadmap

The current evidence supports a progression toward deeper cross-domain integration:

```text
Lab 04 Part 2
shell scripting depth
        |
        v
non-privileged RACF / OMVS validation
        |
        v
MVS <-> USS data movement
        |
        v
JCL / BPXBATCH
        |
        v
runtime diagnostics / logging
        |
        v
network-service correlation
```

Target execution chains such as the following remain **cross-domain objectives until evidence is published**:

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
batch / scheduler decision
```

---

## Related engineering domains

- [Mainframe RACF Security Evidence](https://github.com/P-dot/mainframe-racf-security-evidence)
- [z/OS Communications Server Network Lab](https://github.com/P-dot/zos-communications-server-network-lab)
- [JCL Labs](https://github.com/P-dot/JCL_LABS)
- [z/OS Batch Scheduler](https://github.com/P-dot/zos-batch-scheduler)
- [MVS TSO/ISPF](https://github.com/P-dot/MVS_TSO_ISPF)
- [REXX](https://github.com/P-dot/Rexx)
- [Core z/OS / ADCD / Hercules Engineering](https://github.com/P-dot/zos-adcd-hercules-engineering-lab)

The learning journey can pass through `MVS_TSO_ISPF → JCL_LABS → zos-batch-scheduler → Rexx → UNIX_System_Services-`; that learning sequence does not imply that these repositories share the same runtime ownership.

---

## Publication security

Before publication, evidence should be reviewed for unnecessary exposure of host IP addresses, MAC addresses, hostnames, local workstation paths, credentials, secrets, tokens, private keys, sensitive certificate material, terminal/session identifiers and other host-side network details.

Only information required to explain the laboratory should be published.

---

## Repository structure

```text
.
├── README.md
├── security-scan.txt
├── docs/
│   └── ECOSYSTEM-INTEGRATION.md
└── labs/
    ├── 01-uss-environment-baseline/
    ├── 02-uss-filesystem-posix-permissions/
    ├── 03-uss-process-runtime-management/
    └── 04-uss-shell-scripting/
```

Individual lab READMEs remain the authoritative evidence summaries for their exercises. This root README is intentionally a navigation and engineering overview rather than a duplicate of every lab.

---

## Portfolio position

**Portfolio:** [IBM z/OS Mainframe Engineering Portfolio](https://github.com/P-dot/P-dot)
**Architecture:** [Architecture V2](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/architecture/v2)
**Engineering control:** [Engineering Control](https://github.com/P-dot/zos-adcd-hercules-engineering-lab/tree/main/docs/engineering-control)
**Detailed USS integration:** [Ecosystem Integration](docs/ECOSYSTEM-INTEGRATION.md)

`UNIX_System_Services-` provides the practical **USS / OMVS / POSIX runtime layer** of that ecosystem.
