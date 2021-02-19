# DeckWare
Generate 224-bits of entropy from a shuffled deck of playing cards calculated using [Lehmer
Code][1]. Because log2(52!) ~= 225.581, about 40% of random shuffles will be rejected requiring the
user to reshuffle the deck. Inspired by [Pokerware][2].

[1]: https://en.wikipedia.org/wiki/Lehmer_code
[2]: https://github.com/skeeto/pokerware
