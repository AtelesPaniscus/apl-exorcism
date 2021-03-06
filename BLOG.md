<!-- apl-exalt by NewForester:  Exercism-style exercises in APL and a test case generator in Python 3 -->

## Blog

This is a record/summary of the development work that went into the EGP.

It is a reminder that what might look simple usually isn't.
Doubly so when there is a canonical representation that is supposed to support this kind of thing.

It is also a reminder of the issues that had to be solved just in case the EGP need major revision or even rewriting in another language.


### Week 1

The task for the first week was to build an EGP to regenerate the seven exercises that had previously been built by hand by the GNU APL track team.
This was done because these are the only exercises for which there is a reference
against which the output of the EGP may be compared.

The seven exercises were:

  * hello-world
  * leap
  * hamming
  * rna-transcription
  * raindrops
  * difference-of-squares
  * pangram

These are simple exercises that provide good examples of the `canonical-data.json` input the EGP needs to handle
(in particular the 'nesting' of 'cases').

It was felt necessary to introduce flags for the EGP to control the output and/or provide information missing from `canonical-data.json` or
override the information present.

First, some exercises have 'bad' test cases.
What error should the EGP expect ?
In the seven exercises, 'hamming' solutions are expected to generate LENGTH ERROR and 'rna-transcription' solutions DOMAIN ERROR.
The '`-e`' flag was added to tell the EGP which error to expect (and provide the means to generate the error).

Second, the '`-n`' (for parameter name) was added.
For dyadic functions ('hamming'), this flag may be used to ensure the parameters are the way round humans might expect them to be.
For monadic functions ('leap'), this flag may be used to override the some times lame parameter name used in the `canonical-data.json` file.

Third the '`-t`' (for types) was added to indicate the type of function parameters and their results.
The EGP needs to enclose strings and characters in apostrophes and to use 0 and 1, not False and True, for Booleans.
It may be possible for the EGP to deduce the types correctly most of the time
so this flag was first introduced to handle cases where the deduction was incorrect or inappropriate.
However, always specifying the types explicitly is more consistent so the flag quickly became expected.


### Week 2

The exercises undertaken during week 2 were:

  * isogram
  * collatz-conjecture
  * rotational-cipher
  * atbash-cipher
  * rail-fence-cipher
  * sieve
  * grains

It was expected with these exercises that a couple of small adjustments might be made to the EGP.
The hind sight impression is that each required a change.
The most challenging of which arose from 'sieve' and 'grains'.

Exercism requires some sort of test framework.
Many programming languages now have an xUnit testing framework along the lines of Junit, pyUnit and so forth.
Other Exercism language tracks take advantage of this but Exercism does not require anything so sophisticated.

So far as I know, APL has no xUnit framework.
GNU APL does have a test case framework and the Exercism track uses this.

The GNU APL framework, for those familiar with Python, is more like pyDoc than pyUnit.
It is, I think, meant for regression testing of GNU APL itself.
This has advantages and disadvantages.
None of the latter, on their own, is enough to warrant building something else.

The framework checks the actual output against the expected output letter-by-letter, line-by-line.
If the function under test outputs so much as an extra blank line in one test case, the test case fails and so do all the subsequent test cases.

The last test case of 'sieve' produces a list of primes up to 1000.
This is too long to fit on one line so, to pass, the EGP should mimic GNU APL's line-wrapping.
Hand corrections are tedious.
Neither solution is 'portable'.

The last two test cases of 'grains' fail because GNU APL chooses to print numbers (including integers) larger than 2^53 in real number notation.
I could find no mechanism in GNU APL to alter this behaviour.

The match (`≡`) primitive of APL allows these problems to be side-stepped.  It also allows comparison of the rank (and other otherwise
invisible differences) of the actual and expected test results.
There is a drawback:  the actual test output is not printed, which hampers debugging.

With xUnit test frameworks, a routine or macro is used to compare actual with expected test results.
The advantage is that the routine or macro can do all sorts of extra stuff if need be.
Thus the function `assert∆match` was introduced.
It does the match and if that fails, prints out the actual test result.

The EGP was changed to use `≡` by default except for functions with a Boolean result.
The flag `-c` (for compare) may be used to override this.
Currently, `-c=a` tells the EGP to use `assert∆match`.
Current policy is to use `-c=a` for all exercises except for those with function(s) with a Boolean result.

This is a stop gap measure that sits uncomfortably with the existing test harness.
As yet not enough exercises have been done to enable a more permanent, informed, choice to be made.


### Week 3

The exercises undertaken during week 3 were:

  * anagram
  * triangle
  * roman-numerals
  * perfect-numbers
  * nth-prime
  * diamond
  * bob

This a varied lot of exercises several of which challenged the EGP as was.

The 'anagram' exercise was the first to return a vector (of strings as it happens).
The EGP is able to detect this from the presence of lists in the `canonical-data.json` file.

This exercise brought another issue to the forefront.
In APL, `1` is an scalar, `,1` is an vector but, when printed, both are represented by `1`.
This sometimes is convenient and other times inconvenient.

The GNU APL test case harness compares letter-for-letter so a function may return either a scalar or a vector length 1 for this special case.
However, if another function wants to use the result of the first this can be a real bind.

Policy is now that:

  * a string of length one is represented by `,'x'` and the scalar `'x'` cannot be substituted;
  * an array of strings of length one is represented by `,⊂'hello'` and the scalar string `'hello'` cannot be substituted.

When a similar issue arises for numbers, the situation will be reviewed but it is likely the same policy will be applied.

Hand modification of `perfect-numbers.apl` was required:  the definition of an 'enumeration' `{deficient, perfect, abundant}` is beyond the scope of the EGP.

Hand modification of `bob.rc` was required:  the multi-line test cases were removed.
I do not think APL can handle multi-line 'terminal' input.

The 'diamond' exercise is unusual in that it 'draws' a shape.
Its test cases are better using the existing letter-by-letter comparison, which is now directed by the `-c=r` flag.


### Week 4

The exercises undertaken during week 4 were:

  * acronym
  * variable-length-quantity
  * run-length-encoding
  * crypto-square
  * armstrong-numbers
  * two-fer
  * reverse-string

Some of these I have not done before.  They were all simple.

The 'variable-length-quantity' exercise led to the introduction of a hexadecimal string type.
It also led to consideration of deliberately generating a file that could not be used 'as is' to force hand correction but the idea was dropped.

For the 'two-fer' exercise the `-p=name` flag was introduced to override the function name provided in the `canonical-data.json` file.
The 'two-fer' exercise was the first with an 'optional' parameter.
You cannot have optional parameters in APL.
It was a case of substitute `''` or drop the exercise altogether.


### Week 5

The exercises undertaken during week 5 were:

  * phone-number
  * connect
  * allergies

These were done to finish off the month of January.
These again illustrate the thin line this project walks.

The 'phone-number' exercise was the first where the possibility of different 'bad' test cases needing to generate different APL errors arises.
Since this is beyond the scope of the current EGP design, it was decided not to try this.
All test cases produce 'DOMAIN ERROR'.  I suppose this is a candidate for 'hand modification'.

The 'connect' exercise challenged me, not the EGP.
APL is great for expressing solutions to certain classes of problems in just a couple of lines.
For other classes of problem, a couple of lines is not sufficient.
Writing and debugging a solution quickly becomes very difficult, especially with limited and buggy support for (lambda ?) functions.

The 'allergies' exercise presented two challenges.

First, it introduces new semantics in the `canonical-data.json` file.
This has been special cased in a general manner in the EGP but to do this the EGP test case generation code was heavily refactored.

Second, it is not the only exercise that requires the student implement more than one function but it is the only one so far where the two functions have different signatures.
Since this is beyond the scope of the current EGP design, but could not be solved by hand correction.
For now a hack is hard-coded in the 'new semantics' special case code.


### February 2018

The exercises undertaken during February were:

  * beer-song
  * food-chain
  * twelve-days
  * pig-latin
  * nucleotide-count
  * say
  * palindrome-products
  * sum-of-multiples
  * wordy

These were chosen so that some extension of the EGP would be necessary but not too much in too short a space of time.

The `beer-song`, `food-chain` and `twelve-days` exercises all produced lots of output that cannot be passed on one line to `assert∆match` for comparison.
It was decided to let the test harness compare the output a line at a time.
The EGP recognises `-c=v` as indicating it should select `assert∆verse` instead of `assert∆match`.
All `assert∆verse` does it print the solution's output (a vector of lines of verse) one line at a time for the benefit of the test harness and students.
The EGP recognises `-pw=` and generates code to set the GNU APL print width (⎕PW) to a suitably large value that means the interpreter does not wrap the lines output.

The second set of extensions came with `nucleotide-count` and `palindrome-products`,
which seem to call for solutions that return more than just a simple scalar or vector.
`HINTS.md` files were added to tell students what their solutions must return to pass the test cases and the EGP altered to tell students to read the hints.

The `canonical-data-json` file for the `nucleotide-count` exercise implies solutions return a collection of `{nucleotide, count}` pairs,
although the canonical `description.md` would allow just a list of counts.
The EGP accepts `-t=cn` as indicating an array with rows that contain a character and an integer.

The canonical `description.md` for the `palindrome-products` exercise says solutions must return both the product and its factors.
APL functions can have only one result.
The EGP accepts `-r` as indicating which (`=factors` in this case).

For both these exercises, the EGP selects `assert∆match` but produces code to reshape the reference data for comparison.

The `sum-of-multiples` exercise forced a small rethink of the `-n` flag.
For exercises with 2 parameters, this flag may be used to either swap the parameters (wrt to `canonical-data-json`) or rename the parameters:
it cannot do both.
[Ed. I think it is used to rename the parameters).

### March 2018

The very first exercise attempted in March 2018 hit the loopy bug in GNU APL's test case mode.
Progress ground to a halt:  lots more interesting things to do.

#### April 2018

No activity.

#### May 2018

At the very end of May, to save the project, the `slugtest` workaround was implemented.
See [RUNNING_TESTS](RUNNING_TESTS.md)

The first pass concatenated together:

  * solution under test
  * the Exercism test harness
  * the test cases

A number of input lines had to be disabled.

The test harness was simple (and is no way thought to be at fault).
The use of concatenation in _bash_ meant that all the FIO in the test harness could be removed.
FIO is a new (aka not stable) feature in GNU APL that is not standard.

All that was left was the `test∆try` function:
an alternative implementation of this is used and the Exercism test harness is dropped.

This function is essential for exercise with 'bad' test cases but uses `⎕EC`,
which is known to have problems when used in conjunction with the GNU APL test case mode.

---

*apl-exalt* by NewForester.
Notes licensed under a Creative Commons Attribution-ShareAlike 4.0 International Licence.

<!-- EOF -->
