For when nmap won't work
https://linuxvox.com/blog/ping-sweep-linux/
```
#!/bin/bash
for ip in 192.168.1.{1..254}; do
    ping -c 1 -W 1 $ip > /dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "$ip is alive"
    fi
done
```