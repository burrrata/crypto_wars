Note: this is a remix of this original article
- https://medium.com/crypto-economics/introduction-to-cryptography-part-1-646893e87d5d




# Public Private Key Crypto
- what is it and why should you care?

Back in the day, for most of human history, if someone wanted to communicate securely they had to either be in the same place at the same time, or use a shared secret code to encrypt and decrypt messages. This was problematic because in order to agree on and share a secret code you had first meet in person, and then if you changed your mind or someone broke your code you'd have to meet up again to agree to a new scheme. Not practical in a 24/7 globally connected digital world. Fortunately for us, a few guys named Merkle, Diffie, and Hellman came up with some pretty cool ideas on how to securely share private data over public networks. Let's see how that works...

Let's say that 2 friends named Alice and Bob want to start their own secret club. Of course, like with every secret club, there needs to be a secret way to prove that you're part of the secret club. To make things more difficult... Alice and Bob don't have a clubhouse so they hangout in the school yard with all the other kids. This means that if they reveal that they have a secret club in the open, everyone will know! This wouldn't be that big of a deal, but there's this chick named Eve who believes that the world should have no secrets, and especially no secret clubs. Why? We'll never know, but she doesn't hangout with the other kids; she hangs out at the highest point on the playground, always watching... She's an odd one ¯\\_(ツ)_/¯

Anyways, 
