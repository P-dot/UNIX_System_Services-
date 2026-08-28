# Evidence analysis

## Interactive USS baseline

The OMVS session starts with a prompt rooted at `/u/ibmuser`. `pwd` and `echo $HOME` independently confirm that both the current working directory and HOME are `/u/ibmuser`. `echo $SHELL` returns `/bin/sh`.

`ls -la` shows that the directory is not empty. It contains pre-existing ADCD sample/test material, including Java/C-related files, dumps and subdirectories. This is treated as baseline evidence only; the lab does not modify these files.

## RACF to POSIX identity

The shell reports:

```text
uid=0(START2) gid=0(SYS1)
```

RACF then shows:

```text
IBMUSER OMVS:
  UID=0000000000
  HOME=/u/ibmuser
  PROGRAM=/bin/sh

SYS1 OMVS:
  GID=0000000000
```

The evidence therefore proves that `IBMUSER` is configured with numeric UID 0 and group `SYS1` with numeric GID 0. The shell's display of `START2` is a name-resolution result for UID 0 and is documented as such rather than being treated as proof that the TSO user changed identity.

## Active filesystem layout

`df -k` shows both HFS and zFS-backed mounts. The `/u` mount is associated with the ADCD users HFS filesystem, while several product/application locations are backed by zFS.

This is confirmed declaratively in `BPXPRMDB`, which contains MOUNT statements with explicit `TYPE(HFS)` and `TYPE(ZFS)` values.

## PARMLIB to runtime configuration

The IPL and PARMLIB chain is established by three separate evidence points:

1. `D IPLINFO` reports `IEASYS LIST = DB`.
2. `ADCD.Z111S.PARMLIB(IEASYSDB)` contains `OMVS=DB`.
3. `ADCD.Z111S.PARMLIB(BPXPRMDB)` contains the z/OS UNIX parameters and mount definitions.

`D OMVS,O` then shows the effective runtime values. Multiple values visible in BPXPRMDB match those displayed as active, including process/user limits and other OMVS settings. This ties declared PARMLIB configuration to the running USS instance.

## Lab conclusion

The ADCD z/OS 1.11 environment has an operational USS subsystem integrated with RACF, using `/bin/sh`, a mixed HFS/zFS filesystem hierarchy and an OMVS configuration selected through `IEASYSDB -> OMVS=DB -> BPXPRMDB`.
