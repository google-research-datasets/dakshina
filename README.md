# Dakshina Dataset

The Dakshina dataset is a collection of text in both Latin and native scripts
for 12 South Asian languages. For each language, the dataset includes a large
collection of native script Wikipedia text, a romanization lexicon which
consists of words in the native script with attested romanizations, and some
full sentence parallel data in both a native script of the language and the
basic Latin alphabet.

Dataset URL:
[https://github.com/google-research-datasets/dakshina](https://github.com/google-research-datasets/dakshina)

If you use or discuss this dataset in your work, please cite our paper (bibtex
citation below).  A PDF link for the paper can be found at
[https://www.aclweb.org/anthology/2020.lrec-1.294](https://www.aclweb.org/anthology/2020.lrec-1.294).

```
@inproceedings{roark-etal-2020-processing,
    title = "Processing {South} {Asian} Languages Written in the {Latin} Script:
    the {Dakshina} Dataset",
    author = "Roark, Brian and
      Wolf-Sonkin, Lawrence and
      Kirov, Christo and
      Mielke, Sabrina J. and
      Johny, Cibu and
      Demir{\c{s}}ahin, I{\c{s}}in and
      Hall, Keith",
    booktitle = "Proceedings of The 12th Language Resources and Evaluation Conference (LREC)",
    year = "2020",
    url = "https://www.aclweb.org/anthology/2020.lrec-1.294",
    pages = "2413--2423"
}
```

## Data links ##

File | Download | Version | Date | Notes
---- | :------: | :-------: | :--------: | :------
**dakshina_dataset_v1.0.tar** | [link](https://storage.googleapis.com/gresearch/dakshina/dakshina_dataset_v1.0.tar) | 1.0 | 05/27/2020 | Initial data release


## Data Organization

There are 12 languages represented in the dataset: Bangla (`bn`), Gujarati
(`gu`), Hindi (`hi`), Kannada (`kn`), Malayalam (`ml`), Marathi (`mr`), Punjabi
(`pa`), Sindhi (`sd`), Sinhala (`si`), Tamil (`ta`), Telugu (`te`) and Urdu
(`ur`).

All data is derived from Wikipedia text. Each language has its own
directory, in which there are three subdirectories:

### Native Script Wikipedia {#native}

In the `native_script_wikipedia` subdirectories there are native script text
strings from Wikipedia. The scripts are:

*   For `bn`, `gu`, `kn`, `ml`, `si`, `ta` and `te`, the scripts are named the
    same as the language,
*   `hi` and `mr` are in the Devanagari script,
*   `pa` is in the Gurmukhi script, and
*   `ur` and `sd` are in Perso-Arabic scripts.

All of the scripts other than the Perso-Arabic scripts are Brahmic. This data
consists of Wikipedia strings that have been filtered (see
[below](#native-preprocessing)) to consist only of strings primarily in the
Unicode codeblock for the script, plus whitespace and, in some cases, commonly
used ASCII punctuation and digits. The pages from which the strings come from
have been split into training and validation sets, so that no strings in the
training partition come from Wikipedia pages from which validation strings are
extracted. Files have been gzipped, and have accompanying information that
permits linking strings back to their original Wikipedia pages. For example, the
first line of `mr/native_script_wikipedia/mr.wiki-filt.train.text.shuf.txt.gz`
contains:

```
कोल्हापुरात मिळणारा तांबडा पांढरा रस्सा कुठेच मिळत नाही.
```

### Lexicons {#lexicons}

In the `lexicons` subdirectories there are lexicons of words in the native
script of each language alongside human-annotated possible romanizations for the
word. The words in the lexicons are all sampled from words that occurred more
than once in the Wikipedia training sets, in the `native_script_wikipedia`
subdirectories, and most received a romanization from more than one annotator,
though the annotated romanizations may agree. These are in a format similar to
pronunciation lexicons, i.e., single (word, romanization) pair per line in a TSV
file, with an additional column indicating the number of attestations for the
pair. For example, the first two lines of the file
`pa/lexicons/pa.translit.sampled.train.tsv` contains:

<!-- mdformat off(Don't turn TSV tabs into spaces) -->

```tsv
ਅਂਦਾਜਾ	andaaja	1
ਅਂਦਾਜਾ	andaja	2
```

<!-- mdformat on -->

i.e., two different possible romanizations for the Punjabi word `ਅਂਦਾਜਾ`, one
possible romanization (`andaaja`) attested once, the other (`andaja`) twice. For
convenience, each lexicon has been partitioned into training, development and
testing sets, with partitioning by native script word, so that words in the
training set do not occur in the development or testing sets. In addition, we
<!-- TODO(wolfsonkin): Describe how we identified lemmata and link to it. -->
used some automated methods to identify lemmata (see below) in each word, and
ensured that lemmata in words in the development and test sets were unobserved
in the training set. All native script characters -- specifically, all native
script Unicode codepoints -- in the development and test sets are found in the
training set. See below for further details on [data elicitation](#annotation)
and [preparation](#native-preprocessing). For each language there are
`*.train.tsv`, `*.dev.tsv` and `*.test.tsv` files in the subdirectory. For all
languages except for Sindhi (`sd`), there are 25,000 (native script) word types
in the training lexicon, and 2,500 in each of the dev and test lexicons. Sindhi
also has 2,500 native script word types in the dev and test lexicons, but just
15,000 in the training lexicon.

### Romanized {#romanized}

In the `romanized` subdirectory, we have manually romanized full strings,
alongside the original native script prompts for the examples. The native script
prompts were selected from the validation sets in the `native_script_wikipedia`
subdirectories (see description of preprocessing
[below](#native-preprocessing)). 10,000 strings from each native script
validation set were randomly chosen to be romanized by native speaker
annotators. For long sentences (more than 30 words), the sentences were
segmented into shorter fragments (by splitting in half until fragments are < 30
words), and each fragment romanized independently, for ease of annotation. From
this process, there are `*.split.tsv` and `*.rejoined.tsv`, which contain native
script and romanized strings in the two (tab delimited) fields. (Files with
'split' are the versions with strings >= 30 segmented; those with 'rejoined' are
not length segmented.) For example, the first line of
hi/romanized/hi.romanized.rejoined.tsv contains:

<!-- mdformat off(Don't turn TSV tabs into spaces) -->

```tsv
जबकि यह जैनों से कम है।	Jabki yah Jainon se km hai.
```

<!-- mdformat on -->

Additionally, for convenience, we performed an automatic (white space)
token-level alignment of the strings, with one aligned token per line, as well
as an end-of-string marker `</s>`. In the case that the tokenization is not 1-1,
multiple tokens are left on the same line. These alignments are provided also
with the Latin script de-cased and punctuation removed, e.g., the first seven
lines of the file `hi/romanized/hi.romanized.rejoined.aligned.cased_nopunct.tsv`
are:

<!-- mdformat off(Don't turn TSV tabs into spaces) -->

```tsv
जबकि	jabki
यह	yah
जैनों	jainon
से	se
कम	km
है	hai
</s>	</s>
```

<!-- mdformat on -->

We also performed a validation of the romanizations, by requesting that
different annotators transcribe the romanized strings into the native script of
each language respectively (see details [below](#round-trip-validation)). The
resulting native script transcriptions are provided
(`*.split.validation.native.txt`) for each language, along with a file
(`*.split.validation.edits.txt`) that provides counts of (1) the total number of
reference characters (in the original native-script strings), (2) substitutions,
(3) deletions and (4) insertions in the validation transcriptions. For example,
the first two lines of the file
`bn/romanized/bn.romanized.split.validation.edits.txt` are:

```
 LINE REF SUB DEL INS
    1 126   3   3   0
```

which indicates that the first native script string in
`bn/romanized/bn.romanized.split.tsv` has 126 characters, and there were 3
substitutions, 3 deletions and 0 insertions in the native script string
transcribed by annotators during the validation phase. Note that the comparison
involved some script normalization of visually identical sequences to minimize
spurious errors, as described in more detail [below](#native-preprocessing). All
languages fell between 3.5 and 8.5 percent character error rates of the
validation text. See [below](#round-trip-validation) for further details on this
validation process.

Finally, for convenience, we randomly shuffled this set and divided into
development and test sets, each of which are broken into native and Latin script
text files. Thus the first line in the file
`si/romanized/si.romanized.rejoined.dev.native.txt` is:

```
වැව්වල ඇළෙවිලි වැව ඉහත්තාව, වේල්ල ආරක්ෂා කිරිමට එකල සියල්ලෝම බැදි සිටියෝය.
```

and the first line of `si/romanized/si.romanized.rejoined.dev.roman.txt` is:

```
vevvala eleveli, veva ihatthava, vella araksha kirimata ekala siyalloma bendi sitiyaya.
```

Note that several hundred strings from the Urdu Wikipedia sample (and one from
Sindhi) were not from those languages, rather from other languages using a
Perso-Arabic script, e.g., Arabic, Punjabi or others. Those were excluded for
those sets, leading to less than 10,000 romanized strings.

## Native script data preprocessing {#native-preprocessing}

Let `$L` be the language code, one of `bn`, `gu`, `hi`, `kn`, `ml`, `mr`, `pa`,
`sd`, `si`, `ta`, `te`, or `ur`. The native script files are in
`$L/native_script_wikipedia`. All URLs of Wikipedia pages are included in
`$L.wiki-full.urls.tsv.gz`. This tab delimited file includes four fields: page
ID, revision ID, base URL, and URL with revision ID.

We omitted whole pages that were any of the following:

1.  redirected pages.
2.  pages with infoboxes about settlements or jurisdictions.
3.  pages with `state=collapsed` or `expanded` or `autocollapse`
4.  pages referring to `censusindia` or `en.wikipedia.org`.
5.  pages with `wikitable`.
6.  pages with lists containing more than 7 items.

Indices of pages omitted are given in `$L.wiki-full.omit_pages.txt.gz`.

For pages that are not omitted, we extract text and info files:

*   `$L.wiki-full.text.sorted.tsv.gz`
*   `$L.wiki-full.info.sorted.tsv.gz`

Text is organized by page and section within page. We then:

1. split section text by newline (leading to multiple strings per section).
2. NFC normalize.
3. sentence segment using ICU sentence segmentation (leading to multiple
   sentences per string).  The ICU sentence segmenter is initialized with
   the locale associated with the specific language being segmented.

Both tab delimited files share the same initial 6 fields: `page_id`,
`section_index`, `string_index` (in section), `sentence_index` (in string),
`include_bool`, and `text_freq`, where the `include_bool` indicates whether to
include the string or not (see below), and `text_freq` is the count of the full
section text string in the whole collection. The latter enables us to find
repeated strings as the means for determining boilerplate sections and other
things to exclude.

Both files are sorted numerically (descending) by the first three fields.

The final (7th) field of `$L.wiki-full.text.sorted.tsv.gz` is the text.

The remaining fields of `$L.wiki-full.info.sorted.tsv.gz` are: (7) depth of the
section in the page; (8) the heading level of the section; (9) the section index
of the parent section; (10) the number of words in the text; (11) the number of
Unicode codepoints in the text; (12) the percentage of Unicode codepoints
falling in category A (described below); (13) the percentage of Unicode
codepoints falling in category B (described below); and (14) the section title.

For a given native script Unicode block, we define categories A and B as
follows.  First, we identify a subset of codepoints as special symbols, call
them non-letter symbols: non-letter ASCII codepoints; Arabic full stop;
Devanagari Danda; any codepoint in the General Punctuation block; and any
digits in the current native script Unicode block. Category A are those
codepoints that (1) are outside of the native script Unicode block; and
(2) are not in the non-letter subset of codepoints. Category B are all
codepoints within the native script Unicode block.

The above-mentioned `include_bool` is set to true (when filtering) if: the
percentage of category A is below a threshold; the percentage of category B is
above a second threshold; and, finally, the percentage of whitespace-delimited
words that contain at least one codepoint from the current native script Unicode
block (and not in the non-letter subset) is above the same threshold as category
B codepoints.

For each non-empty section title, we calculate the total number of Unicode
codepoints, the total number of category A codepoints, and the fraction of
codepoints that are category A, for all sections with that title. These
statistics are stored in `$L.wiki-full.nonblock.sections.tsv.gz`, which is
sorted in descending order by total category A codepoints. Thus, the first line
of `hi.wiki-full.nonblock.sections.tsv.gz` shows the section title with the most
category A characters:

<!-- mdformat off(Don't turn TSV tabs into spaces) -->

```sh
$ gzip -cd hi.wiki-full.nonblock.sections.tsv.gz | head -1
6387096.000260	12141241	0.526066	सन्दर्भ
```

<!-- mdformat on -->

It's unsurprising the a section titled `सन्दर्भ` ('references') would have so
much non-codeblock text (mainly ASCII). It also illustrates why we track this
statistic, since we do not want to include references in the text that we are
extracting. To avoid such sections, we create a list of sections where the
aggregate percentage of category A codepoints in sections with that title is
greater than 20%. These omitted section titles are in
`$L.wiki-full.nonblock.sections.list.txt.gz`.

A second round of text extraction then occurs, omitting text occurring in
the aforementioned sections and including only individual strings that
consist of at least 85% category B codepoints, at most 10% category A
codepoints, and at least 85% of white-space delimited words containing a
within-codeblock (and not non-letter) codepoint.

All text that is extracted from a given Wikipedia page is collectively
placed in either a training or a validation set, i.e., no strings in the
validation set share a Wikipedia page with any string in the training set.
Between 23 and 29 thousand strings are placed in each validation set, which
represents a minimum of 2.25% of the data and a maximum of 26% of the data.

The data from this second iteration of extraction is present in:

*   `$L.wiki-filt.train.info.sorted.tsv.gz`
*   `$L.wiki-filt.train.text.sorted.tsv.gz`
*   `$L.wiki-filt.train.text.shuf.txt.gz`
*   `$L.wiki-filt.valid.info.sorted.tsv.gz`
*   `$L.wiki-filt.valid.text.sorted.tsv.gz`
*   `$L.wiki-filt.valid.text.shuf.txt.gz`

The first three files are training set files, the final three are validation.
The `info.sorted` and `text.sorted` files have index sorted data along the lines
described above for the full set, for both training and validation sets. We
additionally randomly shuffled the text from both sets, found in `text.shuf`.

## Annotation

### Validation string selection criteria for romanization

We randomly selected 10,000 strings from the validation set detailed
[above](#native-preprocessing) for romanization by annotators. As detailed
earlier, for strings with >= 30 Unicode codepoints, we segmented into shorter
strings for ease of annotation.

### Round-trip romanization validation {#round-trip-validation}

After eliciting manual romanizations for each of the 10,000 strings in the
selected validation sets in each language, we validated the resulting
romanizations via a second round of annotations, where the romanized
strings were provided to annotators and they were tasked with producing
the strings in the native script, which was then compared with the original
string. To compare the strings, we performed a visual normalization of both
the original and validation native script strings, so that visually identical
strings were encoded with the same codepoints for comparison. We then
calculated the number of character substitutions, deletions and insertions
for each string in the Viterbi alignment between the visually normalized
original and validation strings, including whitespace and punctuation. This,
along with the count of characters in the reference (visually normalized
original string) allows for calculation of character error rates.

<!-- TODO(wolfsonkin): Maybe split up sections more granularly in romanized. -->

As stated [above](#romanized), the languages all fell between 3.5 and 8.5
percent character error rate. The error rate could not have been 0 for a variety
of reasons:

*   Some non-codeblock text was allowed in the original native script strings,
    e.g., individual words in the Latin script, something that annotators with
    access only to the romanizations could not recover;
*   Digit strings are typically variously realized with Latin and native script
    digits, which is also not recoverable;
*   In the Perso-Arabic script in particular, tokenization in native and Latin
    scripts may be different, leading to whitespace character mismatch;
    similarly, punctuation placement sometimes leads to different tokenization;
*   Errors occurring in either the original Wikipedia or validation strings;
*   Visually identical strings can be encoded with different Unicode codepoint
    sequences, something we controlled to some extent with visual normalization,
    but other correspondences may occur; and
*   Valid spelling variation exists in the languages, e.g., for English
    loanwords, but also for common words such as "Hindi" in Devanagari, which
    can be equally well realized as either `हिन्दी` or `हिंदी`.

We provide the validation strings and character edits per string to permit
users of the resource to potentially explore methods that take such
information into account, e.g., for model evaluation.

## License
The dataset is licensed under
[CC BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/).
Any third party content or data is provided "As Is" without any warranty,
express or implied.

## Contacts
* roark [at] google.com
* ckirov [at] google.com
* wolfsonkin [at] google.com
