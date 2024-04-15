# Linux Previlege Escalation

## System Enumeration
### Basic Informations
```
hostname
uname -a
id
history
cat /proc/version #for kernel and gcc version
cat /etc/issue
cat /etc/passwd
```
### Process Informations
```
ps       #show processes in the current shell(session or terminal,eg.pts)
ps -A    #view all running processes regardless of in which the session they are running
ps axjf  #view process tree
pstree   #view process tree
```
```
ps aux
a - all processes on the system
u - display the user by which the process is launched
x - show processes that are not attached to a terminal
jf - display in tree like format 
```
### Environmental Variables
```
env
echo $PATH
```

### Sudo Privileges
```
sudo -l
```

### Network Informations









