# PicoCTF_2017: Aggregator

**Category:** Binary
**Points:** 190
**Description:**

I wrote a program to help me synthesize my log files. You can edit the log files to add annotations that will print out aggregated information. Because the original program that outputs the logs was a bit faulty, I also included the ability to clear a single day of logs.  
Find my aggregator running on shell2017.picoctf.com:7785. [aggregator.c](https://webshell2017.picoctf.com/static/2745e04028ecf1c8ae15c70bb085f5e6/aggregator.c)  [aggregator](https://webshell2017.picoctf.com/static/2745e04028ecf1c8ae15c70bb085f5e6/aggregator)

**Hint:**

The buffer isn't on the stack, so you can't use your own pointers. Is there another way to get a pointer?

Make sure you know about using hn and $ in format strings

## Write-up
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIxMjQ2NDAwNjldfQ==
-->