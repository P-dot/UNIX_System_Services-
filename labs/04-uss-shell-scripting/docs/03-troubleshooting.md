# Troubleshooting and Lessons Learned

No failed execution prevents Part 1 from closing.

Two interpretation points are intentionally documented:

1. A script's logical message (`ACTION=UNKNOWN`) is independent from its process return code unless the script explicitly controls the exit status.
2. The permission evidence for `/bin/sh` is treated as the normal mode shown by the captured `ls -l` output; no unobserved ACL or extended-security marker is claimed.

The laboratory avoids Bash-only constructs and records only behavior observed in the z/OS 1.11 `/bin/sh` environment.
