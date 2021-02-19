# Deckware
Deckware is a 224-bit randomness extractor, returning the randomness in the shuffle of a 52-card
deck of standard playing cards. It's inspired by [Pokerware][1], which is in turn inspired by
[Diceware][2]. However, Deckware does not ship a word list. Instead, it returns a 224-bit
hexadecimal string for you do do with as you please. One use could be converting that hexadecimal
string into a 14-word [Niceware passphrase][3].

[1]: https://github.com/skeeto/pokerware
[2]: https://diceware.com
[3]: https://github.com/diracdeltas/niceware

The entire tool is self-contained in a single HTML file which can and should be executed offline by
opening the file locally in your browser. It does not require any external dependencies, and does
not ship any JavaScript frameworks or libraries.

A sufficiently shuffled deck of 52 playing cards produces log2(52!) ~= 225.581 bits of entropy.
Deckware uses [Lehmer Code][4] to assign a unique number to every possible permutation in 52
factorial (52!). However, to remain uniform, any identifier that is larger than 2^225-1 is rejected,
and the user will need to reshuffle the deck. Unfortunately, this is expected to happen about 40% of
the time.

[4]: https://en.wikipedia.org/wiki/Lehmer_code

The HTML tool does not use any cookies, local storage, or any other method of persistent data. It is
strongly recommend that you thoroughly shuffle the deck, or order the deck after retrieving your
hexadecimal output to destroy the key. Reloading the browser should also be done for the same
reason.

## Passphrase Generation
1. [Thoroughly and sufficiently][5] shuffle the deck.
2. Drag and drop each card from the upper table to the lower table based on your shuffle.
3. Press the "Calculate unique deck ID" button.
4. Copy the hexadecimal string and give to [Niceware][3].
5. Reshuffle or order the deck to destroy the key.
6. Reload your browser to destroy the key.

[5]: https://stats.stackexchange.com/a/79552

## Screenshots
The default page with an unshuffled deck:

![deckware-1][6]

[6]: https://user-images.githubusercontent.com/699572/108456551-c50d2580-722d-11eb-9f9d-f1ac45ba9084.png

Recording the shuffled deck and submitting for a hex ID:

![deckware-2][7]

[7]: https://user-images.githubusercontent.com/699572/108456565-cb030680-722d-11eb-96a7-f8609ba24819.png

## Errata
The suit symbols are emoji provided by [OpenMoji][8]. They are licensed under the CC-BY-SA 4.0
license. The text accompanying the suit symbols however is designed by me using the DejaVu Serif
font in Inkscape.

[8]: https://openmoji.org/

The accompanying blog post can be found at [Introducing Deckware - a 224-bit entropy exatractor][9].

[9]: https://pthree.org/2021/02/18/introducing-deckware-a-224-bit-entropy-extractor/
