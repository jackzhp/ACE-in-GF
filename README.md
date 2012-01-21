Attempto Controlled English in Grammatical Framework
====================================================

Introduction
------------

This project implements the syntax of Attempto Controlled English (ACE)
(version 6.6) in Grammatical Framework (GF) and ports it to
additional natural languages (_Ger_, _Ita_, ...).
Note that this project does not implement the ACE interpretation
rules or the mapping of ACE sentences to discourse representation structures.


Requirements
------------

### Coverage

The grammar should cover a reasonably large and interesting subset of ACE,
specifically the OWL-compatible subset that is supported by AceWiki.
Ideally, the grammar should cover the full ACE.

### Correctness

The grammar should not overgenerate. Since the grammar is going to
be used in a look-ahead editor (which explicitly exposes the coverage
of the grammar to the user), it should not support forms that fall
outside of ACE.

### Multilinguality

The grammar should allow for bidirectional translations
between ACE and a number of other controlled natural languages
(_ACE-like_ German, Italian, Finnish, etc.).


Reference implementations
-------------------------

  * The reference ACE parser (APE) can be obtained from <https://github.com/Attempto/APE>. There is a web-based demo at <http://attempto.ifi.uzh.ch/ape/>.

  * The reference AceWiki grammar can be found at <https://github.com/AceWiki/AceWiki/tree/master/src/ch/uzh/ifi/attempto/acewiki/aceowl/>. There is a web-based demo at <http://attempto.ifi.uzh.ch/acewiki/>.


First commit
------------

The first commit was based on the 2011-12-14 version of
the GF darcs repository `examples/attempto/`-directory implemented
by the GF developers in 2009 targeting ACE v6.0. See also the publication:

	K. Angelov and A. Ranta.
	Implementing Controlled Languages in GF.
	N. Fuchs (ed.), CNL-2009 Controlled Natural Languages, LNCS/LNAI 5972, 2010.

This version does not completely match ACE v6.6,
i.e. some ACE constructs are not supported, e.g.

  * transitive adjectives: `mad-about` (`mad about` does not seem to work either)
  * exactly
  * less than
  * everybody
  * somebody X
  * somebody does
  * somebody who
  * Mary who
  * X who
  * `which` as a question pronoun
  * `is not` and `isn't` are not equivalent
    * `a woman is not a man .` fails
    * `a woman isn't a man .` succeeds
  * `are not` and `aren't` are not equivalent
  * `does not` and `doesn't` are not equivalent
  * `do not` and `don't` are not equivalent
  * `who` (instead of `whom`) in object relative clauses (`a woman who a man sees`)
  * dative shift (`John gives Mary an apple`)
  * ...

and it supports some constructs which in ACE do not exist, have been
deprecated or should be avoided (i.e. create a warning), e.g.

  * he waits . (and other unresolvable personal pronouns)
  * the man waits . (gives a warning in APE)
  * a man X is the man Y .
  * not more than, not at least, ...
  * numbers larger than 12 as words, e.g. `one hundred and thirty`
  * whom
  * `- ( X + X ) waits .` (minus sign should be followed by a number)
  * ...


Building
--------

_Note: only Eng and Ger are currently supported_

In order to build the PGF-file execute:

> ant clean

> bash make-pgf.bash

The GF libraries are expected to be found in a system-wide location, e.g.:

  * ~/.cabal/share/gf-3.3/lib/present/
  * ~/.cabal/share/gf-3.3/lib/prelude/

In addition to building the PGF-file (`TestAttempto.pgf`), `make-pgf.bash`

  * generates some random example sentences and
  * converts the PGF into a speech recognition grammar format (JSGF).

Both these additional outputs are only for testing purposes.
All outputs are written into the `build`-directory.

### Known problems

  * random generation often gets stuck
  * JSGF produces zero-output with the error message `gf: mergeIdentical: Unknown_100_0`


Running
-------

Example of translating an English sentence to German.

	$ gf build/pgf/TestAttempto.pgf
	TestAttempto> p -lang=Eng "John gives an apple to Mary ." | l -lang=Ger
	John gibt einen Apfel Mary .


Testing
-------

### Parsing with GF

Parsing ACE sentences with `TestAttempto.pgf`.

> ghc --make -o Parser Parser.hs

> cat examples/ace.txt | ./Parser build/pgf/TestAttempto.pgf

or

> ./run_test.sh examples/ace.txt

which creates two files

  * test_out.txt
  * test_out_fail.txt

To run tests on all the test cases in the tests-directory

> sh run_tests.sh

The output files are created into the subdirectories of the test-directory.

### Generating with GF and parsing with APE

See the scripts and instructions in the `tools`-directory.


Status
------

Status of this project in terms of ACE-compliance of the GF grammars
measured in different ways. We look at:

  * various ACE subsets
  * GF parsing
  * GF generation
  * GF translation correctness (?)

_Note: So far we have only looked at how many known ACE sentences the GF parser can parse._


### AceWiki supported fragment of ACE OWL

AceWiki test set obtained by exhaustive generation with the Codeco grammar.
Content words: ask, Mary, woman, friend, mad-about.

  * Total: 19718
  * Parsed: 5609
  * NOT parsed: 14109
  * Runtime: ~39 sec

Examples of parsed:

  * for every woman it is false that a woman is the woman .
    * für jede Frau ist es falsch daß eine Frau die Frau ist .

A few reasons (i.e. words and phrases) that cause the parse to fail:

  * mad-about (`mad about` does not seem to work either)
  * somebody X
  * somebody does
  * somebody who
  * Mary who
  * X who
  * is/are not
  * does/do not
  * which (as a question pronoun)


### ACE Editor

_Work in progress_


### Full ACE

_Work in progress_

Since there is no generator, we use the APE regression test set.
We take all the snippets that APE parsed correctly into a non-empty DRS.
Some normalization is performed, e.g.

  * sentence-initial function words are lowercased
  * punctuation marks are separated from the words

> ~/mywork/APE/examples$ swipl -s output_tests.pl -t halt -g main > ace.txt

> ~/mywork/ACE-in-GF$ ./run_test.sh ../APE/examples/ace.txt

  * Parsed: 421
  * NOT parsed: 2351

Most failures are caused by missing content words in the GF ACE test lexicon
(which is easy to fix). The most frequent failures however feature some syntactic
structures and function words which the GF ACE implementation does not support:

     68 there is a man who
     46 which
     26 John in
     24 John likes
     23 a man who
     22 what
     20 John does
     17 every man who
     16 a man runs
     14 a man does
     12 there is a red
     12 John beats
     12 if there is a man then he sees a cat
     12 if a user
     12 how
     10 there is a cat
     10 not for
     10 John sees a man who
     10 John owns
     10 in
     10 everything
     10 a man hits
      9 Mary who
      9 John can not
      9 everybody who

This is a frequency ranking of failing partial sentences, from the first word to the word
that caused the parse to fail.


Structure of the grammar
------------------------

### General

  * Attempto
    * abstract Attempto = Numeral, Symbols
  * Symbols
    * abstract Symbols
    * ACE formulas and expressions
  * SymbolsC
    * concrete SymbolsC of Symbols = open Precedence, Prelude
    * used by all languages
  * AttemptoI
    * incomplete concrete AttemptoI of Attempto = SymbolsC, Numeral ** open Syntax, Symbolic, Prelude, LexAttempto
  * LexAttempto
    * interface LexAttempto = open Syntax
    * things that are not part of the RGL
  * TestAttempto
    * abstract TestAttempto = Attempto
    * adds test vocabulary

### Language-specific (e.g. Eng)

  * AttemptoEng
    * concrete AttemptoEng of Attempto = SymbolsC,NumeralEng ** AttemptoI ...
  * LexAttemptoEng
    * instance LexAttemptoEng of LexAttempto
  * TestAttemptoEng
    * concrete TestAttemptoEng of TestAttempto = AttemptoEng ** open SyntaxEng, ParadigmsEng, IrregEng ...
