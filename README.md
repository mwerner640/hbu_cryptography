# hbu_cryptography

In this section of the workshop we are going to learn:
- Cryptography Introduction
- Stream Ciphers
- Block Ciphers
- Message Integrity
- Collision Resistant Hashing
- Authenticated Encryption
- Basic Key Exchange 
- Trapdoor Permutation
- Diffie-Hellman
- Cryptography with Python

## Cryptography Introduction
### What is cryptography?
Cryptography is a means of securing information to prevent **eavesdropping** and **tampering**. If you're only slightly familar with cryptography, you probably know it is a way of passing secret messages, but we also use cryptography to protect network traffic on the web, over wifi, and through bluetooth; to encrypt files on your disk drive; to access content locked into a specific platform (ie. songs you own on iTunes); and to verify your identity online.

As an example, encrypting communication over a network has two main steps: establishing a shared key using public key cryptography and then transmitting data using that shared secret key. These two steps are the basis of most traditional encryption. Non-traditionally, encryption can also be used to establish a digital signature or to communicate anonymously. 

### Simple Example Ciphers
To start, we'll begin with some ciphers that were used seriously in the past but nowdays with advances in computing are easily broken. You should only use these ciphers recreationally. 

First let's establish some notation. A *symmetric cipher* refers to an crypto system where both the encrypting party and the decrypting party use the same secret key. Our encryption and decryption algprithms are E(k,m) and D(k,c), where k is our key, m is our plaintext message, and c is our ciphertext. E(k,m)=c and D(k,c)=m. To formally define this we say a symmetric cipher is a cipher defined over (K, M, C) is a pair of efficient algorithms (E,D) where E: K x M -> C, D: K x C -> M, such that for every m in M and k in K, D(k, E(k,m)) = m. E is often a randomized function where D is always deterministic. 

So, if Professor Les Lander wants to send a secret message to Professor Eileen Head to tell her the name of his favorite student in CS 140, m = "Bill Gates", he feeds m and the shared secret key into his encryption algorithm E(). This gives him the secret message "dfhyugilg6f", also known as a ciphertext. He then sends his least favorite student to Professor Head with this note, but they can't read it! Only Professor Head can, because she already has the secret key, k, and the decryption algorithm D(). Plugging the key and the ciphertext into D(), she reads the output and knows to watch out for Bill Gates. 

#### Substitution Cipher
A typical substitution cipher has no key. E is a bijective function from the alphabet to the alphabet and D is its inverse. Consider an example where in E: H maps to C, B maps to A, and U maps to T, while in D: C maps to H, A maps to B, and T maps to U. With these functions if we want to send a friend the message "HBU" to remind them to come to hackBU, we'll hand them the ciphertext "CAT".

##### Caesar Cipher
An even easier to crack version of the substitution cipher is the Caesar cipher. In this version our encryption bijection is limited to 26 possible bijections--each of which is merely a shift of the alphabet, keeping the letters in the same order. Our key then, is a value between 0 and 25, and determines how many letters our algorithm shifts each letter in the ciphertext. For example, if k=2, A maps to C, B maps to D, C maps to E, D maps to F,..., Y maps to A, and Z maps to B. What, then, is the ciphertext for our "HBU" message from before with this encryption algorithm and key?

#### Vigener Cipher
The Vigener Cipher is slightly more complicated than a substitution cipher. For it, we need to establish a bijection between the alphabet and [0,25]. Then we take our key and repeat it until it is the length of our message. For example, with the message COMETOHACKBU and a key, KEY, we would expand our key to KEYKEYKEYKEY. Then we use our bijection to map our expanded key and our message into integers. Adding each digit of these integers mod26 and then putting them through the inverse of the bijection produces your ciphertext. To continue our example, we have 2_14_12_4_19_14_7_0_2_10_1_20 and 10_4_24_10_4_24_10_4_24_10_4_24. Added mod26 these digits are 12_18_11_14_23_13_17_4_1_20_5_19, which leads us to the ciphertext MSLOXNHEBUFT. To decrypt that we take it back to numbers, subtract our expanded key digitwise mod26, and then map back to letters.

#### How to Break Any of These Ciphers
We can break these ciphers, along with more complicated outdated ciphers like rotor machines (heard of Enigma?) and DES with a brute force method due to the spead of modern computers. To break them even faster we can consider clues from the ciphertext. As the substitution cipher is a direct bijection, we can use what we know about letter frequency in standard sentences to speed up our search. For example, E is the most common letter in english sentences and Z is not--so the most common letter in our cipher is most likely the image of E and unlikely to be the image of Z. We can also do this looking for common pairs of letters such as TH. We can use this idea with the Vigener cipher to try to identify our key. The most common letter - 4 [E] is most likely the first letter of the key.

### Discrete Probability: Reminders
Let U be a finite set. Then a *probability distribution P over U* is a function P, where U maps to [0,1], such that the summation of P(x) over all x in U is 1. A subset A of U is called an *event* and the probability of A is the summation of P(x) over all x in A. This summation will be an element of [0,1]. A *random variable* is a function X, that maps U to V. X takes values on V and produces a distribution on V.
Events A and B are *independent* if the probability of A and B (and meaning intersection) is the same as the probability of A times the probability of B. Random variables X,Y are independent if for all a,b in V the probability that X=a and Y=b is the same as the probability X=a times the probability that Y=b.

## Stream Ciphers

### One Time Pad
The One Time Pad is the first secure cipher. It can encrypt messages that consist of 0s and 1s into ciphertext that consists of 0s and 1s using a randomized key of 0s and 1s the same length as the message. To calculate the ciphertext we XOR each bit of the message with the corrosponding bit of the key. To decrypt we do the same between the key and the ciphertext. This cipher encodes and decodes very fast, but because it has keys as long as the messages and each key can only be used once, the process of exchanging the keys securely is exactly as hard as exchanging the messages was in the first place. Ignoring that, is the One Time Pad secure?

### Perfect Secrecy
Let's define security under the assumption an attacker only has access to the ciphertext. A basic definition would focus on our main concerns: that the attacker cannot access our secret key or our plaintext message. The formal definition accepted by cryptographers is that of *perfect secrecy*. We consider a cipher as having perfect sercery if for every m_0, m_1 in M where m_0 is the same size as m_1 and every c in C, the probability E(k, m_0) = c equals the probability E(k, m_1) = c where k is randomly chosen from K. This means that given two messages and a ciphertext our attacker could not tell which of the messages produced that ciphertext.

By this definition, we can prove the One Time Pad is secure. We won't--but we **could**. We will however note in closing, that to acheive this definition of perfect secrecy, a cipher must use a key equal in length or longer than our message. 

### Pseudorandom Generators
As we noted initially, the One Time Pad is not a particularly useful cipher because its key is as long as its messages. An idea to reduce the length of key while minimising the loss of security is to utilize pseudorandom key generators instead of random key generation. The pseudorandom generator is a function, G, that maps from a seed space to the larger key space. For an example of how they would work, consider the One Time Pad. If we use a key, k, smaller than the length of the message, and our pseudorandom generator, G, our new encryption function is E(k,m) = m XOR G(k). Along the same lines our new decryption function is D(k,c) = c XOR G(k). This is not perfectly secret, but it is reasonably secure. To redefine security in a way more applicable to this situation, let's further refine G.

We say G is predictable if there exists an efficent algorith A and a positive i less than or equal to n-1 such that the probabilty over a randomly chosen key K that we can predict that i+1th bit of G(k) given the first i bits of A(G(k)) is greater than half plus epsilon, for non-negligible epsilon. G is unpredictable if G is not predictable.

#### Note: Non-negligible

### Attacks on Stream Ciphers

### Example Stream Ciphers

### Secure Pseudorandom Generators

### Semantic Security

# This workshop is based off material from Dan Boneh's free online cryptography course.





