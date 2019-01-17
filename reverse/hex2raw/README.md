# PicoCTF_2017: Hex2Raw

**Category:** Reverse Engineering
**Points:** 20
**Description:**

>This program requires some unprintable characters as input... But how do you print unprintable characters? CLI yourself to /problems/9bbf83df34f9c5239f329f977f4a66c1 and turn that Hex2Raw!

**Hint:**

>Google for easy techniques of getting raw output to command line. In this case, you should be looking for an easy solution.

## Write-up

In simple python,

Navigate to `/problems/9bbf83df34f9c5239f329f977f4a66c1` as per the question.

    $ cd /problems/9bbf83df34f9c5239f329f977f4a66c1
    
    $ ls 
	flag hex2raw input
	
	$ ./hex2raw 
	Give me this in raw form (0x41 -> 'A'):
	99d9835d83647dcf4cb202a51e1c29bc

	You gave me:

Now take the hex `99d9835d83647dcf4cb202a51e1c29bc` and decode it.
	
	$ python -c "print('99d9835d83647dcf4cb202a51e1c29bc'.decode('hex'))" 
	ك]d})

Give the output to the script.

	$ python -c "print('99d9835d83647dcf4cb202a51e1c29bc'.decode('hex'))" | ./hex2raw
	Give me this in raw form (0x41 -> 'A'):
	99d9835d83647dcf4cb202a51e1c29bc

	You gave me:
	99d9835d83647dcf4cb202a51e1c29bc
	Yay! That's what I wanted! Here be the flag:
	84b0a5a216620b05b1cae86508dad925
	
Therefore, the flag is `84b0a5a216620b05b1cae86508dad925`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzU5NjA2MzQ2LDE2NDE1NDU2NDcsMTQxNz
E4NzA1NywtODcyNDcwNTAwXX0=
-->