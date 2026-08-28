# Troubleshooting: unavailable `mount` command

## Symptom

The command:

```sh
mount
```

returned:

```text
mount: FSUM7351 not found
```

## Checks performed

The lab did not assume a missing filesystem or a broken USS subsystem, because `df -k` had already proved that filesystems were mounted and readable.

The following read-only checks were used:

```sh
echo $PATH
whence mount
```

The PATH contains `/bin`, but `whence mount` returned no location. Additional file/path checks shown in the evidence also failed to identify a usable `mount` utility in the tested context.

## Interpretation

The result is documented as an environment-specific command availability/resolution limitation. It is not interpreted as evidence that USS has no mounted filesystems.

For this baseline, `df -k`, `D OMVS,O`, `IEASYSDB` and `BPXPRMDB` provide sufficient evidence to reconstruct the filesystem and configuration model without changing the system or forcing a workaround.
