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

```
netstat -an
netstat -4anp
netstat -4lnp
netstat -at #tcp
netstat -au #udp
netstat -l #list listening ports
netstat -lt4
netstat -s #satistics
netstat -i #interface information
netstat -rn #routing table
netstat -4ano #o for display timers
```
```
ip addr show
ip -br -c addr show
ip route
ifconfig
ifconfig -a
```

### Finding Files

```
find / -mtime 10	#modified time in last 10 days
find / -atime 10	#accessed time in last 10 days
find / -cmin -60	#changed within last 60 mins
find / -amin -60	#access within last 60 mins
```
```
find / -size 50M	#find files with 50M size
find / -size +50M	#find files bigger than 50M size
find / -size -50M	#find files smaller than 50M size
```







