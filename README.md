# Deckware
## Randomness Extractor
Deckware is a 237-bit randomness extractor, returning the randomness in the shuffle of a 54-card
deck of standard playing cards (52 cards and 2 jokers). It's inspired by [Pokerware][1], which is in
turn inspired by [Diceware][2]. Deckware also ships its own word list, which is explained later in
this readme. The entropy extractor returns a 237-bit hexadecimal string for you do do with as you
please. One use could be converting that hexadecimal string into a 14-word [Niceware passphrase][3],
as the entropy for a [Bitcoin BIP39 mnemonic][4], or as an 8-word Deckware passphrase, described
below.

The entire tool is self-contained in a single HTML file which can and should be executed offline by
opening the file locally in your browser. It does not require any external dependencies, and does
not ship any JavaScript frameworks or libraries.

A sufficiently shuffled deck of 54 playing cards produces log2(54!) ~= 237.0638 bits of entropy.
Deckware uses [Lehmer Code][5] to assign a unique number to every possible permutation in 54
factorial (54!).

The HTML tool does not use any cookies, local storage, or any other method of persistent data. It is
strongly recommend that you thoroughly shuffle the deck, or order the deck after retrieving your
hexadecimal output to destroy the key. Reloading the browser should also be done for the same
reason.

The entirety of the randomness extraction is:

```javascript
function factorial(n) {
  // find n!
  let result = BigInt(1)
  while (n > 0) {
    result *= BigInt(n)
    n--
  }
  return result
}
function calculateLehmer() {
  let cards = []   // the shuffled deck from the user
  let lehmer = []  // the lehmer multipliers for each card
  let counter = 0  // a simple counter
  let result = BigInt(0)
  const outcome = document.getElementById('outcome')
  // get the recorded results in the lower table
  for (let i = 0; i < 54; i++) {
    let id = document.getElementById('card' + i)
    cards.push(id.childNodes[0].id)
  }
  // find the lehmer multipliers based on the above outcome
  for (let i = 0; i < 54; i++) {
    for (let j = i + 1; j < 54; j++) {
      if (cards[i] > cards[j]) counter++
    }
    lehmer[i] = counter
    counter = 0
  }
  counter = lehmer.length
  // find the factorial lehmer result using the above multipliers
  for (let i = 0; i < lehmer.length; i++) {
    counter--
    result += (BigInt(lehmer[i]) * factorial(counter))
  }

  let hex = result.toString(16)
  outcome.innerText = hex.padStart(60, '0')
  outcome.style.color = 'black'
}
```

The order of cards is in Bridge order. That is:

* **Clubs**: Ace - King = 1 - 13
* **Diamonds**: Ace - King = 14 - 26
* **Hearts**: Ace - King = 27 - 39
* **Spades**: Ace - King = 40 - 52
* **Jokers**: Red = 53, Black = 54

## Passphrase Generation
There are a few ways for generating passphrases from Deckware. As mentioned above, the first two
involve exctracting entropy from a shuffled 54-card deck, and using [Niceware][3] or [BIP39][4]:

1. [Thoroughly and sufficiently][6] shuffle the deck.
2. Drag and drop each card from the upper table to the lower table based on your shuffle.
3. Press the "Extract deck entropy" button.
4. Copy the hexadecimal string and give to Niceware or BIP39.
5. Reshuffle or order the deck to destroy the key.
6. Reload your browser to destroy the key.

The other way is to use the passphrase generator tool shipped with this project. If you have a deck
of cards, the manual process can be done as follows:

1. Shuffle the Ace through 6 of each suit (the rest of the cards are not needed).
2. Read the cards off three at a time. Record the 3 card faces and their suits.
3. Look up the index of the result from above to return one word.
4. Repeat steps 1-3 until all 24 cards have been read and 8 words have been generated.

For example, suppose the 24-card partial deck is shuffled as:

    AD,2H,AS,6S,AC,5C,2C,3C,4S,5S,3D,2D,AH,3H,4D,2S,3S,6C,4H,5H,6D,6H,4C,5D

Looking up each word index three cards at a time produces the following passphrase:

- **AD2HAS**: drafts
- **6SAC5C**: webbing
- **2C3C4S**: acting
- **5S3D2D**: vase
- **AH3H4D**: lop
- **2S3S6C**: squint
- **4H5H6D**: ping
- **6H4C5D**: rematch

The security for each generated word is:

- **1st**: log2(24 \* 23 \* 22) ≈ 13.57 bits
- **2nd**: log2(21 \* 20 \* 19) ≈ 12.96 bits
- **3rd**: log2(18 \* 17 \* 16) ≈ 12.26 bits
- **4th**: log2(15 \* 14 \* 13) ≈ 11.41 bits
- **5th**: log2(12 \* 11 \* 10) ≈ 10.37 bits
- **6th**: log2(9 \* 8 \* 7) ≈ 8.98 bits
- **7th**: log2(5 \* 4 \* 3) ≈ 5.91 bits
- **8th**: log2(3 \* 2 \* 1) ≈ 2.58 bits

The resulting passphrase has approximately 79.04 bits of security.

The word list is built by combining the following lists, sorting them and removing duplicates:

- Diceware Beale (7,776 words)
- Diceware Natural Languae Passwords nouns, 6 characters and shorter (1,000 words)
- Diceware Natural Languae Passwords adjectives, 6 characters and shorter (5,219 words)
- EFF Short list (1,296 words)
- Random 4-digit Sofie Germain prime numbers (94 numbers)

## Screenshots
The default page with an unshuffled deck:

![deckware-1][7]

Recording the shuffled deck and submitting for a hex ID:

![deckware-2][8]

## Errata
The suit symbols and jokers are emoji provided by [OpenMoji][9]. They are licensed under the
CC-BY-SA 4.0 license. The text accompanying the suit symbols and stars accompanying the jokers
however are designed by me using the DejaVu Serif font in Inkscape.

The accompanying blog post can be found at [Introducing Deckware - a 224-bit entropy
exatractor][10]. Initially, the design only extracted 224 bits with 52 cards, but I realized it
could be far more efficient adding the two jokers, and I might as well extract every possible
uniform bit out of the deck that I can.

[1]: https://github.com/skeeto/pokerware
[2]: https://diceware.com
[3]: https://github.com/diracdeltas/niceware
[4]: https://github.com/iancoleman/bip39
[5]: https://en.wikipedia.org/wiki/Lehmer_code
[6]: https://stats.stackexchange.com/a/79552
[7]: https://user-images.githubusercontent.com/699572/108796403-ee87c300-7545-11eb-8525-a24a8ef92135.png
[8]: https://user-images.githubusercontent.com/699572/108796392-e891e200-7545-11eb-97b7-ed6a2672c8de.png
[9]: https://openmoji.org/
[10]: https://pthree.org/2021/02/18/introducing-deckware-a-224-bit-entropy-extractor/
