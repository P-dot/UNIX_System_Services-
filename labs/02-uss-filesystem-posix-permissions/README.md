# Lab 02 - USS Filesystem, Files, Links and POSIX Permissions

## Objective

Build and validate a controlled z/OS UNIX System Services workspace under `/u/ibmuser/uss-lab02` and demonstrate filesystem operations, POSIX permission handling, redirection, file copying/moving, hard links, symbolic links and executable-file behavior from the OMVS shell.

This lab follows the environment baseline established in Lab 01 and confines all changes to the dedicated lab directory.

## What this lab proves

| Area | Evidence | Result |
|---|---|---|
| Working directory | `pwd` | USS session operates from `/u/ibmuser`. |
| Directory creation | `mkdir uss-lab02` | Dedicated lab workspace created successfully. |
| Default directory mode | `ls -ld` + `umask` | `umask 0022` produces mode `755` for newly created directories. |
| Workspace layout | `mkdir data logs scripts` | Isolated `data`, `logs` and `scripts` directories created. |
| Default file mode | `touch` + `ls -l` | New regular file created as `644`, consistent with base `666` and `umask 0022`. |
| Symbolic chmod | `chmod go-r` | File changed from `644` to `600`. |
| Octal chmod | `chmod 640` | Owner/group/other permissions explicitly set to `rw-r-----`. |
| Redirection | `>`, `>>`, `cat` | Overwrite and append behavior validated. |
| Copy | `cp` | Independent file copy created with expected content. |
| Move/rename | `mv` | File renamed without losing its content. |
| Hard link | `ln` + `ls -li` | Both names share inode/file identifier `208`; link count becomes 2. |
| Symbolic link | `ln -s` + `ls -li` | Symlink is a separate object and points by pathname to `sample.txt`. |
| Link failure behavior | `rm` + `cat` | Hard link survives target-name removal; symlink becomes broken with `EDC5129I`. |
| Directory permissions | `chmod 700/755` | Directory mode deliberately restricted and then restored. |
| Execute bit | `chmod u+x` | `runlab` changes from `644` to `744`. |
| Shell execution | `./runlab` + `echo $?` | Script-like command file executes and returns RC `0`. |

## Final lab layout

```text
/u/ibmuser/uss-lab02
├── data
│   ├── sample.txt
│   └── sample-backup.txt
├── logs
└── scripts
    └── runlab
```

## Key findings

### 1. `umask` removes permissions; it does not grant them

The active `umask` is `0022`. New directories start from mode `777`, resulting in `755`. New regular files start from mode `666`, resulting in `644`; execute permission is not automatically added.

### 2. File content and permission metadata are independent

Writing `RECORD1` and appending `RECORD2` changed the content and size of `sample.txt` while the previously configured mode `640` remained in place.

### 3. Hard links and symbolic links behave differently

`sample.txt` and `sample-hardlink.txt` shared identifier `208`, proving that both directory entries referenced the same underlying object. The symbolic link had its own identifier and stored a pathname reference.

After removing `sample.txt`, the hard link continued to return `RECORD1` and `RECORD2`, while the symbolic link failed with `EDC5129I No such file or directory`.

### 4. The execute bit has practical effect

`runlab` was created without execute permission, then changed with `chmod u+x` to `-rwxr--r--`. Executing `./runlab` produced `USS-LAB02-OK` and `echo $?` returned `0`.

### 5. Privileged identity affects authorization testing

Lab 01 established that the observed USS session uses effective UID 0/GID 0. For that reason, this lab demonstrates permission metadata changes but does not claim that access-denial behavior observed under this identity represents an ordinary non-privileged USS user.

## Troubleshooting retained

An initial attempt using longer quoted `echo` input did not write the expected data. A minimal diagnostic test:

```sh
echo TEST
echo TEST > test.txt
ls -l test.txt
cat test.txt
```

created a 5-byte file and returned `TEST`, proving that shell redirection itself worked. The lab then continued with shorter `RECORD1`/`RECORD2` input. The temporary `test.txt` file was removed during cleanup.

## Evidence

Screenshots are stored in [`evidence/screenshots/`](evidence/screenshots/). The original four-page DOCX supplied during the lab is preserved unchanged in [`evidence/source/`](evidence/source/).

## Documentation

- [Command walkthrough](docs/01-command-walkthrough.md)
- [Evidence analysis](docs/02-evidence-analysis.md)
- [Troubleshooting](docs/03-troubleshooting.md)
- [Security and operational notes](docs/04-security-and-operational-notes.md)
- [References](docs/05-references.md)

## Result

**Completed and validated.** The final execution returned **RC=0** and all filesystem changes remained confined to `/u/ibmuser/uss-lab02`.
