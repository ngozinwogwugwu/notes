# Intro to RSA
[RSA](https://simple.wikipedia.org/wiki/RSA_algorithm) (named after its inventors) is an asymmetric encryption algorithm.
- **public key**: encrypts messages. It includes *e* and *n*, where *e* is the *encryption exponent*
- **private key**: decrypts messages. It includes *d* and *n*, where *d* is the *decryption exponent*
- *n* here is the *modulus*

So basically, you generate an RSA key pair and give out your public key. Whoever you gave your public key to can use it to encrypt a message and send it to you. You use your private key to decrypt the message, and since the algorithm is asymmetric, no one can use the public key to decrypt messages.

Here are the terms we'll use here:
- *C* = encrypted message
- *m* = original message
- *e* = encryption exponent
- *d* = decryption exponent
- *n* = modulus
- *p* and *q* = two distinct primes
- *T* = Totient

## Important Ideas
- **Relatively Prime**: A pair of numbers is relatively prime if they don't share any factors
- **Totient**: Number of ints (between 1 and *n*) that are relatively prime with *n*
- **Congruence**: If you can divide the difference of two numbers by another number and get an integer as an answer, they're congruent ([here's a better explaination](http://mathworld.wolfram.com/Congruence.html))

## Generating Public/Private key pairs
- **step 1**: generate two distinct primes, *p* and *q*
- **step 2**: let *n = pq*
- **setp 3**: Calculate the Totient, *T = (p-1)(q-1)*
- **step 4**: create a public key
  - find an *encryption exponent*, *e*. *e* is *relatively prime* and is less than *T*
  - The public key is (*e*, *n*)
- **step 5**: create a private key
  - compute a *decryption exponent*, *d*, that satisfies a *congruence relation*
  - *de* is congruent to *1 (mod T)*, or *(de) % T = 1*
  - The private key is (*d*, *n*)

## Encryption
*C = (m^e) % n*

## Decryption
*m = (C^d) % n*