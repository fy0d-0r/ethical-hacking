# Linux Previlege Escalation

## System Enumeration
### Basic Informations
```
hostname
uname -a
cat /proc/version #for kernel and gcc version
cat /etc/issue
```
### Process Informations
```
ps       #show processes in the current shell(session or terminal,eg.pts)
ps -A    #view all running processes regardless of in which the session they are running
ps axjf  #view process tree
pstree   #view process tree
```
