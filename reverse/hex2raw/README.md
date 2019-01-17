# PicoCTF_2017: Hex2Raw

**Category:** Reverse Engineering
**Points:** 20
**Description:**

>This program requires some unprintable characters as input... But how do you print unprintable characters? CLI yourself to /problems/33432c6de9329bca3a3ff26e5538d8f2 and turn that Hex2Raw!

**Hint:**

>Google for easy techniques of getting raw output to command line. In this case, you should be looking for an easy solution.

## Write-up

In simple python,

    $cd /problems/33432c6de9329bca3a3ff26e5538d8f2
    
    $ ls 
	flag hex2raw input
	
	$ python -c "print('416f1c7918f83a4f1922d86df5e78348'.decode('hex'))" | ./hex2raw
	
	Give me this in raw form (0x41 -> 'A'):
	416f1c7918f83a4f1922d86df5e78348

	You gave me:
	416f1c7918f83a4f1922d86df5e78348
	
	Yay! That's what I wanted! Here be the flag:
	1d2411efe307f5ac07bd28bbabb5769e

Therefore, the flag is `1d2411efe307f5ac07bd28bbabb5769e`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQxNzE4NzA1NywtODcyNDcwNTAwXX0=
-->