Note: this is a remix of this original article
- https://medium.com/crypto-economics/introduction-to-cryptography-part-1-646893e87d5d




# Public Private Key Crypto
- what is it and why should you care?

Back in the day, for most of human history, if someone wanted to communicate securely they had to either be in the same place at the same time, or use a shared secret code to encrypt and decrypt messages. This was problematic because in order to agree on and share a secret code you had first meet in person, and then if you changed your mind or someone broke your code you'd have to meet up again to agree to a new scheme. Not practical in a 24/7 globally connected digital world. Fortunately for us, a few guys named Merkle, Diffie, and Hellman came up with some pretty cool ideas on how to securely share private data over public networks. Let's see how that works...

Let's say that 2 friends named Alice and Bob want to start their own secret club. Of course, like with every secret club, there needs to be a secret way to prove that you're part of the secret club. To make things more difficult... Alice and Bob don't have a clubhouse so they hangout in the school yard with all the other kids. This means that if they reveal that they have a secret club in the open, everyone will know! This wouldn't be that big of a deal, but there's this chick named Eve who believes that the world should have no secrets, and especially no secret clubs. Why? We'll never know, but she doesn't hangout with the other kids; she hangs out at the highest point on the playground, always watching... She's an odd one  ¯\\_(ツ)_/¯

Anyways, Alice and Bob come up with a plan... First they try using [secret colors](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) because that seems to be [what everyone else is doing](https://www.youtube.com/watch?v=NmM9HA2MQGI), but when they try it out in practice it turns out that everything just turns brown (to shit). And who wants to live in a world that's all brown (shit)? Requiring a practical solution, Alice and Bob press on, and on one dismal afternoon while staring at the clock waiting for school to be over, the get hit by an idea! A big one! What if instead of using colors, they used numbers, but instead of using any random numbers, they used numbers that wrap around [like a clock does](https://www.youtube.com/watch?v=Yjrfm_oRO0w)?

How would this work?! Well... Alice and Bob (or anyone else in the club) would each have a secret number 
- following the wiki article: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange

```rust,editable
// the base is a primitive root modulo
// how do I explain that simply?

fn main() {
 
    // (b**p) % m
    // rust makes exponential multiplication with i32 a real bother
    // but hopefully this sheds some more light on exactly what's going on
    // feel free to uncomment the println!() macros to see for yourself! :)
    fn exp_mod(b: i32,
               p: i32,
               m: i32) -> i32 {
        
        let mut out = (b * b) % m;
        //println!("0: {}", out);
        for i in 1..p-1 { //because the first iter of out took 2 off the base
            out = (out * b) % m;
            //println!("{}: {}", i, out);
        }
        out
    }
    
    // Public Params
    let modulus = 23;
    let base = 5;
    // Alice
    let a_private = 4;
    let a_public = exp_mod(base, a_private, modulus);
    // Bob
    let b_private = 3;
    let b_public = exp_mod(base, b_private, modulus);

    // Check Results
    let to_a_from_b = exp_mod(b_public, a_private, modulus);
    let to_b_from_a = exp_mod(a_public, b_private, modulus);
    assert_eq!(to_a_from_b, to_b_from_a);
    println!("to_a_from_b: {}", to_a_from_b);
    println!("to_b_from_a: {}", to_b_from_a);
    
}
```


### Recommend Research Resources
- wikipedia: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
- computerphile colors: https://www.youtube.com/watch?v=NmM9HA2MQGI
- computerphile modulo: https://www.youtube.com/watch?v=Yjrfm_oRO0w
