# Deckware
Deckware is a 236-bit randomness extractor, returning the randomness in the shuffle of a 54-card
deck of standard playing cards (52 cards and 2 jokers). It's inspired by [Pokerware][1], which is in
turn inspired by [Diceware][2]. However, Deckware does not ship a word list. Instead, it returns a
236-bit hexadecimal string for you do do with as you please. One use could be converting that
hexadecimal string into a 14-word [Niceware passphrase][3], or as the entropy for a [Bitcoin BIP39
mnemonic][4]

The entire tool is self-contained in a single HTML file which can and should be executed offline by
opening the file locally in your browser. It does not require any external dependencies, and does
not ship any JavaScript frameworks or libraries.

A sufficiently shuffled deck of 54 playing cards produces log2(54!) ~= 237.0638 bits of entropy.
Deckware uses [Lehmer Code][5] to assign a unique number to every possible permutation in 54
factorial (54!). However, to remain uniform, any identifier that is larger than 2^237-1 is rejected,
and the user will need to reshuffle the deck. This is expected to happen about 1 in every 25
shuffles.

The HTML tool does not use any cookies, local storage, or any other method of persistent data. It is
strongly recommend that you thoroughly shuffle the deck, or order the deck after retrieving your
hexadecimal output to destroy the key. Reloading the browser should also be done for the same
reason.

The entirety of the randomness extraction is

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
  // discard anything 2^237 or greater
  if (result > 0x1fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffn) {
    outcome.innerText = 'Outcome will not be uniform. Reshuffle.'
    outcome.style.color = 'red'
  }
  // accept 2^237-1 or smaller, and return only the lower 236 bits
  else {
    result &= 0xfffffffffffffffffffffffffffffffffffffffffffffffffffffffffffn
    let hex = result.toString(16)
    outcome.innerText = hex.padStart(59, '0')
    outcome.style.color = 'black'
  }
}
```

The order of cards is in Bridge order. That is:

* **Clubs**: Ace - King = 1 - 13
* **Diamonds**: Ace - King = 14 - 26
* **Hearts**: Ace - King = 27 - 39
* **Spades**: Ace - King = 40 - 52
* **Jokers**: Red = 53, Black = 54

## Passphrase Generation
1. [Thoroughly and sufficiently][6] shuffle the deck.
2. Drag and drop each card from the upper table to the lower table based on your shuffle.
3. Press the "Extract deck entropy" button.
4. Copy the hexadecimal string and give to [Niceware][3] or [BIP39][4].
5. Reshuffle or order the deck to destroy the key.
6. Reload your browser to destroy the key.

## Screenshots
The default page with an unshuffled deck:

![deckware-1][7]

Recording the shuffled deck and submitting for a hex ID:

![deckware-2][8]

## Errata
The suit symbols are emoji provided by [OpenMoji][9]. They are licensed under the CC-BY-SA 4.0
license. The text accompanying the suit symbols however is designed by me using the DejaVu Serif
font in Inkscape.

The accompanying blog post can be found at [Introducing Deckware - a 224-bit entropy exatractor][10].

[1]: https://github.com/skeeto/pokerware
[2]: https://diceware.com
[3]: https://github.com/diracdeltas/niceware
[4]: https://github.com/iancoleman/bip39
[5]: https://en.wikipedia.org/wiki/Lehmer_code
[6]: https://stats.stackexchange.com/a/79552
[7]: https://user-images.githubusercontent.com/699572/108796392-e891e200-7545-11eb-97b7-ed6a2672c8de.png
[8]: https://user-images.githubusercontent.com/699572/108796403-ee87c300-7545-11eb-8525-a24a8ef92135.png
[9]: https://openmoji.org/
[10]: https://pthree.org/2021/02/18/introducing-deckware-a-224-bit-entropy-extractor/
