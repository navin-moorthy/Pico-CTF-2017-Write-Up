## ECC 2 - 200 (Cryptography)

#### Thanks and Writen over the pwang00 (Sanguinius) writeup from [Hacademia](https://hgarrereyn.gitbooks.io/th3g3ntl3man-ctf-writeups/2017/picoCTF_2017/problems/cryptography/ECC2/ECC2.html)

### Problem

More elliptic curve cryptography fun for everyone!  [handout.txt](https://webshell2017.picoctf.com/static/3a2d620286b527a0c022e8726160c907/handout.txt)  (Yes, the flag will just be the number n.)

### Hint

Using SageMath (or something similar which supports working with elliptic curves) will be very helpful.

## Solution

### Overview

Use the Pohlig-Hellman algorithm to calculate Elliptic Curve Discrete Logarithm and recover scalar  nnused to arrive at point  QQ  (n*P)

### Details

We are given the following handout:

```
Elliptic Curve: y^2 = x^3 + A*x + B mod M
M = 93556643250795678718734474880013829509320385402690660619699653921022012489089
A = 66001598144012865876674115570268990806314506711104521036747533612798434904785
B = *You can figure this out with the point below :)*

P = (56027910981442853390816693056740903416379421186644480759538594137486160388926, 65533262933617146434438829354623658858649726233622196512439589744498050226926)
n = *SECRET*
n*P = (23587034938374768786301222539991586253242655515915989431307599794801199763403, 58594847963665471409425852843336835079537055974819057000500246625851308476858)

n < 400000000000000000000000000000

Find n.

```

Our first step will be to solve for  bb  via rearranging the elliptic curve equation:

y2=x3+ax+bmodny2=x3+ax+bmodn

b=y2−x3−axmodnb=y2−x3−axmodn

In Sage, this is done via substituting the  xx  and  yy  values as shown:

```
sage: x, y = P[0], P[1]
sage: b = (y^2 - x^3 - a*x) % N  #The ^ operator is exponentiation instead of XOR in Sage.
sage: print(b)
25255205054024371783896605039267101837972419055969636393425590261926131199030

```

Solving for  bb, we obtain 25255205054024371783896605039267101837972419055969636393425590261926131199030.

We now have all the necessary parameters for the elliptic curve. Thus, our next step is to define the curve over a finite field of integers modulo  MM, and define the points  PP  and  QQ  to be on the curve:

```
F = FiniteField(M)
E = EllipticCurve(F,[A,B])
P = E.point(P)
Q = E.point(Q)

```

Essentially, this challenge requires us to solve the Elliptic Curve Discrete Logarithm Problem (ECDLP): compute the scalar  nn  given the base point  PP  and the product point  QQ  where  Q=nPQ=nP. There are a few known attacks against ECDLP, including the:

1.  MOV attack (involves finding linearly independent points and calculating Weil pairing to reduce ECDLP to over finite field instead of group of points on elliptic curve)
2.  Pohlig-Hellman (reduces discrete logarithm calculations to prime subgroups of the order of P and uses Chinese Remainder Theorem to solve system of congruences for discrete logarithm of the whole order )

We can rule out the MOV attack, since it is infeasible given the size of the elliptic curve order. However, factoring the order of our elliptic curve  EE  reveals many small prime factors, indicating that Pohlig-Hellman could be feasible:

```
sage: factor(E.order())
2^2 * 3 * 5 * 7 * 137 * 593 * 24337 * 25589 * 3637793 * 5733569 * 106831998530025000830453 * 1975901744727669147699767

```

The goal of Pohlig-Hellman and the mathematics leading to it is as follows:

We are attempting to recreate a system of congruences to solve for the value of  ll  (referred to as  nn  in this problem):

l≡l1(modpe11)l≡l2(modpe22)...l≡li(modpeii)l≡l1(modp1e1)l≡l2(modp2e2)...l≡li(modpiei)

where  ll  denotes the discrete logarithm for the order of  PP,  lili  denotes discrete logarithm calculations for each of the smaller prime orders (factors)  pp  of  PP, and  eiei  denotes the exponent of  pp.

First, define an integer  xx  such that  x=pe11pe22...perrx=p1e1p2e2...prer  (in other words,  xx  is equivalent to the order of  PP).

lili  can be written in the form:

li=z0+z1p+z2p2+...+ze−1p−1e−1li=z0+z1p+z2p2+...+ze−1p−1e−1  where  z∈[0,p−1]z∈[0,p−1].

We then define the points  Pi=xpiPPi=xpiP, and  Qi=xpiQQi=xpiQ.

Since we know that the order of  PiPi  is  pipi, we can rewrite the equation as  Qi=lPi=ziPiQi=lPi=ziPi, we can now solve for every  z0...ze−1z0...ze−1  by finding a value for  zizi  such that  Qi=zi∗PiQi=zi∗Pi.

This is comparable to brute force in that if the value of  pp  is small enough, it is feasible to try all the values of  zz  in range of  ee  until a value which satisfies the equation above is found.

In Sage, this can be done as follows:

```
sage: primes = [4, 3, 5, 7, 137, 593, 24337, 25589, 3637793, 5733569, 106831998530025000830453, 1975901744727669147699767]
sage: dlogs = []
sage: for fac in primes:
          t = int(P.order()) / int(fac)
          dlog = discrete_log(t*Q,t*P,operation="+")
          dlogs += [dlog]
          print("factor: "+str(fac)+", Discrete Log: "+str(dlog)) #calculates discrete logarithm for each prime order

factor: 4, dlog: 2
factor: 3, dlog: 1
factor: 5, dlog: 4
factor: 7, dlog: 1
factor: 137, dlog: 129
factor: 593, dlog: 224
factor: 24337, dlog: 5729
factor: 25589, dlog: 13993
factor: 3637793, dlog: 1730599
factor: 5733569, dlog: 4590572

```

You will notice that Sage hangs while computing the discrete logarithms for last 2 prime factors. However, we don't need to know them, since in the problem statement,  ll  was defined to be less than 400000000000000000000000000000. Multiplying every prime order save for the last 2 returns a boundary of 443208349730265573969192476820, meaning that  ll  fits within the bound!

Our final step is just to solve for  ll  via CRT:

```
sage: l = crt(dlogs.primes[:-2])
152977126447386808276536247114

```

which returns the value for  ll!

Just to check the validity of  ll, we perform a quick boolean comparison;

```
sage: l * P == Q
True

```

Thus, our value of  ll  is indeed correct!

### Flag

152977126447386808276536247114

Note: due to random problem generation, your value may be different.

### Full code

```
M = 93556643250795678718734474880013829509320385402690660619699653921022012489089
A = 66001598144012865876674115570268990806314506711104521036747533612798434904785
B = 25255205054024371783896605039267101837972419055969636393425590261926131199030
P = (56027910981442853390816693056740903416379421186644480759538594137486160388926, 65533262933617146434438829354623658858649726233622196512439589744498050226926)
Q = (61124499720410964164289905006830679547191538609778446060514645905829507254103, 2595146854028317060979753545310334521407008629091560515441729386088057610440)
F = FiniteField(M)
E = EllipticCurve(F,[A,B])
P = E.point(P)
Q = E.point(Q)
factors, exponents = zip(*factor(E.order()))
primes = [factors[i] ^ exponents[i] for i in range(len(factors))][:-2]
dlogs = []
for fac in primes:
    t = int(P.order()) / int(fac)
    dlog = discrete_log(t*Q,t*P,operation="+")
    dlogs += [dlog]
    print("factor: "+str(fac)+", Discrete Log: "+str(dlog)) #calculates discrete logarithm for each prime order

l = crt(dlogs,primes)
print(l)

```

### Resources

[http://wstein.org/edu/2010/414/projects/novotney.pdf](http://wstein.org/edu/2010/414/projects/novotney.pdf)[https://koclab.cs.ucsb.edu/teaching/ecc/project/2015Projects/Sommerseth+Hoeiland.pdf](https://koclab.cs.ucsb.edu/teaching/ecc/project/2015Projects/Sommerseth+Hoeiland.pdf)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjEwOTM3NTg0Nl19
-->