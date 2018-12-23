Note: this is a remix of this original article
- https://medium.com/crypto-economics/introduction-to-cryptography-part-1-646893e87d5d




# Public Private Key Crypto
- what is it and why should you care?

Back in the day, for most of human history, if someone wanted to communicate securely they had to either be in the same place at the same time, or use a shared secret code to encrypt and decrypt messages. This was problematic because in order to agree on and share a secret code you had first meet in person, and then if you changed your mind or someone broke your code you'd have to meet up again to agree to a new scheme. Not practical in a 24/7 globally connected digital world. Fortunately for us, a few guys named Merkle, Diffie, and Hellman came up with some pretty cool ideas on how to securely share private data over public networks. Let's see how that works...

![treehouse](https://i.huffpost.com/gen/804469/images/o-COOL-TREEHOUSE-DESIGNS-facebook.jpg)

Let's say that 2 friends named Alice and Bob build a treeshouse. Like any kids with a treehouse, they started a secret club. Like with every secret club, there needs to be a secret way to prove that you're part of the secret club. Normally there would be a cool passphrase or knock that you would yell to have the rope thrown down, but... this means that anyone nearby could learn their secret code! This wouldn't be that big of a deal, but there's this chick named Eve who believes that the world should have no secrets, and especially no secret clubs. Why? We'll never know, but she's always watching... and whenever she learns anything she posts it on the school bulletin board for everyone to know. Now Timmy has a daily panic attack when people put bugs in his desk and Sarah has to wear ear plugs incase anyone utters a palindrome, and they often do. Needless to say, if everyone knew the clubs secret code it would be a disaster ¯\\_(ツ)_/¯

[there needs to be a better segue into this]
So, Alice and Bob come up with a plan... First they try using [secret colors](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) because that seems to be [what everyone else is doing](https://www.youtube.com/watch?v=NmM9HA2MQGI), but when they try it out in practice everything just turns brown. This means anyone could just mix all the colors together to produce brown without knowing Alice and Bob's secret color, and still get into their club. Not cool, and besides, who wants to live in a world that's all brown? Requiring a practical solution, Alice and Bob press on, and on one dismal afternoon, while staring at the clock, waiting, for school, to be over... they get an idea! A big one! What if instead of using colors, they used numbers, but instead of using any random numbers, they used numbers that wrap around [like a clock does](https://www.youtube.com/watch?v=Yjrfm_oRO0w)?

How would this work?! 

Well... first numbers would work like clocks in that if you go past the maximum they just wrap around again. In this case we're using a 24hrs clock that wraps around after 23. Second, the club needs a number. [TODO: explain primitive root modulo stuff here] After much consideration Alice and Bobs decide that the number shall be 5, and 5 shall be the number. They tell everyone. 5 club is lit af. Everyone wants in, esp Eve... 

Everyone who's part of the club has a secret number. In fact, the club itself even has a secret number. These numbers are so secret that no one knows what they are or might be. They are truly random and unknown. They could be anything...

Everyone, including the club, also has a public number. These are kind of like an address and everyone knows what these are. How do we create these public numbers in a way that connects them to the private numbers? Well it's easy, just multiply the club's number by it's self (exponentiation) as many times as a person's secret number, but wrap around everytime they go past 23 (like on a clock). For example: if Jim's secret number is 4, then Jim would multiple the club's public number 4 times (5 * 5 * 5 * 5), but would wrap around everytime the value was higher than 23. The symbol for this is %, and the mathematical term is the [modulo operation](https://en.wikipedia.org/wiki/Modulo_operation). Try it out for yourself! 
```rust,editable
fn main() {
    
    let modulo = 23;
    let club_public_number = 5;
    let jim_secret_number = 4; // 4
    
    // (b**p) % m
    // function to perform exponential modulo artithmetic 
    // because rust makes exponential multiplication with 
    // i32 a real bother
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
    
    // What's Jim's public number?
    let jim_public_number = exp_mod(club_public_number, jim_secret_number, modulo);
    println!("Jim's public number is: {}", jim_public_number);

    // let's check for other numbers that could have created the same public number
    for i in 0..1000 {
        let alt_public_number = exp_mod(club_public_number, i, modulo);
        if alt_public_number == jim_secret_number {
            println!("alt secret number: {}", i);
        }
    }
    
}
```

Now Jim has a public number (4), but no one knows what number was multiplied around the clock to get there. Could be 5\*\*4, could be 5\*\*26, could be 5\*\*136, or many others... 

Anyways, the way to check that Jim is part of the club is to do 2 things:
- Jim multiplies the club's public number times itself as many times as his secret number
- Whoever is in the club multiplies Jim's public number times itself as many times as the club secret number
- They then write the numbers down on paper, fold the paper into airplanes, and throw them at each other; if the numbers match they're in.

Let's try this out!

```rust,editable
// TODO:
//
// Primitive Root Modulo Stuff
// - the base is a primitive root modulo
// - this is not explained, thus this is not an end-to-end tutorial
// - how do we explain this simply?
//
// Attacks and Failures
// - it would be great to include a framework for people
// - to try random values and see if they work or not
// - 
// - then create a game (with larger primes) where the secret
// numbers are chosen randomly you have to prove that you
// - know the secret number to get into the club (thus
// - showing how it's easy to prove if you're in the club
// - but much harder to break if you're not in the club)
// - 
// - then link to a real dh library in rust that explains
// - how to use it in practice and provides secure code



// This is the (editable) code that our friends Alice 
// and Bob used for their secret club. Feel free to
// uncomment the println!() macros to see what's going on.
// This also happens to follow the example in the wikipedia
// article on Diffie-Hellman key exchange, so feel free to
// check that out as well,
// https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange

fn main() {
 
    // Alice and Bob's Agreed Upon Parameters
    let base = 5;
    let modulus = 23;

    // Alice and Bob's Private Keys
    let a_private = 4;
    let b_private = 3;
    
    // Function to perform exponential modulo artithmetic 
    // because rust makes exponential multiplication with 
    // i32 a real bother
    // (b**p) % m
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
    
    // Alice and Bob's Public Keys
    let a_public = exp_mod(base, a_private, modulus);    
    let b_public = exp_mod(base, b_private, modulus);

    // Let's Check To See If It Worked!
    let to_a_from_b = exp_mod(b_public, a_private, modulus);
    let to_b_from_a = exp_mod(a_public, b_private, modulus);
    assert_eq!(to_a_from_b, to_b_from_a);
    //println!("to_a_from_b: {}", to_a_from_b);
    //println!("to_b_from_a: {}", to_b_from_a);
    
}
```


### Recommend Research Resources
- wikipedia modulo operation: https://en.wikipedia.org/wiki/Modulo_operation
- wikipedia diffie hellman: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
- computerphile colors: https://www.youtube.com/watch?v=NmM9HA2MQGI
- computerphile modulo: https://www.youtube.com/watch?v=Yjrfm_oRO0w
- rust playground: https://play.rust-lang.org/
