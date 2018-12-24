# Eve Strikes Back

## Overall Theme / Main Takeaway
- don't roll your own crypto like Alice and Bob did 👍

## Concepts to Cover
- man in the middle attacks => spoofing, rerouting, or crypanalysis of scrambled messages
- rate limiting to prevent online brute force attacks (does not prevent captured messages via MIM attacks and offline hash cracking via rainbow tables or hashcat)
- randomness (Alice and Bob use the same keys every time, so Eve can perform cryptanalysis)
- things that are hard to guess for humans vs computers (demo a brute force cracking attack) (stretch goal: a social engineered cracking attack based on Alice and Bob's favorite things)
- social engineering via publicly available security questions and 

## Story Arch

### P1) A blessing in disguise: 
- Eve learns the dark arts of cryptanalysis and social engineering (HOW? She was traveling and one of her bags was lost for months. She needed to get back into her accounts but had no way to access the passwords and codes that were securely stored in her checked bag. Desperate, she tried everything and, with the help of her friend Mal, learned how to break into her own accounts. Thinking it through, she wondered if the same tactics could help her infultrate Alice and Bob's now super popular club?). Meanwhile Alice and Bob are enjoying their club so much that they get tired of doing all the work of checking and doing the protocol they implimented. Since Eve hasn't attacked in a while anyways, they oursource verification to Alice's little brother in exchange for a chocolate bar every other friday. Alice's mom is a strict vegan health nut so this is a big deal for her little brother.

### P2) The Final Attack:
- Eve completely [pwns](https://proxy.duckduckgo.com/iu/?u=http%3A%2F%2Fa.abcnews.com%2Fimages%2FUS%2F160112_vod_orig_ice%2520_mixdown_16x9_992.jpg&f=1) Alice and Bob in various ways: first intercepting messages and using brute force attacks and cryptanalysis to uncover the secret messages (Alice's little brother was only told to follow the protocol, so Eve can just hangout and try guess after guess with no penalties lol), then by sending false information between club memeber to create drama and upset, and then by bribing Alice's little brother with a Snickers bar to give her access to the secret master keys. 

### P3) Free At Last?
- Alice and Bob are forced to abandon the treehouse because it becomes untenable and overrun with field trips from CS schools warning students of the perrils of rolling your own crypto. They've think they've lost everything, but they still have each other. 

<hr>

Beginnings of code building on previous concepts in part 1, and expanding to impliment Eve's various attacks :)

```rust,editable
// PART 2: Eve Strikes Back!

// TODOS
    // it would be great to include a framework for people
    // to try being Eve and attempting random values and see 
    // if they work or not
    // 
    // then create a game (with larger primes) where the secret
    // numbers are chosen randomly you have to prove that you
    // know the secret number to get into the club (thus
    // showing how it's easy to prove if you're in the club
    // but much harder to break if you're not in the club)
    // 
    // then link to a real dh library in rust that explains
    // how to use it in practice and provides secure code



fn main() {
    
    // USEFUL FUNCTIONS
    
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
    
    // scrambles a vector of strings based on an input key
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

    // undoes the effect of the scramble() function
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
    

    
    // DATA
    
    // Club Parameters
    let passphrase = vec!["t", "h", "e", "p", "a", "s", "s", "w", "o", "r", "d", "i", "s", "p", "a", "s", "s", "w", "o", "r", "d"];
    let passphrase_test = vec!["t", "h", "e", "p", "a", "s", "s", "w", "o", "r", "d", "i", "s", "p", "a", "s", "s", "w", "o", "r", "d"];
    let modulo = 23;
    let club_base_number = 5;
    
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

    // Jim's message
    let (scrambled_passphrase_str, scrambled_passphrase_vec) = scramble(passphrase, jim_auth_number);
    
    // Eve is always watching...
    //let evesdropped_scrambled_passphrase = scrambled_passphrase_vec.clone(); // rust type `std::vec::Vec<&str>`, which does not implement the `Copy` trait
    let mut evesdropped_scrambled_passphrase = Vec::new();
    for i in scrambled_passphrase_vec.clone() {
        evesdropped_scrambled_passphrase.push(i);
    }
    println!("Evesdropped scrambled passphrase: {:?}", evesdropped_scrambled_passphrase);
    
    
    // Jim again
    let (unscrambled_passphrase_str, unscrambled_passphrase_vec) = unscramble(scrambled_passphrase_vec, club_jim_auth_number);
    
    // Did it work?
    assert_eq!(unscrambled_passphrase_vec, passphrase_test);
    println!("original passphrase: {:?}", passphrase_test);
    println!("scrambled passphrase: {:?}", scrambled_passphrase_str);
    println!("unscrambled passphrase: {:?}", unscrambled_passphrase_str);
    

    
    // But wait! 
    // What if Eve hears someone saying a scrambled passphrase
    // and then tries to use someone else's public key to unscramble it?
    // Or what it Eve tries to pretend she's someone else using their public key?
    let eve_public_number = jim_public_number.clone(); // 4
    // But wait... Even with Jim's Public Key, how many times does Eve need to 
    // multiply the Club Number by itself to find the secret key shared between Jim and The Club?
    //let eve_secret_number = "?";
    //let eve_auth_number = exp_mod(club_public_number, eve_secret_number, modulo);
    
    // Looks like Eve will have to guess
    for i in 0..23 {
        let eve_secret_number = i;
        let eve_auth_number = exp_mod(club_public_number, eve_secret_number, modulo);
        let (eve_unscrambled_passphrase_str, eve_unscrambled_passphrase_vec) = unscramble(evesdropped_scrambled_passphrase.clone(), eve_auth_number);
        if eve_unscrambled_passphrase_vec == passphrase_test {
            println!("Eve broke the code!");
            println!("Secret Passphrase: {:?}", eve_unscrambled_passphrase_str);
            println!("Secret Key: {:?}", eve_auth_number);
        }
    }
    
    // Looks like Eve is going to have to resort to social engineering to trick
    // the club bouncer into thinking that her made up public key is legit
    let eve_private_number = 45;
    let eve_public_number = exp_mod(club_base_number, eve_private_number, modulo); // 5
    let club_eve_auth_number = exp_mod(eve_public_number, club_secret_number, modulo); // 9

}
```
