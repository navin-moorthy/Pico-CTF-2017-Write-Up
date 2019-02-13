# PicoCTF_2017: MIPS

**Category:**
**Points:**
**Description:**

>

**Hint:**

>

## Write-up
Thanks to https://blog.icec.tf/mips-picoctf-2017/ for the writeup hint.

We followed the recommendation we were given in the hints and used the  [SPIM](https://blog.icec.tf/mips-picoctf-2017/)simulator

### Setting Up SPIM

As an Arch Linux user, I started looking in the Arch User Repository (AUR) to see if they had  `spim`. They did, so I installed it from there and explored the options of  `spim`

```
Usage: spim  
    -bare           Bare machine (no pseudo-ops, delayed branches and loads)
    -asm            Extended machine (pseudo-ops, no delayed branches and loads) (default)
    -delayed_branches   Execute delayed branches
    -delayed_loads      Execute delayed loads
    -exception      Load exception handler (default)
    -noexception        Do not load exception handler
    -exception_file <file>  Specify exception handler in place of default
    -quiet          Do not print warnings
    -noquiet        Print warnings (default)
    -mapped_io      Enable memory-mapped IO
    -nomapped_io        Do not enable memory-mapped IO (default)
    -file <file> <args> Assembly code file and arguments to program

```

The hint told us that we needed some options for it to work properly. There were only a couple of options that actually changed how the program would get executed and they were  `-delayed_branches`,  `-bare`  and  `-delayed_loads`. In the second hint we were given that the  `R2000 architecture`  uses branch-delay slots, so let's try to run it with the  `-delayed branches`  option and define the mips file we were given with the  `-file`  option

```
spim -file mips.txt -delayed_branches  

```

It didn't matter what I typed, the program never returned anything. This was peculiar, so I checked what version I had installed  `SPIM Version 8.0 of January 8, 2010`. Turns out to be a rather old version.

I decided to install the most recent version from their  [sourceforge page](https://sourceforge.net/projects/spimsimulator/). Now when I typed something I finially got a response

```
$ spim -file mips.txt -delayed_branches
1234  
Not quite! Keep trying.  

```

Apperantly our input was not correct, so I'm guessing that we're supposed to find for what input it tells us it's the correct input. Judging from the description where it tells us to give the flag in the form of a hexadecimal number I assume the input needs to be a number.

In order to reverse engineer this it would be very helpful to be able to step through these instructions since none of our team members had prolific MIPS experience. This is where  `QTSPIM`  comes in.

##### QTSPIM

`spim`  has a graphical user interface where you can step through the simulator and see what's in each of the registers.![qtspim](https://i.imgur.com/bPsQE6d.png)I had some issues with it where it would only run one simulation and I would be unable to run it again. In those situations I managed to resolve it by clearing all of the registers before running it again. There's a button to clear the registers on the top bar, it looks like a recycle icon.

### Delayed Branching

One of the hints tells us this architecture uses delayed branching, but we weren't sure what this meant at first. We did notice some strange behaviour around the branches when stepping through the given MIPS program in QTSPIM.

We found a great  [resource](https://www.cs.uaf.edu/2000/fall/cs301/notes/Chapter13/node4.html)  which manages to explain delayed branching really well.

```
A technique for minimizing the effect of control dependencies is to separate the point where the branch operation takes effect from the branch tests. The branch instruction performs a test on a branch condition. If the test succeeds, the PC is modified, but the modification does not take effect immediately. This delayed branch allows one or more instructions following the branch to be executed in the pipeline whether the branch is taken or not.  

```

This explains the weird behaviour we were seeing in  `QtSpim`, it executes the instruction after the branch regardless of the outcome of the branch.

### MIPS to C

At this point we had tried reversing the MIPS program by staring with minimum progress (but we learned MISP :D). We also tried to bruteforce the input by starting a new  `spim`  process each time, but it was proving to be too slow. We decided to move the MIPS instruction into C so we can try different inputs quicker.

We translated the MIPS instructions its C equivalent while making sure we account for the branch delay we just learned about.

You can see the comment above each line of C code is the MIPS instruction which was translated.

```c
#include <stdio.h>
#include <limits.h>
int check(const int inp) {  
  int t1, t2, t3, t4, t5, t6, t7, t8, t9, t10, t11, t12, t13, t14, t15, t16;
  t1 = inp;
  t4 = t1;
  // li      $6,-16777216       # 0xffffffffff000000
  t6 = -16777216;
  // and     $6,$4,$6
  t6 = t4 & t6;
  // li      $16,16711680       # 0xff0000
  t16 = 16711680;
  // and     $16,$4,$16
  t16 = t4 & t16;
  // andi    $7,$4,0xff00
  t7 = t4 & 0xff00;
  // andi    $4,$4,0xff
  t4 = t4 & 0xff;
  // sra     $3,$6,24
  t3 = t6 >> 24;
  // move    $2,$0
  t2 = 0;
L3:  
  // slt     $5,$2,13
  t5 = t2 < 13 ? 1 : 0;
  // addiu   $2,$2,1
  t2 = t2 + 1;
  // beq     $5,$0,$L2
  if (t5 == 0) goto L2;
  // addiu   $3,$3,-13
  t3 = t3 + -13;
  // b       $L3
  goto L3;
L2:  
  // addiu   $3,$3,-6
  t3 = t3 + -6;
  // sll     $5,$3,24
  t5 = t3 << 24;
  // sra     $16,$16,16
  t16 = t16 >> 16;
  // addiu   $2,$16,-81
  t2 = t16 + -81;
  // sll     $8,$2,6
  t8 = t2 << 6;
  // sll     $3,$2,8
  t3 = t2 << 8;
  // subu    $3,$3,$8
  t3 = t3 - t8;
  // subu    $3,$2,$3
  t3 = t2 - t3;
  // sra     $7,$7,8
  t7 = t7 >> 8;
  // sll     $2,$4,1
  t2 = t4 << 1;
  // addiu   $2,$2,3
  t2 = t2 + 3;
  // sll     $3,$3,16
  t3 = t3 << 16;
  // bne     $7,$2,$L7
  if (t7 != t2) goto L7;
  // li      $2,94
  t2 = 94;
  // b       $L4
  goto L4;
L7:  
  // li      $2,165
  t2 = 165;
L4:  
  // addiu   $2,$2,-94
  t2 = t2 + -94;
  // sll     $2,$2,8
  t2 = t2 << 8;
  // srl     $6,$6,24
  t6 = (int)(((unsigned int)t6) >> 24);
  // subu    $16,$6,$16
  t16 = t6 - t16;
  // subu    $4,$4,$16
  t4 = t4 - t16;
  // addu    $3,$5,$3
  t3 = t5 + t3;
  // addu    $3,$2,$3
  t3 = t2 + t3;
  // addu    $16,$4,$3
  t16 = t4 + t3;
  // bne     $16,$0,$L5
  /* if t16 != 0 fail */
  /* success */
    return t16 == 0;
}

```

You know that you have the correct input when register 16 becomes  `0`  at the end of execution. The check function runs through all of the instructions and makes sure that this constraint is satisied.

We now need to figure out which input gives us  `0`  in register 16. For that we brute force from  `INT_MIN`  up to  `INT_MAX`, because the MIPS program wants a number and it can only handle 32 bit numbers.  
If the  `check()`  function returns true, that means that we have found our input.

```
int main() {  
  for (int i = INT_MIN; i<=INT_MAX; i++ ) {
    if (check(i)) {
      printf("Good job! 0x%x\n",i);
      break;
    }
  }
}

```

### Brute Forcing

Now to run our brute force program.

```
$ time ./mips
Good job! 0xaf51bf5e  
./mips  33.26s user 0.01s system 99% cpu 33.391 total

```

We manage to guess the right input in only 30 seconds, not too bad!  
flag: 0xaf51bf5e
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUxNTc4NzU2Nl19
-->