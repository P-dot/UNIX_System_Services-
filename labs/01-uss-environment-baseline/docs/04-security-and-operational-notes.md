# Security and operational notes

## UID 0 is significant

The evidence shows that `IBMUSER` has OMVS `UID=0`. UID 0 is a privileged UNIX identity and should be treated as a security-relevant baseline finding. This lab records the condition but does not change it.

The same numeric UID is displayed by `id` with the resolved name `START2`, highlighting why unique UID/GID assignment matters for unambiguous accountability.

## Separation of observations and changes

This first lab performs discovery only:

- no `ALTUSER` or `ALTGROUP` commands;
- no `chmod`, `chown` or file creation;
- no filesystem mount/unmount;
- no BPXPRM or IEASYS edits;
- no dynamic OMVS parameter changes.

This keeps the baseline reproducible and prevents the act of measurement from changing the system being measured.

## Portfolio hygiene

The included screenshots do not expose host IP addresses, MAC addresses or Windows adapter identifiers. Before publication, the repository should continue to exclude raw network-sensitive data, credentials, private keys, passwords and licensed system binaries.
