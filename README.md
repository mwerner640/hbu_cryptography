# hbu_cryptography

In this section of the workshop we are going to learn:
- Cryptography Introduction
- Stream Ciphers
- Block Ciphers
- Trapdoor Permutation 
- Diffie-Hellman 

Coming Soon:
- Message Integrity 
- Collision Resistant Hashing 
- Authenticated Encryption 
- Basic Key Exchange 
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
Let's define security under the assumption an attacker only has access to the ciphertext. A basic definition would focus on our main concerns: that the attacker cannot access our secret key or our plaintext message. The formal definition accepted by cryptographers is that of *perfect secrecy*. We consider a cipher as having perfect sercery if for every m<sub>0</sub>, m<sub>1</sub> in M where m<sub>0</sub> is the same size as m<sub>1</sub> and every c in C, the probability E(k, m<sub>0</sub>) = c equals the probability E(k, m<sub>1</sub>) = c where k is randomly chosen from K. This means that given two messages and a ciphertext our attacker could not tell which of the messages produced that ciphertext.

By this definition, we can prove the One Time Pad is secure. We won't--but we **could**. We will however note in closing, that to acheive this definition of perfect secrecy, a cipher must use a key equal in length or longer than our message. 

### Pseudorandom Generators
As we noted initially, the One Time Pad is not a particularly useful cipher because its key is as long as its messages. An idea to reduce the length of key while minimising the loss of security is to utilize pseudorandom key generators instead of random key generation. The pseudorandom generator is a function, G, that maps from a seed space to the larger key space. For an example of how they would work, consider the One Time Pad. If we use a key, k, smaller than the length of the message, and our pseudorandom generator, G, our new encryption function is E(k,m) = m XOR G(k). Along the same lines our new decryption function is D(k,c) = c XOR G(k). This is not perfectly secret, but it is reasonably secure. To redefine security in a way more applicable to this situation, let's further refine G.

We say G is predictable if there exists an efficent algorith A and a positive i less than or equal to n-1 such that the probabilty over a randomly chosen key K that we can predict that i+1th bit of G(k) given the first i bits of A(G(k)) is greater than half plus epsilon, for non-negligible epsilon. This is to say that if we have an algotithm A,  where knowing the result of the first i bits of the algorithm on the generated key, G(k), allows us to predict the i+1th bit of the generated key at greater than 50% accuracy, then G is predictable. if G is unpredictable if G is not predictable.

#### Note: Non-negligible
Epsilon is a function that maps the positive integers to the positive real numbers. Epsilon is non-negligible if there exists a d such that epsilon of lambda is greater than or equal to one over lambda to the power of d infinitely often. In practice this is roughly equivalent to epsilon being greater than one divided by two to the power of thirty.

### Attacks on Stream Ciphers
#### Key Reuse
We noted before without explaining, that keys for the one time pad can only be used once. To prove this, lets assume we intercepted two ciphertext messages made with the same key. How can we access the plaintext? The attack for this scenario is to xor the two ciphertexts. If we're working with the same key then we'll end up with our two messages xor-ed as the key will cancel out. While this will not reveal the seperate plaintexts it will produce a value, that due to the redundancies in english and ascii, we can obtain a result from, just like the sample ciphers in the last section. 

This may seem like an easy mistake to avoid, but stream ciphers have been used with repeated keys in ways that allowed exploits like this to exist in disk encryption and encrypting network traffic. To avoid this, it is now recommended to never use stream ciphers for disk encryption and it has become standard for networks to renegotiate keys each session. 

#### Manipulation
Stream ciphers often lack integrity, which is to say that an intercepted ciphertext can be modified in a way that is undetectable and leads to predictable changes to the plaintext. For example with the one time pad, xoring the ciphertext with a malicious edit, x, will lead the decode function to produce a "plaintext" that is really the plaintext xored with x.

### Example Stream Ciphers
#### RC4
RC4 was a stream cipher introduced in 1987. It was used in HTTPS and WEP. Unfortuantely there is a bias in the initial output that has been discovered which allows for related key attacks. Thus, RC4 is no longer in use today.

#### CSS
The CSS was a piece of hardware known as the Content Scramble System. The seed for CSS was only five bits, that was then fed through linear feedback shift registers and modular addition. A systematic and extremely quick attack exists to identify the key from ciphertext for CSS. At that point the message can be decrypted traditionally. 

#### eStream
eStream is a modern stream cipher that uses both a seed and a nonce. The nonce is non-repeating for any given key. The equation is then E(k, m; r) = m xor PRG(k;r). The PRG most commonly used is Salsa20, which has no known attack better than brute force. Despite this, we cannot prove that Salsa20--or any PRG is secure.

### Secure Pseudorandom Generators
We say a PRG is secure if for any efficient statistical test, A, the advantage of A, G is negligible. We define *statistical test* on the space {0,1} to the n as an algorithm, A, such that A(x) outputs 0 for not random or 1 for random. We define *advantage* A,G on A, a statistical test, and G, a PRG, as the absolute value of the probability that A(G(k))=1 for a random k from the seed space, minus the probability A(r)=1, with r being a random key from the full keyspace. There is currently no PRG that has been proven secure, although serious contenders are suspected to be secure and have not been proven insecure. We can note that its proven that a secure PRG if any are to be found, will be unpredictable. In the opposite direction, any unpredictable PRG will be secure. 

For future reference we should note that two distributions over {0,1} to the n are *computationally indistinguishable* if there exists an efficient statistical test, A, such that the difference between the probabilities that the statistical tests on elements of each distribution is one is less than negilible. 

### Semantic Security
When considering *semantic security* we define the advantage A,E on A, a statical test, and E, an encryption algorithm, as the difference between the probabilities of W<sub>a</sub> and W<sub>b</sub>. W<sub>a</sub> is the event that an experiement, a, equals 1. E is *semantically secure* if for all efficient A the semantic security advantage A,E is negilible. This means for all explict m<sub>0</sub>, m<sub>1</sub> E(k, m<sub>0</sub>) is compuationally indestinguishable from E(k, m<sub>1</sub>).

## Block Ciphers
### What are Block Ciphers?
Block ciphers are symmetric ciphers that operate on fixed size groups of bits, refered to as blocks. As an example 3DES can only take message text of 64 bits and requires a 168 bit key. This is in contrast to stream ciphers such as the one time pad, which can take messages of any size and requires no specific key length. This specificity is because block ciphers are bit on iteration. The key is expanded into multiple keys and then a round function, R(k<sub>i</sub>,m), is performed on each key and the message. 

In general, block ciphers in use today are faster than stream ciphers in use today.

Let's consider block ciphers more rigorously. We can consider block ciphers *Pseudorandom Functions*/*Pseudorandom Permuatations*. A PRF, f, defined over (K,X,Y) is a function that maps from KxX to Y such that there exists an efficient algorithm to evaluate f(k,x). A PRP, E, defined over (K,X) is a function that maps from KxX to X such that there exists an efficient deterministic algorithm to evaluate E(k,x), the function E(k, x) is one-to-one, and there exists an efficient inversion algorithm D(k,y). This is to say that E cannot be distinguished from a random permutation. Note that all PRPs are PRFs, but to be a PRP a PRF must also have X=Y and be invertible. 

Informally we can consider PRFs secure if f(k,x) is indistinguishable from a random function from X to Y and PRPs secure if E(k,x) is indistinguishable from a random one-to-one function from X to Y. 

### DES
DES, the Data Encryption Standard was established as the standard in 1976 and broken in 1997. It was widely deployed in the financial industry. The cipher is based on the idea of a Feistel Network. Given d funcions that maps {0,1} to the n onto itself, we want to build an invertible function from {0,1} to the 2n onto itself. We can define the network recursively where R<sub>i</sub> = f<sub>i</sub>(R<sub>i-1</sub>) xor L<sub>i-1</sub> and L<sub>i</sub> = R<sub>i-1</sub> where R<sub>0</sub> and L<sub>0</sub> are our input and R<sub>d</sub> and L<sub>d</sub> are our output. This is an invertible function and so our decryption version is this process in reverse order. If our functions are secure PRFs and we use independent keys, feistel networks of specific lengths have been proven secure PRPs. DES consists of a 16 round feistel network and a key expansion, taking our k out to 16 keys. The functions inside the feistel network are refered to as S-boxes, and then the final function on the network's results is the P-box. These functions need to be chosen carefully to avoid linearity. Chosing the functions at random would lead to an insecure cipher.    

### Attacks
As we noted earlier, DES is broken. The most significant method for breaking DES is the exhaustive search attack.

#### Exhaustive Search Attack
The exhaustive key search is a brute force attack to find the key given two message ciphertext pairs. The process is time consuming (potentially months of searching) or expensive (tens of thousands of dollars of computing power), but it has been repeatedly demonstrated as possible. The 56 bit key is too small. We can strengthen DES to prevent thus attack with 3DES, which triples the entire process. While no longer vulnerable to exhausive search, 3DES is slow, even for a block cipher. Why not 2DES? Doubling DES leaves the process vulnerable to a meet in the middle attack which would reduce the search time down to that of DES. DESX was also proposed to replace DES, but it too has been broken.

#### Side Channel Attacks
Rather than attacking the ciphertext or plaintext ciphertext pairs, attackers have been known to break block ciphers by observing the timing of encryption and decryption or the power consumption of the algorithms involved. Blocking this type of attack is fairly difficult and typically involves generating noise on the system and establishing protocols to maintain consistent computing times. 

#### Fault Attacks
A single error in the implementation of the last round of a block cipher is known to expose the secret key. 

#### Linear Attacks
As we noted when discussing DES, the presense of linearity in the rounding functions of the block cipher can make the entire thing linear. To show you intuitively what that means for security, an example of a linear cipher is a caesar cipher. Ciphers protect the message by making the relationship between the plaintext and ciphertext appear random. Preventing a linear relationship is crucial, but still extremely difficult, hence why attackers will still search for linear relationships and occassionally find them.

#### Quantum Attacks
Just as DES is has too small a keyspace to not be susceptible to brute force attacks, all block cipher attacks are susceptible to brute force when quantum computing is brought into play. Fortunately quantum computers are rare and expremely expensive to run, so only highly valuable decryptions would be worth months of computing time on a quantum computer.

### AES
AES is a Subs-Perm Network. Ten alternating Subs and Perm layers invoke the expanded keys to take our input to our output. The process involves byte substitution, row shifting, and column mixing. AES is currently implemented in browsers through Javascript and in Intel hardware. No reasonable attack is known to exist on AES (excluding quantum).

### Block Ciphers From PRGs
It is possible to build block ciphers from PRGs. We can expand PRGs into PRFs and the Luby-Rackoff Theorem expands these PRFs into PRPs. This is rarely used in practice as it is much slower than other methods. 

### Many Time Keys
So far, in this workshop, we've emphasised how using the same key more than once greatly decreases the security of our cipher. While this in true, in practice keys are commonly reused, because the process of securely exchanging new keys is so costly. A variety of processes have been developed to lessen the damage of many time keys.

#### Electronic Code Book
The most basic "strategy" for using a key multiple times, the Electronic Code Book makes no changes to the encryption process beyond saving results to reuse on duplicate messages. With ECB if we encrypt two identical messages we receive two identical ciphertexts. This doesn't seem like a huge problem, but in cases with large volumes of messages to incript, such as image encryption, clear patterns can emerge which give away a lot of data about the unencrypted image. In the same line, we would be able to easily modify the plaintext image, by adding a pattern to the ciphertext image. 

#### Random Algorithm
A strategy to combat this problem is to use randomised encryption and decryption algorithms. This is to say that if we have a message, m<sub>1</sub>, E would encrypt m<sub>1</sub> into c<sub>1</sub> or c<sub>2</sub>, etc, and D would decrypt both back into m<sub>1</sub>. While the theory behind this solution is elegant, it requires lengthening the ciphertext, which is unideal as we want to minimise the storage or transmission costs for our message as much as possible.

#### Nonce Use
We've already introduced the concept of nonces. By consistently varying our nonce we can avoid varying our key while dodging the identical results issue that arises with ECB. To avoid re-exchanging our nonce each transmission as we would a key, we can have our nonce be a counter or variation on a counter. Nonce based encryption is semantically secure under chosen plaintext attack. 

A *chosen plaintext attack* is when an attacker can encrypt as many messages of their choice as they want and then view their ciphertext. This is a reasonable privlege for us to grant our attacker as we can see this occur in real life situations such as with access to a password protected encrypted disk. Our attacker cannot decrypt data without the password, but they can encrypt new messages of their chosing.  

#### CBC
In Cipher Block Chaining, each plaintext block is xored with the last block before being encrypted. To ensure uniqueness, a well chosen initial message, referred to as IV, is xor-ed with the first block. The ciphertext must then be padded to a multiple of ciphertext block. If no pad is needed we add a dummy block. Our IV must be unpredicatable. For added security we can implement a nonce with CBC, used with the first of the expanded keys.

#### CTR
CBC can be further refined by xor-ing a counter to IV or concatinating a nonce to IV. That including an easily predictable elment such as a counter will enhance security has had many skeptics, but in this case, no significant attacks have been found that cannot be blamed on poor choice of IV or the underlying functions, so the practice is widely accepted.

## Trapdoor Permutation
### Public Key Encryption
### Trapdoor Construction
### RSA
### PKCS

## Diffie-Hellman
### ElGamal Public-key System
### ELGamal Security
### Improving ElGamal Security

# This workshop is based off material from Dan Boneh's free online cryptography course.





