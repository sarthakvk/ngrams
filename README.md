# N-Gram Frequencies for Typing Practice

A frequency-ranked dataset of English character bigrams, trigrams, and
tetragrams for typing practice. The lists can be used with typing tools such
as [Monkeytype](https://monkeytype.com/) or with custom typing trainers.
All alphabetic characters in the dataset are lowercase.

## Dataset

| N-gram | Full frequency data | Top 100 | Top 200 |
| --- | ---: | ---: | ---: |
| Bigrams | [`bigrams.txt`](bigrams.txt) (2,386) | [`bigrams-top-100.txt`](bigrams-top-100.txt) | [`bigrams-top-200.txt`](bigrams-top-200.txt) |
| Trigrams | [`trigrams.txt`](trigrams.txt) (44,979) | [`trigrams-top-100.txt`](trigrams-top-100.txt) | [`trigrams-top-200.txt`](trigrams-top-200.txt) |
| Tetragrams | [`tetragrams.txt`](tetragrams.txt) (238,090) | [`tetragrams-top-100.txt`](tetragrams-top-100.txt) | [`tetragrams-top-200.txt`](tetragrams-top-200.txt) |

The top-100 and top-200 files contain a single space-separated line of n-grams,
ordered from most to least frequent:

```text
th he in er an re on en at nd ...
```

The full files contain every unique n-gram and its frequency as tab-separated
values:

```text
th	12641350
he	11282123
in	8851244
```

## Source

The dataset is derived from Adam Kilgarriff's complete
[BNC World frequency list](https://www.kilgarriff.co.uk/BNClists/all.num.gz),
which lists words from the British National Corpus by frequency.

## Generation

For every word in the source data, each overlapping character sequence of
length 2, 3, and 4 is extracted. The word's frequency is then added to the
frequency of each sequence.

```python
for index in range(len(word) - n + 1):
    ngram = word[index:index + n]
    counts[n][ngram] += frequency
```

For example, `there` produces:

```text
Bigrams:    th he er re
Trigrams:   the her ere
Tetragrams: ther here
```

If `there` has a frequency of 10,000, each sequence receives 10,000. Repeated
sequences are counted at every position, and entries for the same word are
combined across parts of speech.

The full files are sorted by frequency in descending order. N-grams with the
same frequency are sorted lexicographically.
