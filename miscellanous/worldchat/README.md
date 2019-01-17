# PicoCTF_2017: WorldChat

**Category:** Miscellanous
**Points:** 30
**Description:**

>We think someone is trying to transmit a flag over WorldChat. Unfortunately, there are so many other people talking that we can't really keep track of what is going on! Go see if you can find the messenger at shell2017.picoctf.com:7911. Remember to use Ctrl-C to cut the connection if it overwhelms you!

**Hint:**

>There are cool command line tools that can filter out lines with specific keywords in them. Check out 'grep'! You can use the '|' character to put all the output into another process or command (like the grep process)

## Write-up
Bash piping~

    $ nc shell2017.picoctf.com 7911 | grep "flag"
	15:22:33 noihazflag: my parents , in my opinion, are our best chance for the future of humanity
	15:22:33 ihazflag: Hungry jackolanterns will never be able for what, I do not know
	15:22:33 ihazflag: Cats with hats , in my well-educated opinion, are our best chance to help me spell 'raspberry' correctly
	15:22:34 ihazflag: A small moose totally understands me and my pet sloth to generate fusion power
	15:22:34 flagperson: this is part 1/8 of the flag - 08bc
	15:22:34 whatisflag: Only us want to see me to generate fusion power
	15:22:35 ihazflag: a heavily bearded dolphin wants to see me to drink your milkshake
	15:22:35 whatisflag: Hungry jackolanterns , in my opinion, are our best chance 	for the future of humanity
	15:22:35 personwithflag: We , in my well-educated opinion, are our best chance to generate fusion power
	15:22:35 noihazflag: my homegirlz will never be able to understand me
	15:22:36 flagperson: this is part 2/8 of the flag - 0aa6


We seem to spot a common pattern `this is part 1/8 of the flag - 08bc`, let's try regexp!

    $ nc shell2017.picoctf.com 11511 | grep -Eo "this is part [0-9]\/8 of the flag - [a-z0-9]{4}"
    this is part 1/8 of the flag - 8d84
    this is part 2/8 of the flag - 913f
    this is part 3/8 of the flag - 84bd
    this is part 4/8 of the flag - 68a4
    this is part 5/8 of the flag - 6576
    this is part 6/8 of the flag - 3e48
    this is part 7/8 of the flag - d9d9
    this is part 8/8 of the flag - ca1c

Therefore, the flag is `8d84913f84bd68a465763e48d9d9ca1c`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0OTgyOTU5NjIsLTEzNDkwODU4NDRdfQ
==
-->