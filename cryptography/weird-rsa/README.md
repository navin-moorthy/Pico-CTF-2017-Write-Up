# PicoCTF_2017: Weird RSA

**Category:** Cryptography
**Points:** 90
**Description:**

>We recovered some [data](RSA.txt). It was labeled as RSA, but what in the world are "dq" and "dp"? Can you decrypt the ciphertext for us?

**Hint:**

>

## Write-up

We recovered some data. It was labeled as RSA, but what in the world are "dq" and "dp"? Can you decrypt the ciphertext for us?

```text
c: 95272795986475189505518980251137003509292621140166383887854853863720692420204142448424074834657149326853553097626486371206617513769930277580823116437975487148956107509247564965652417450550680181691869432067892028368985007229633943149091684419834136214793476910417359537696632874045272326665036717324623992885
p: 11387480584909854985125335848240384226653929942757756384489381242206157197986555243995335158328781970310603060671486688856263776452654268043936036556215243
q: 12972222875218086547425818961477257915105515705982283726851833508079600460542479267972050216838604649742870515200462359007315431848784163790312424462439629
dp: 8191957726161111880866028229950166742224147653136894248088678244548815086744810656765529876284622829884409590596114090872889522887052772791407131880103961
dq: 3570695757580148093370242608506191464756425954703930236924583065811730548932270595568088372441809535917032142349986828862994856575730078580414026791444659
```

Firstly let's take a quick look at the  [Wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem))  page about RSA cryptosystem. We can easily see that values named as  **dp**  and  **dq**  are used in the Chinese Remainder algorithm, which is used as the algorithm in many popular crypto libraries, due to it's efficiency. Let's view the equations.

```text
dP = (1/e) mod (p-1)
dQ = (1/e) mod (q-1)
qInv = (1/q) mod p

m1 = c^dP mod p
m2 = c^dQ mod q
h = qInv * (m1 - m2) mod p
m = m2 + h * q
```

Great, we have everything right in the provided data. The only thing that concernes me is the  **qInv**  variable, which proved by this  [paper](http://www.di-mgt.com.au/crt_rsa.html)  states to be modular inverse, and can be calculated using  [Euclidean Algorithm](http://www.di-mgt.com.au/euclidean.html).

Quick google for the Python code calculating modular inverse, gives us this code.

```python
def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, y, x = egcd(b % a, a)
        return (g, x - (b // a) * y, y)

def modinv(a, m):
    g, x, y = egcd(a, m)
    if g != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m
```

_Thanks  [Märt](http://stackoverflow.com/questions/4798654/modular-multiplicative-inverse-function-in-python)!_

Let's load the variables into the Python console.

```python
w3ndige@W3ndige ~> python
Python 3.6.0 (default, Jan 16 2017, 12:12:55)
[GCC 6.3.1 20170109] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> c = 95272795986475189505518980251137003509292621140166383887854853863720692420204142448424074834657149326853553097626486371206617513769930277580823116437975487148956107509247564965652417450550680181691869432067892028368985007229633943149091684419834136214793476910417359537696632874045272326665036717324623992885
>>> p = 11387480584909854985125335848240384226653929942757756384489381242206157197986555243995335158328781970310603060671486688856263776452654268043936036556215243
>>> q = 12972222875218086547425818961477257915105515705982283726851833508079600460542479267972050216838604649742870515200462359007315431848784163790312424462439629
>>> dp = 8191957726161111880866028229950166742224147653136894248088678244548815086744810656765529876284622829884409590596114090872889522887052772791407131880103961
>>> dq = 3570695757580148093370242608506191464756425954703930236924583065811730548932270595568088372441809535917032142349986828862994856575730078580414026791444659
```

Now we can add the modular inverse code, and calculate all the missing parts. We can also use Python's pow which will speed up the calculations as stated in the documentation:

_**pow(x, y[, z])**  - return x to the power y; if z is present, return x to the power y, modulo z (computed more efficiently than pow(x, y) % z)._

```python
>>> def egcd(a, b):
...     if a == 0:
...         return (b, 0, 1)
...     else:
...         g, y, x = egcd(b % a, a)
...         return (g, x - (b // a) * y, y)
...
>>> def modinv(a, m):
...     g, x, y = egcd(a, m)
...     if g != 1:
...         raise Exception('modular inverse does not exist')
...     else:
...         return x % m
...
>>> qinv = modinv(q, p)
>>> m1 = pow(c, dp, p)
>>> m2 = pow(c, dq, q)
>>> h = (qinv * (m1 - m2)) % p
>>> m = m2 + h * q
>>> print(m)
149135670063399521704490559489300383724787675131107361956792519054534529857
```

We have the message. Last thing will be to turn it into a hexadecimal number, and calculate the ASCII characters, as we know we have to decrypt the ciphertext.

```python
>>> hex(m)
'0x5468657265735f6d6f72655f7468616e5f6f6e655f7761795f746f5f525341'
>>> txt = '5468657265735f6d6f72655f7468616e5f6f6e655f7761795f746f5f525341'
>>> ''.join([chr(int(''.join(c), 16)) for c in zip(txt[0::2],txt[1::2])])
'Theres_more_than_one_way_to_RSA'
```

Thanks  [Andrew](http://stackoverflow.com/questions/9641440/convert-from-ascii-string-encoded-in-hex-to-plain-ascii)  for this snippet. Here's the flag!
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg2NjAzNjc2OF19
-->