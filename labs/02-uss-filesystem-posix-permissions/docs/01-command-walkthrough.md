# 01 - Command Walkthrough

## Workspace and defaults

`pwd` confirms the current USS working directory. `mkdir uss-lab02` creates the isolated lab directory. `ls -ld` displays the directory object itself, including type, mode, owner and group. `umask` reports the mask applied when new filesystem objects are created.

With `umask 0022`, a directory created from base mode `777` becomes `755`. A regular file created from base mode `666` becomes `644`.

## Files and permissions

`touch data/sample.txt` creates an empty regular file. `chmod go-r` removes read permission from group and others, producing mode `600`. `chmod 640` then explicitly sets owner `rw-`, group `r--`, others `---`.

`echo ... > file` replaces file content; `echo ... >> file` appends. `cat` reads the resulting content.

## Copy and move

`cp sample.txt sample-copy.txt` creates another file containing the same data. `mv sample-copy.txt sample-backup.txt` renames/moves that filesystem entry.

## Links

`ln sample.txt sample-hardlink.txt` creates another directory entry for the same underlying object. `ls -li` proves this by displaying the same identifier and an increased link count.

`ln -s sample.txt sample-symlink.txt` creates a separate symbolic-link object containing a pathname reference.

Removing `sample.txt` demonstrates the difference: the hard link still accesses the underlying data, while the symlink points to a pathname that no longer exists.

## Directory modes

`chmod 700 logs` restricts the directory to owner `rwx`; `chmod 755 logs` restores owner `rwx` and group/other `r-x`.

Because the effective session identity is UID 0, the lab records mode changes without presenting privileged access behavior as a valid ordinary-user denial test.

## Execute permission

`runlab` is created as a regular `644` file containing `echo USS-LAB02-OK`. `chmod u+x runlab` adds owner execute permission and produces `744`.

`./runlab` executes the file from the current directory. `echo $?` returns the previous command's status. The observed result was `0`.
