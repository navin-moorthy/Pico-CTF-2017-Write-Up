# PicoCTF_2017: Hex2Raw

**Category:** Reverse Engineering
**Points:** 20
**Description:**

>This program requires some unprintable characters as input... But how do you print unprintable characters? CLI yourself to /problems/9bbf83df34f9c5239f329f977f4a66c1 and turn that Hex2Raw!

**Hint:**

>Google for easy techniques of getting raw output to command line. In this case, you should be looking for an easy solution.

## Write-up

In simple python,

Navigate to 
    $ cd /problems/9bbf83df34f9c5239f329f977f4a66c1
    
    $ ls 
	flag hex2raw input
	
	$ ./hex2raw 
	Give me this in raw form (0x41 -> 'A'):
	99d9835d83647dcf4cb202a51e1c29bc

	You gave me:

Now take the hex and decode it.
	
	$ python -c "print('416f1c7918f83a4f1922d86df5e78348'.decode('hex'))" 
	Aoy:O"

	$ python -c "print('416f1c7918f83a4f1922d86df5e78348'.decode('hex'))" | ./hex2raw
	
	Give me this in raw form (0x41 -> 'A'):
	416f1c7918f83a4f1922d86df5e78348

	You gave me:
	416f1c7918f83a4f1922d86df5e78348
	
	Yay! That's what I wanted! Here be the flag:
	1d2411efe307f5ac07bd28bbabb5769e

Therefore, the flag is `1d2411efe307f5ac07bd28bbabb5769e`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkzOTg0MzUxMywxNjQxNTQ1NjQ3LDE0MT
cxODcwNTcsLTg3MjQ3MDUwMF19
-->