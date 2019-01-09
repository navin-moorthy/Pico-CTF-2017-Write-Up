# PicoCTF_2017: Tutorial 1

**Category:**  Tutorial  **Points:**  0  **Description:**

> How can you figure out Robin Morris's middle name? Thankfully you have a  [list](https://github.com/LFlare/picoctf_2017_writeup/blob/master/tutorial/tutorial_1/contractors.txt)  you can check!

**Hint:**

> Please don't search by hand!

## [](https://github.com/LFlare/picoctf_2017_writeup/tree/master/tutorial/tutorial_1#write-up)Write-up

A very simple  `grep`.

```
$ grep -E "^Robin (.*) Morris$" contractors.txt 
Robin Almay Morris

```

Therefore, the flag is,  `Almay`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTIzNzAyODc1Nl19
-->