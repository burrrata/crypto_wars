<p align="center">
  <img src="https://fontmeme.com/temporary/2fff75811232a63155fb5caf857c4cec.png">
</p>

<br>

## Overall Theme / Main Takeaway
- learn from best practices and use established and audited protocols and libraries when dealing with sensitive and valuable data.

<br>

## Concepts To Cover
- the difference between psuedo random and cryptographically random numbers
- the avalanche effect in hash functions
- verifiable content addressing via hashes
- rate limiting to prevent brute force attacks
- true randomness by rolling dice or augmenting the passphrase
- length > complexity and computation time in big O notation => show why using larger primes is essential as well as how to determine the theoretic/computational security guarantees of a protocol (but how large of computations can the Rust Playground or mdBook handle?)
- 2FA > security questions (or randomized answers to security questions)
- digital signatures: for content addressing/verification as well as 1 way hash functions
- it would be cool to actually have a gif/video of an avalanche to emphasize that while you know **something** happened, you can't which piece of snow on the ground came from which place on the mountain because it's all jumbled up (and would be uniquely for every new avalanche)

<br>

## Story Arch

### Alice and Bob strike back!

### P1) Learning The Hard Way
- Alice and Bob explore best practices in crypto such as: don't roll your own crypto, use audited code and protocols, don't reuse passphrases or keys, don't let other people hold/control your keys, don't use a centralized processor to manage security or data access.

### P2) Sweet Revenge
- They use Alice's little brother as a honeypot to catch Eve red handed manipulating the old codes

### P3) Survival Of The Fittest
- They impliment a newer better protocol impressing all the CS professors who used to laugh at them, and Eve is banned from learning any more trickery in the computer lab. (might be better if she was permanently vanquished or converted to an ally than just being censored?)


<br>

