# PicoCTF_2017: Tutorial 1

**Category:** Tutorial

**Points:** 0

**Description:**

>How can you figure out Robin Morris's middle name? Thankfully you have a [list](contractors.txt) you can check!

**Hint:**

>Please don't search by hand!

## Write-up

A very simple `grep`.

    $ grep -E "^Robin (.*) Morris$" contractors.txt 
    Robin Almay Morris

Therefore, the flag is, `Almay`.

## Explanation

**grep** - print lines matching a pattern

**-E** - "--extended-regexp" -  Interpret PATTERN as an extended regular expression.

- Learn or brush up your Regular Expression Skills 
- Usefull site : https://regex101.com/
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTk1ODcxNTA2OSwtMTI1MTU1MjgxOSwtMT
c2OTk2Nzc3MV19
-->