# Warmup RSA

The **Warmup RSA** challenge is designed to test participants' understand of RSA encryption and prime factorization. The challenge is based on an RSA encryption system where the private key is derived from the knowledge of the prime factors of the modulus $n$. Here's a breakdown of how the challenge is structured and how to solve it.

## Challenge Overview

The RSA cryptosystem in this challenge uses a modulus $n = p \times q$, where $p$ and $q$ are large prime numbers. The encryption exponent $r = 65537$ is commonly used in RSA. The flag is encrypted using the public key and the ciphertext is given as $c$. Our goal is to decrypt the ciphertext and recover the flag.

The key generation process is as follows:
1. Generate two primes, $p$ and $q$. 
2. Compute $n = p \times q$,
3. Choose $e = 65537$ (a standard choice for RSA encryption).
4. Encrypt the flag $m$ (converted to long integer using `bytes_to_long`).

The provided ciphertext $c$ and modulus $n$ are given as part of the challenge.

## Problem Breakdown
The challenge involves factoring the modulus $n$ to obtain the primes $p$ and $q$. Once we have $p$ and $q$, we calculate $\phi(n) = (p - 1)(q - 1)$, which is necessary for finding the private key $d$. After calculating $d$, we can decrypt the ciphertext $c$ using the formula:
$$
m = c^d \text{ mod } n
$$
The prime factors $p$ and $q$ are not directly given, but are constructed using a particular formula:
$$
q = p + 42b^{\frac{\text{bitsize}}{12}}+ 17b^{\frac{\text{bitsize}}{24}} + 42b + 1337
$$
Where $b$ is a prime number of size 12 bits.

## Key Insights for Solving the Challenge

- We know that the prime $b$ used in the key generation is smaller (a 12-bit prime).
- The different between $q$ and $p$ is a function of $b$, given by the equation:
$$
\text{ diff } = 42b^{32} + 17b^{16} + 42b + 1337
$$
- Once we compute this difference, we can set up a quadratic equation to solve for $p$:
$$
p^2 + p \cdot \text{diff} - n = 0
$$
- Solving this quadratic equation gives us potential values for $p$. If $p$ is valid (i.e., $p$ and $q$ = $n / p$ are prime), we can proceed with decryption.

## Solution Approach
1. **Factorization:**
- Iterate over all possible values of $b$ that are prime and check the condition for $q - p$ using the provided formula.
- For each prime $b$, calculate the different $\text{diff}$ and the solve the quadratic equation for $p$.
2. **Decryption:**
- Once we obtain valid values for $p$ and $q$, compute $\phi (n) = (p - 1)(q - 1)$.
- Compute the modular inverse of $e$ modulo $\phi(n)$ to get the private key $d$.
- Use the private key $d$ to decrypt the ciphertext $c$ and recover the message $m$, which is the flag.

## Solution Code

```python
from Crypto.Util.number import isPrime, long_to_bytes
from math import isqrt

def solve_quadratic(a, b, c):
    discriminant = b*b - 4*a*c
    if discriminant < 0:
        return None
    root = isqrt(discriminant)
    if root * root != discriminant:
        return None
    sol1 = (-b + root)//(2*a)
    sol2 = (-b - root)//(2*a)
    return sol1, sol2

n = 729873623543656312134307118912665256781357935692626903169156150433937519752782083907499829904547040251053110020584673588552287964181185247366145284580956529366829912211930759566764271125168397271330673982376560489636282781549333563
c = 55998184521830999281817039472301544636818114568295094941847806390463936789530964194822878197897166490112675455011157083179676040236474209093809166224001096815812124353799949999248500406044583901981864080093522349557347259218634936
e = 65537

# Try all possible values of b
for b in range(2, 4096):
    if not isPrime(b):
        continue
        
    # Calculate q - p
    diff = 42*(b**32) + 17*(b**16) + 42*b + 1337
    
    # Solve quadratic equation: p^2 + p*diff - n = 0
    roots = solve_quadratic(1, diff, -n)
    if roots is None:
        continue
        
    for p in roots:
        if p <= 0:
            continue
        q = n//p
        if p*q != n:
            continue
        if not (isPrime(p) and isPrime(q)):
            continue
            
        # Found the factors! Now decrypt
        phi = (p-1)*(q-1)
        d = pow(e, -1, phi)
        m = pow(c, d, n)
        flag = long_to_bytes(m)
        print(f"Flag: {flag}")
        exit(0)
```

## Conclusion
The challenge requires knowledge of RSA encryption, prime factorization, and solving quadratic equations. By iterating through possible primes bb, we can factor nn and decrypt the message. This is a great warmup for understanding RSA and cryptography basics.
