---
layout: default
title: "port"
permalink: /port/
---

# port forwarding

```bash
[host]$ ssh iot@10.10.x.x 
iot@iot:~$ nmap -sT 192.168.0.100

Starting Nmap 7.01 ( https://nmap.org ) 
...
Host is up (0.012s latency).
Not shown: 997 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https
```

```bash
[host]$ ssh -N iot@10.10.x.x -L 8081:192.168.0.100:80
```

```bash 
[host]$ nmap -sT localhost -p 8081
Starting Nmap 7.92 ( https://nmap.org ) 
...
Host is up (0.00013s latency).
Other addresses for localhost (not scanned): ::1

PORT     STATE SERVICE
8081/tcp open  blackice-icecap

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

```bash
[host]$ lsof -n -i4TCP:8081
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
ssh     9109 [host]    5u  IPv4  77636      0t0  TCP 127.0.0.1:tproxy (LISTEN)
```

```bash
[host]$ curl http://localhost:8081  -v
*   Trying 127.0.0.1:8081...
* Connected to localhost (127.0.0.1) port 8081 (#0)
> GET / HTTP/1.1
> Host: localhost:8081
> User-Agent: curl/7.79.1
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Transfer-Encoding: chunked
...
```
