# Washington Irving, *Sleepy Hollow*
## By Sarah M Johnson

A citable digital edition.

**Status**: <span style="color: red;">In progress</span>.

## A Short Story

Text: `urn:cts:fuTexts:irving.lsh.johnson:`

*Sleepy Hollow* is often referred to as the first American short story.  It is an Americanized version of a German folktale and was published in 1819-20 by The Sketch Book, which is a short-story collection by Washington Irving. It focuses on a tale of a headless horseman that haunts the town of Sleepy Hollow.  

Washington Irving was a writer that is sometimes called the  "first American man of letters", best known for his works "The Legend of Sleepy Hollow" and "Rip Van Winkle".

Basic Bibliography

* The Editors of Encyclopedia Britannica, "Washington Irving, American Author" https://www.britannica.com/biography/Washington-Irving



A plain-text, citable edition following the protocols of the [CITE Architecture](http://cite-architecture.org). The file in `text/SleepyHollow.cex` is intended for machine-processing and has been validated as to character-set and spelling.

There is a human-readable HTML site, derived from the `.cex` file, in `html/`. The scripts that generated that site are in `/src/main/scala/`.

Included Scala scripts take advantage of the code libraries in the [CITE Architecture](http://cite-architecture.org).

<br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>
<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a>

Edited, 12/16/2019

## Running Scripts for Validation, Publication, and Analysis

This repository is an SBT project for running Scala code. Everything should be reproducible.

### Requirements

- A Java JDK 1.8 or higher.
- [SBT](https://www.scala-sbt.org) Installed and on the PATH.

### Running

- Clone this repository.
- Navigate to this repository's root level.
- `$ sbt console`
- `scala> :load scripts/character-validation.sc`
- etc.

## Code Contents


## Character Validation

The text has been machine validated as to character-set using [a Scala script](https://github.com/smjohn44/CSC270-work/blob/master/scripts/corpus-char-validate.sc). The following is a complete inventory of the characters present in this text:

| Character | Character | Character | Character | Character |
|-----------|-----------|-----------|-----------|-----------|
| `space` (20) | `!` (21) | `(` (28) | `)` (29) | `,` (2c) |
| `-` (2d) | `.` (2e) | `:` (3a) | `;` (3b) | `?` (3f) |
| `A` (41) | `B` (42) | `C` (43) | `D` (44) | `E` (45) |
| `F` (46) | `G` (47) | `H` (48) | `I` (49) | `J` (4a) |
| `K` (4b) | `L` (4c) | `M` (4d) | `N` (4e) | `O` (4f) |
| `P` (50) | `R` (52) | `S` (53) | `T` (54) | `U` (55) |
| `V` (56) | `W` (57) | `Y` (59) | `Z` (5a) | `a` (61) |
| `b` (62) | `c` (63) | `d` (64) | `e` (65) | `f` (66) |
| `g` (67) | `h` (68) | `i` (69) | `j` (6a) | `k` (6b) |
| `l` (6c) | `m` (6d) | `n` (6e) | `o` (6f) | `p` (70) |
| `q` (71) | `r` (72) | `s` (73) | `t` (74) | `u` (75) |
| `v` (76) | `w` (77) | `x` (78) | `y` (79) | `z` (7a) |
| `|` (7c) | `à` (e0) | `é` (e9) | `ê` (ea) | `—` (2014) |
| `’` (2019) | `“` (201c) | `”` (201d) |

Confirm character-validation with:

~~~
$ sbt console
scala> :load scripts/character-validation.sc
~~~

This will generate a file `validation-data/charTable.md` containing each distinct character present in the text, with its Unicode value.

## Spelling Validation

This English translation of Irving's *Sleepy Hollow* has been spell-checked against two files. One is [a standard English word-list](https://github.com/smjohn44/CSC270-work/tree/master/validation-data/SCOWL-wl) generated from the [SCOWL](http://wordlist.aspell.net) online tool. The second is [a user-dictionary](https://github.com/smjohn44/CSC270-work/blob/master/validation-data/userDictionary.txt).

The spell-check script at [/scripts/corpus-spelling0.sc](https://github.com/smjohn44/CSC270-work/blob/master/scripts/corpus-spelling0.sc).

Confirm spelling validation with:

~~~
$ sbt console
scala> :load scripts/corpus-spelling0.sc
~~~

## Ngram Analysis

An NGram is a recurring pattern of N-number of words. This repository includes a basic Scala script showing how NGram analysis can work with, and be enhanced by, the [CITE Architecture](http://cite-architecture.org).

Running:

~~~
$ sbt console
scala> :load scripts/ngrams.sc
~~~

This script analyzes the text for 3-grams.

1. It generates a *citable tokenized exemplar* of the text, divided into word-tokens, with punctuation removed, and all words lower-cased: `noPuncCorpus`.
1. It creates a Vector of all possible patterns of 3 words, as a `Vector[ (CtsUrn, String) ]`, with citations keyed to `noPuncCorpus`: `ngt`.
1. It creates a Histogram of those 3-Grams, consisting of a `Vector[ (String, Int) ]`, sorted by frequency (most common NGrams at the bottom): `ngh`.

See the histogram with: `scala> showMe(ngh)`

Find all citations to occurances of one Ngram with, e.g.:

~~~scala
scala> val oneNG: Set[CtsUrn] = urnsForNGram( "the administration of", ngt)
scala> showMe(oneNG)
~~~

If we ask for NGrams where `n=1`, we simply get a list of the vocabulary for this text, sorted by frequency:

~~~scala
scala> val vocab = makeNGramTuples(1, noPuncCorpus)
scala> val vocabHisto = makeNGramHisto(vocab)
~~~

The script includes a function `takePercent( histo: Vector[(String, Int)], targetPercent: Int)`. This is a tail-recurive method that will take, starting with the most frequent, items from a histogram that add up to a desired percentage of all occurrances. In other words, "What English words do I need to know to recognize 50% of the words in this text?":

~~~scala
scala> val halfOfAllWords = takePercent(vocabHisto, 50)
scala> showMe(halfOfAllWords)
~~~
