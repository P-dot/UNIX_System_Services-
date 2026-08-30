# 03 - Troubleshooting

## Initial redirection attempt

A first attempt with longer quoted `echo` strings did not produce the expected content in `sample.txt`.

Instead of changing shell configuration, the issue was isolated with a minimal test:

```sh
echo TEST
echo TEST > test.txt
ls -l test.txt
cat test.txt
```

Observed result:

- `echo TEST` printed normally.
- `test.txt` was created with size 5 bytes.
- `cat test.txt` returned `TEST`.

This proved that output redirection was operational in the active OMVS shell.

The lab then used shorter input:

```sh
echo RECORD1 > sample.txt
echo RECORD2 >> sample.txt
```

which succeeded. The temporary diagnostic file was removed before the final state.

## Broken symbolic link

After deliberately deleting the symlink target name, `cat sample-symlink.txt` returned:

```text
EDC5129I No such file or directory.
```

This is not treated as a lab failure. It is the expected result of the hard-link versus symbolic-link test. The broken symlink was removed during cleanup.
