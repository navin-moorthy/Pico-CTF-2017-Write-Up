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
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExODkxMDQyMzZdfQ==
-->