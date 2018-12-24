# A New Club
AKA Public Private Key Crypto
- What is it and why should you care?

Back in the day, for most of human history, if someone wanted to communicate securely they had to either be in the same place at the same time, or use a shared secret code to encrypt and decrypt messages. This was problematic because in order to agree on and share a secret code you had first meet in person, and then if you changed your mind or someone broke your code you'd have to meet up again to agree to a new scheme. Not practical in a 24/7 globally connected digital world. Fortunately for us, a few guys named [Merkle](https://en.wikipedia.org/wiki/Ralph_Merkle), [Diffie](https://en.wikipedia.org/wiki/Whitfield_Diffie), and [Hellman](https://en.wikipedia.org/wiki/Martin_Hellman) came up with some [pretty cool ideas](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) on how to securely share private data over public networks. Let's see how that works...

![treehouse](https://i.huffpost.com/gen/804469/images/o-COOL-TREEHOUSE-DESIGNS-facebook.jpg)

Let's say that 2 friends named Alice and Bob build a treeshouse. Like any kids with a treehouse, they started a secret club. Like every secret club, there needs to be a secret way to prove that you're part of the secret club. Normally there would just be a cool passphrase or knock that you would yell to have the rope thrown down, but... this means that anyone nearby could learn their secret code! This wouldn't be that big of a deal, but there's this chick named Eve who believes that the world should have no secrets, and especially no secret clubs. Why? We'll never know, but she's always watching... and whenever she learns anything she posts it on the school bulletin board for everyone to know. Now Timmy has a daily panic attack when people put bugs in his desk and Sarah has to wear ear plugs incase anyone utters a palindrome, and they often do. Needless to say, if everyone knew the clubs secret code it would be a disaster ¯\\_(ツ)_/¯

So, Alice and Bob come up with a plan... First they thought about using [secret colors](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) because that seems to be [what everyone else is doing](https://www.youtube.com/watch?v=NmM9HA2MQGI), but when they tried it out everything just turns brown. This means anyone could just mix all the colors together to produce brown without actually knowing Alice and Bob's secret colors! Not cool, and besides, who wants to live in a world where everything's brown? Requiring a practical solution, Alice and Bob press on, and on one dismal afternoon, while staring at the clock, waiting, for school, to be over... they get an idea! A big one! What if instead of using colors, they used numbers, but instead of using any random numbers, they used numbers that wrap around [like a clock does](https://www.youtube.com/watch?v=Yjrfm_oRO0w)?

![clock](https://theproducersmiami.com/wp-content/uploads/2017/10/fascinating-24-inch-clock-24-inch-atomic-wall-clock-black-and-white-clock.jpg)

How would this help them share and verify the secret passphrase without Eve knowing?! 

```TODO: explain primitive root modulo stuff linking 5 to 23```

First, the club gets a number. After much consideration Alice and Bob decide that the number shall be 5, and 5 shall be the number. They tell everyone. 5 club is lit af. Everyone wants in, esp Eve... 

Then, the club gets a secret passphrase. At first only Alice and Bob know it, but as well all know, 5 club is lit af so soon more people join. They all know the passphrase, but they can't say it out loud because Eve could be anywhere anytime, always watching... This is truly annoying.

To get around this unfortunate dilema, Alice and Bob create a number system where, like clocks, the numbers wrap around if you go past the maximum number. In this case we're using a 24hrs clock that wraps around after 23. 

Also, everyone who's part of the club has a secret number. In fact, the club itself even has a secret number. These numbers are so secret that no one knows what they are or might be. They are truly random and unknown. They could be anything...

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
- They then write each number down on a piece paper, fold the papers into airplanes, and throw them at each other. Now both Jim and the people in the club have a shared secret that no one else knows that they can use to scramble and unscramble the secret passphrase!
- Jim scrambles every letter in the passphrase forward by the shared secret number.
- Jim then proceeds to yell that new series of letters out loud.
- Whoever is in the club writes down those letters, then shifts them backwards by the amount of the shared secret number.
- If it reveals the passphrase, Jim is in!
- Also, even though Eve is creeping in the bushes nearby, everyone who yells a scrambled passphrase to get in yells a different one because it's scrambled based on their unique numbers!
- Eve tries again and again to yell random passphrases but it doesn't work. Horray! 

Let's try this out!

```rust,editable

// TODO:
// - clean up the spaghetti code
// - add comments to explain everything

fn main() {
    
    
    // Club Parameters
    let passphrase = vec!["t", "h", "e", "p", "a", "s", "s", "w", "o", "r", "d", "i", "s", "p", "a", "s", "s", "w", "o", "r", "d"];
    let passphrase_test = vec!["t", "h", "e", "p", "a", "s", "s", "w", "o", "r", "d", "i", "s", "p", "a", "s", "s", "w", "o", "r", "d"];
    let modulo = 23;
    let club_base_number = 5;
    
    
    // (b**p) % m
    // b = base, p = private, m = modulo
    fn exp_mod(b: i32,
               pk: i32,
               m: i32) -> i32 {
        // b = base
        // pk = private key
        // m = modulo
        
        let mut out = (b * b) % m;
        //println!("0: {}", out);
        for i in 1..pk-1 { //because the first iter of out took 2 off the base
            out = (out * b) % m;
            //println!("{}: {}", i, out);
        }
        out
    }
    
    
    // Jim's Numbers
    let jim_secret_number = 4;
    let jim_public_number = exp_mod(club_base_number, jim_secret_number, modulo); // 4
    // The Club's Numbers
    let club_secret_number = 98;
    let club_public_number = exp_mod(club_base_number, club_secret_number, modulo); // 9
    
    
    // Let's create a secret key between Jim and The Club
    let jim_auth_number = exp_mod(club_public_number, jim_secret_number, modulo); // 6
    let club_jim_auth_number = exp_mod(jim_public_number, club_secret_number, modulo); // 6
    assert_eq!(jim_auth_number, club_jim_auth_number);


    // Now let's scramble the passphrase in a way that only the people in the club can unscramble!
    fn scramble(p: Vec<&str>,
                k: i32) -> (String, Vec<&str>) {
        
        // p = passphrase
        // k = key
    
        let a = vec!["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"];
        let key = k as usize;
        let mut scrambled_vec = Vec::new();
        
        for i in p {
            for j in 0..a.len() {
                if i == a[j] {
                    let alpha_index = j;
                    let scrambled_letter = (alpha_index + key) % 26;
                    scrambled_vec.push(a[scrambled_letter])
                }
            }
        }
        
        let mut scrambled_str = String::new();
        let sc = scrambled_vec.clone();
        for i in sc {
            scrambled_str.push_str(i);
        }
        
        (scrambled_str, scrambled_vec)
    }
    let (scrambled_passphrase_str, scrambled_passphrase_vec) = scramble(passphrase, jim_auth_number);
    
    // Eve is always watching...
    //let evesdropped_scrambled_passphrase = scrambled_passphrase_vec.clone(); // rust type `std::vec::Vec<&str>`, which does not implement the `Copy` trait
    let mut evesdropped_scrambled_passphrase = Vec::new();
    for i in scrambled_passphrase_vec.clone() {
        evesdropped_scrambled_passphrase.push(i);
    }
    
    
    // But can the Secret Club unscramble the passphrase?
    fn unscramble(p: Vec<&str>,
                  k: i32) -> (String, Vec<&str>) {
                  
        // p = passphrase
        // k = key

        let a = vec!["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"];
        let key = k as usize;
        let mut unscrambled_vec = Vec::new();
        
        for i in p {
            for j in 0..a.len() {
                if i == a[j] {
                    let alpha_index = j;
                    let unscrambled_letter = (26 + alpha_index - key) % 26;
                    unscrambled_vec.push(a[unscrambled_letter])
                }
            }
        }
        
        let mut unscrambled_str = String::new();
        let usc = unscrambled_vec.clone();
        for i in usc {
            unscrambled_str.push_str(i);
        }
        
        (unscrambled_str, unscrambled_vec)
    }    
    let (unscrambled_passphrase_str, unscrambled_passphrase_vec) = unscramble(scrambled_passphrase_vec, club_jim_auth_number);
    
    
    // Did it work?
    assert_eq!(unscrambled_passphrase_vec, passphrase_test);
    println!("original passphrase: {:?}", passphrase_test);
    println!("scrambled passphrase: {:?}", scrambled_passphrase_str);
    println!("unscrambled passphrase: {:?}", unscrambled_passphrase_str);

}
```

Now you're probably wondering: "Isn't that a bit convoluted? I mean they could just have a list of people's names and then look out the window and see if that person is the person on the list!" Yes... they **could**, but what about on Halloween when everyone's wearing costumes, or at night when it's dark, or during they day when they're bored? These are the questions that keep me up at night, and apparently Alice and Bob feel the same way. Now they can have their secret club with secret numbers and all Eve knows is that whenever she tries to guess the passphrase by saying random numbers it doesn't work. Yay!

![celebrate](https://proxy.duckduckgo.com/iu/?u=https%3A%2F%2Fwallpaperbrowse.com%2Fmedia%2Fimages%2Fcelebrate.jpg&f=1)

Everyone celebrates and lives happily ever after, or do they... While Alice and Bob and celebrating their new club, Eve is hard at work learning about the dark art or cryptanalysis. What will she learn? How will she use it? 

Find out in part 2: [Eve Strikes Back!](https://github.com/burrrata/the_cryptoeconomics_cookbook/blob/master/src/eve_strikes_back.md)

<hr>

### Recommend Research Resources
- [Wikipedia: modulo operation](https://en.wikipedia.org/wiki/Modulo_operation)
- [Wikipedia: diffie hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)
- [Computerphile: diffie hellman with colors](https://www.youtube.com/watch?v=NmM9HA2MQGI)
- [Computerphile: diffie hellman with modulo operations](https://www.youtube.com/watch?v=Yjrfm_oRO0w)
- [The Rust Playground](https://play.rust-lang.org/)

Rust code that follows along with the diffie hellman wikipedia article. Feel free to edit and play around with it here, or copypasta into the [Rust Playground](https://play.rust-lang.org/)
```rust,editable
// TODO: add an explanation for primitive Root Modulo Stuff
// - the base is a primitive root modulo
// - this is not explained, thus this is not an end-to-end tutorial
// - how do we explain this simply?

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

<hr>

### TODOS
- explain primitive root modulo stuff linking 5 to 23
- would it be easier for people to read if we used strings and chars instead of Vecs?
- make sure that there are no trailing references to the Club Public Number when it should be the Club Base Number which is 5.
- is it confusing that Jim's public number is the same as his private number, even though the modulo stuff is explained and demonstrated?
- to minimize vocabulary juggling and manitain relevance in the context of public private key crypto, search and replace: 
    - number => key
    - secret => private
