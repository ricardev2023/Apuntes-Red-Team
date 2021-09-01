# MEJORANDO SHELL A TTY INTERACTIVA \(Traducir\)

## Shell to Bash

Upgrade from shell to bash.

`SHELL=/bin/bash script -q /dev/null`

## Python PTY Module

Spawn `/bin/bash` using [Python's PTY module](https://docs.python.org/3/library/pty.html), and connect the controlling shell with its standard I/O.

`python -c 'import pty; pty.spawn("/bin/bash")'`

## Fully Interactive TTY

Background the current remote shell \(`^Z`\), update the **local** terminal line settings with `stty`[2](https://0xffsec.com/handbook/shells/full-tty/#fn:2) and bring the remote shell back.  
After bringing the job back the cursor will be pushed to the left. Reinitialize the terminal with `reset`.

`Ctrl-Z  
stty raw -echo  
fg  
reset  
xterm`

