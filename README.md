# BNC Character N-Gram Frequencies

This directory contains frequency-sorted character n-grams derived from Adam
Kilgarriff's complete BNC World frequency list:

<https://www.kilgarriff.co.uk/BNClists/all.num.gz>

The generated files are:

- `bigrams.txt`: 2,386 unique bigrams
- `trigrams.txt`: 44,979 unique trigrams
- `tetragrams.txt`: 238,090 unique tetragrams

Each output line is tab-separated:

```text
ngram<TAB>frequency
```

## Source Format

Each source row has four whitespace-separated fields:

```text
frequency word POS file_count
```

For example:

```text
239460 there ex0 3993
88490 there av0 3684
```

The `!!WHOLE_CORPUS` row is excluded because it is corpus metadata rather than
a word.

## Extraction Logic

For every source row and each n-gram length `n` in `2`, `3`, and `4`, every
overlapping substring is extracted and credited with that row's frequency:

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

If `there` has frequency 10,000, each listed n-gram receives `+10,000`.

Repeated n-grams within a word are counted once per occurrence. For example,
`letter` contains `te` twice, so the word's frequency is added to `te` twice.

Words represented by multiple POS rows contribute every row's frequency. The
two `there` rows shown above therefore contribute a combined 327,950 to each
n-gram occurrence in `there`.

## Token Handling

Source tokens are preserved exactly:

- No stemming or lemmatization is performed.
- No letters-only filter is applied.
- No additional lowercasing is performed.
- Apostrophes, punctuation, and digits are retained.
- Underscores in BNC multiword entries such as `out_of` are retained.

The complete raw BNC list includes names, numbers, punctuation, and CLAWS
multiword tokens. A letters-only or lemmatized source would produce different
results.

## Sorting

Each file is sorted by frequency descending. N-grams with equal frequencies
are sorted lexicographically ascending for deterministic output:

```python
sorted(counts.items(), key=lambda item: (-item[1], item[0]))
```

## Validation

The generated files were independently checked to ensure:

- Every n-gram has the expected length.
- No n-gram appears more than once in a file.
- Every frequency is positive.
- Frequencies are sorted descending, with lexicographic tie-breaking.
- Summed output frequencies equal the source-derived total of `frequency *
  number_of_overlapping_positions`.

Validated weighted totals:

```text
Bigrams:     370,420,373
Trigrams:    273,779,389
Tetragrams:  194,732,499
```
