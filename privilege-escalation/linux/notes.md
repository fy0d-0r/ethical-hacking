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
```
find / -type d -writable 2>/dev/null		    #writable directories
find / -type d -perm -222 2>/dev/null		    #at least readable directories 
find / -type d -perm -o=w 2>/dev/null		    #directories that can be written by anyone at least
find / -type d -perm -o=x 2>/dev/null		    #world executable directories
find / -type f -perm -4000 2>/dev/null		  #files with suid permission
find / -type f -perm -u=s 2>/dev/null		    #files with suid permission
find / -type f -perm -4000 -ls 2>/dev/null  #files with suid permission (long listing)
```

### Automated Enumeration Tools

- [LinPeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
- [LinEnum](https://github.com/rebootuser/LinEnum)
- [LES (Linux Exploit Suggester)](https://github.com/mzet-/linux-exploit-suggester)
- [Linux Smart Enumeration](https://github.com/diego-treitos/linux-smart-enumeration)
- [Linux Priv Checker](https://github.com/linted/linuxprivchecker)



### SUID and SGID

```
find / -type f -perm -4000 -ls 2>/dev/null #print suid files and also sgid fils
find / -type f -perm -2000 -ls 2>/dev/null #print only sgid files
```

```
SUID (SetUID):
        Octal value: 4000
        Binary representation: 10000000000
        Effect: When set on an executable file, the file is executed with the privileges of the file owner (rather than the user executing the file).

SGID (SetGID):
        Octal value: 2000
        Binary representation: 01000000000
        Effect:
            When set on an executable file, the file is executed with the privileges of the group that owns the file.
            When set on a directory, files created within the directory inherit the group ownership of the directory (rather than the primary group of the user creating the file).
```

#### Abusing SUID permission on `base64`

The following is extrected from [GTFO bins](https://gtfobins.github.io/gtfobins/base64/)
```
sudo install -m =xs $(which base64) .

LFILE=file_to_read
./base64 "$LFILE" | base64 --decode
```
