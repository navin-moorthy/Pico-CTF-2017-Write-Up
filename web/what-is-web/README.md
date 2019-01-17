# PicoCTF_2017: What Is Web

**Category:** Web Exploitation
**Points:** 20
**Description:**

>Someone told me that some guy came up with the "World Wide Web", using "HTML" and "stuff". Can you help me figure out what that is? [Website](http://shell2017.picoctf.com:4443/).

**Hint:**

>How can you figure out how the webpage is actually built?

## Write-up
Viewing the [page source](http://shell2017.picoctf.com:4443/index.html) gives us `The first part of the flag (there are 3 parts) is 72b28b258d2`

Viewing the [CSS source code](http://shell2017.picoctf.com:4443/hacker.css) gives us  `The second part of the flag is b2ea021486f` 

Viewing the [JS source code](http://shell2017.picoctf.com:4443/script.js) gives us `The final part of the flag is ddd5020451d`.

Therefore, the flag is `72b28b258d2b2ea021486fddd5020451d`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTgxNzAxNTY0LC0xOTY4MzM5NjQxXX0=
-->