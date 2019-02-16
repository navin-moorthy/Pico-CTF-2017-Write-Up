# PicoCTF_2017: weirderRSA

**Category:** Master
**Points:** 175
**Description:**

>Another message encrypted with RSA. It looks like some parameters are missing. Can you still decrypt it? [Message](clue.txt)

**Hint:**

>Is there some way to create a multiple of p given the values you have?
Fermat's Little Theorem may be helpful

## Write-up

The first thing I will try to do is solve for p. Because we are given n, computing q after getting p will be simple. With both q and p, the rest of the problem is relatively trivial.

From previous questions, we knew that dp was d%(p-1), where % is the modulus function. (NOTE: dp is NOT d*p. For multiplication, I will explicitly write “*”.)

It then follows that d < p-1.

We also know that d*e = 1%(p-1). (This is simply writing down the modular multiplicative inverse part of RSA)

d*e = 1%(p-1) is the same thing as saying ((d%(p-1))*(e%(p-1)))%(p-1) = 1%(p-1).

Then by the definition of modulus, we get: dp*e = k(p-1)+1, where k is some unknown integer. If this is not obvious, just think it through for a moment.

Because dp<p-1, it follows that k≤e.

Because e is a relatively small number, it is easy to simply try all the values of k from 1 to e until we can find a corresponding integer value of p that will satisfy dp*e = k(p-1)+1.

So I wrote a python script that went something like this (modular inverse function and some obvious variables not included for sake of legibility):

l = dp * e - 1  
p = 0  
for k in range(1, e):  
    if l%k == 0:  
        p = (l//k + 1)  
        if n%p == 0:  
            q = n//p  
            t = (p-1)*(q-1)  
            print(pow(c,modinv(e,t),n))

In that above script, I went ahead and tried deciphering the ciphertext c in the loop as well. It only took a few seconds to run, and voila! We got our plaintext number:

3670434958110785066911905751469631231338751225710158680692616521935747246580688484040488309932916523151997

Converting this to ASCII, we get:

flag{wow_leaking_dp_breaks_rsa?_98924743502}

Done! Honestly, trying to fit the hint into the problem was harder than just doing it this way.

Thanks to [soup](https://medium.com/@nicebowlofsoup) for the writeup help.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE3Mjk3MzMzM119
-->