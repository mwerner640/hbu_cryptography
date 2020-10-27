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

If you dislike math, we recommend skipping to *Cryptography with Python* after you finish the first two sections of *Cryptography Introduction*.

## Cryptography Introduction
### What is cryptography?
Cryptography is a means of securing information to prevent **eavesdropping** and **tampering**. If you're only slightly familar with cryptography, you probably know it is a way of passing secret messages, but we also use cryptography to protect network traffic on the web, over wifi, and through bluetooth; to encrypt files on your disk drive; to access content locked into a specific platform (ie. songs you own on iTunes); and to verify your identity online.

As an example, encrypting communication over a network has two main steps: establishing a shared key using public key cryptography and then transmitting data using that shared secret key. These two steps are the basis of most traditional encryption. Non-traditionally, encryption can also be used to establish a digital signature or to communicate anonymously. 

### Simple Example Ciphers
To start, we'll begin with some ciphers that were used seriously in the past but nowdays with advances in computing are easily broken. You should only use these ciphers recreationally. 

First let's establish some notation. A *symmetric cipher* refers to an crypto system where both the encrypting party and the decrypting party use the same secret key. Our encryption and decryption algprithms are E(k,m) and D(k,c), where k is our key, m is our plaintext message, and c is our ciphertext. E(k,m)=c and D(k,c)=m. 

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
