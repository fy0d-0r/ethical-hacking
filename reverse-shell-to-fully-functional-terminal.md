# How to Upgrade Reverse Shell to Fully Functional Terminal

```
python3 -c 'import pty; pty.spawn("/bin/bash");'
Ctrl-z
echo $TERM
stty -a
stty raw -echo
fg
export SHELL=bash
export TERM=xterm-256color
stty rows 50 columns 100
reset
```
