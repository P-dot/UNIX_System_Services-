# 02 - Evidence Analysis

## Permission baseline

The evidence shows `umask` returning `0022`. The newly created `uss-lab02`, `data`, `logs` and `scripts` directories appear as `drwxr-xr-x` (`755`). The newly created `sample.txt` appears as `-rw-r--r--` (`644`).

This demonstrates the different creation bases used for directories (`777`) and regular files (`666`) before application of the mask.

## Deliberate permission changes

`sample.txt` is shown successively as `644`, `600` and `640`, validating both symbolic and octal `chmod` forms. `logs` is shown changing from `755` to `700` and then being restored to `755`.

## Redirection and file operations

The successful diagnostic `test.txt` is 5 bytes and contains `TEST`. The final `sample.txt` is 16 bytes and contains two records:

```text
RECORD1
RECORD2
```

The copy and renamed backup contain the same records.

## Link semantics

The hard-link evidence shows identifier `208` for both `sample.txt` and `sample-hardlink.txt`, with link count `2`.

The symbolic link has identifier `212` and is displayed as `sample-symlink.txt -> sample.txt`.

After `rm sample.txt`, `sample-hardlink.txt` remains readable while `cat sample-symlink.txt` returns `EDC5129I No such file or directory`. This is the expected broken-symlink condition and is retained as positive evidence of the semantic difference.

## Final execution

The final screenshot shows:

```text
./runlab
USS-LAB02-OK
echo $?
0
```

and `runlab` remains `-rwxr--r--`. This is the completion criterion for the lab.
