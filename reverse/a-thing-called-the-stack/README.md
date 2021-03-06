# PicoCTF_2017: A Thing Called The Stack

**Category:** Reverse Engineering
**Points:** 60
**Description:**

>A friend was stacking dinner plates, and handed you [this](assembly.s), saying something about a "stack". Can you find the difference between the value of esp at the end of the code, and the location of the saved return address? Assume a 32 bit system. Submit the answer as a hexidecimal number, with no extraneous 0s. For example, the decimal number 2015 would be submitted as 0x7df, not 0x000007df

**Hint:**

>Where is the return address saved on the stack?
Which commands actually affect the stack?

## Write-up

This is a trivial reading-and-understanding assembly. The code that was given is conveniently printed as below:
```
foo:
    pushl %ebp
    mov %esp, %ebp
    pushl %edi
    pushl %esi
    pushl %ebx
    sub $0x124, %esp
    movl $0x1, (%esp)
    movl $0x2, 0x4(%esp)
    movl $0x3, 0x8(%esp)
    movl $0x4, 0xc(%esp
```
A quick note for this assembly is that it is AT&T flavor.

### Background Information

In program terms, a stack refers to a chunk of memory that stores local variables in functions and stored data. Stored data, in this case would include your return address. When you  **push**  to a stack, you add data onto the top of the chunk of memory. When you  **pop**  from the stack, you take off the data that is at the top of this data chunk, or the data you last pushed onto the stack. This is defined as LIFO (Last In First Out). A interesting bit to note in a stack is the fact that when you push to the stack, the address to the top of the stack  _decreases_  instead of increase.

A function that you call typically has two properties, a  **stack pointer**  (top of the stack) and a  **frame pointer**  (bottom of the stack relative to the function). The  `ebp`  register stores the frame pointer (also known as the  **base pointer**) and the  `esp`  register stores the  **stack pointer**.

### Deciphering the Assembly Code

When a code calls a function, the address of the code's next instruction is implicitly pushed onto the stack (also known as the  **return address**).

The first few instructions will prepare our new stack and frame pointer relative to the function. We also would want to save the previous function's frame pointer so that later on we can return to it via a  `pop`:

pushl %ebp
mov %esp, %ebp

In AT&T flavor assembly, the  `mov`  instruction is an assignment operator, except the operands are flipped as compared to an assignment  `=`  operator. In C terms, it would mean  `%ebp = %esp`. (Note that in Intel flavor, the operands are reverse to AT&T). The  `pushl`  would add 4 bytes to  `%esp`, but the  `mov`  does nothing to modify  `%esp`.

> Return address location -  `esp`Stacks are a common thing in the computerverse, simply because it's a highly efficient way of storing data by appending. To solve this challenge, you need to visualize the stack after the assembly code.

    [stack]
    ebp:        [old ebp]
    ebp-4:      [ebp]
    ebp-8:  =  **4 bytes**
> 
> **Note that return address location is greater than  `%esp`  because lower addresses of stack means values closer to the top of the stack.

In this function we also push some other register values, each incrementing  `%esp`  by 4 bytes:
```
pushl %edi
pushl %esi
pushl %ebx
```
Certain registers can be  **volatile**, meaning, we could change these registers without having to revert back. For example  `eax`and  `edx`  are such registers. However, others are  **non-volatile**. We should keep these values the same throughout our code, but if we have to modify the values, we would first have to push to stack so that we could revert these register values later on. This code is doing such.

> Return address location -  `%esp`  =  **16 bytes**

Next, we subtract  `%esp`  by a value:

`sub $0x124, %esp ; 0x124 = 292`

This is typically done to make room for local temp variables. (i.e. when a function declares local variables, they are found right here in the stack). In this case, we seem to need 292 or  [edi]
    ebp-12:     [esi]
    ebp-16:     [ebx]
    ebp-264:    0x1     <- esp
    ebp-268:    0x2
    ebp-272:   ` 0x124`  bytes of stack space to store local variables.

> Return address location -  `%esp`  =  **308 bytes**

Finally we make some assignments to some of our local variables:
```
movl $0x1, (%esp)
movl $0x2,  0x4(%esp)
movl $0x3,  0x8(%esp)
movl $0x4,  0xc(%esp)
```
First notation to notice is that there are parenthesis around the  `%esp`  register. As we discussed, %esp refers to a stack  _pointer_. In C, pointers are used to refer to something else. In this case  `%esp`  refers to the address of the top of the stack, but  `(%esp)`refers to the  _value_  at the top of the stack. The instructions  `mov3
    ebp-276:    0x4

It so happens that we only need to find the difference, which is `264`  or assign constants (denoted with a  `$`) into the variables located in the stack. The first one refers to the topmost value on the stack. The second one does an add offset to  `%esp`  so that we refer to the second value on the top of the stack. The third one refers to the third topmost value, the forth the forth topmost value. In all these assignments, the  `%esp`  register doesn't actually change at all.

> Return address location -  `%esp`  =  **308 bytes**

We see that the difference between the  `%esp`  register and the return address is `308 bytes`. When we convert it into hexadecimal, we get  `0x134`, which is our answer. QED!`0x108` in hexadecimal.

Therefore, the flag is `0x108`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTczOTgzMzQ5NCwxMzk1NDEwMTk4LDgzOT
A3MjcxOSwtMTUxNzIyMTI0NCwxOTUxMTUyMTA2XX0=
-->