# PicoCTF_2017: My First SQL

**Category:** Web Exploitation
**Points:** 50
**Description:**

>I really need access to [website](http://shell2017.picoctf.com:14356/), but I forgot my password and there is no reset. Can you help?

**Hint:**

>Have you heard about SQL injection?

## Write-up
A simple SQL injection, just try to login to an admin account. As most SQL injection flaws comes from unescaped queries, a universal query such as `' or 1=1 --` works, everytime.

Therefore the flag is `be_careful_what_you_let_people_ask_6a69724d2883354dfb842c71fd992d87ca3db5eae25f146a5418c4365b0b5540`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDQ2OTM1MDFdfQ==
-->