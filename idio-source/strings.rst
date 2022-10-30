.. include:: ./global.rst

######################
Strings and Characters
######################

*******************
Unicode Code Points
*******************

The "characters" in strings (and as standalone values) are Unicode
code points, normally represented by ``#U+...`` for enough hexadecimal
digits to represent the code point.  Leading zeroes are not required
(but may be necessary, see below).

You can input a specific character with ``#\X`` for some *X*, a UTF-8
code point.

There are a limited number of ``#\{newline}`` *named* characters

.. code-block:: idio
   :caption: :file:`unicode.idio`

   ;; ħ is U+0127 LATIN SMALL LETTER H WITH STROKE
   c1 := #U+127

   c2 := #\ħ

   ;; the unicode type is much like fixnum and can be compared with eqv?
   printf "Does ``%s'' eqv? ``%s''? %s\n" c1 c2 (eqv? c1 c2)

   ;; SPACE
   c1 = #U+20

   ;; or using an escape sequence
   c2 = #\{space}

   printf "Does ``%s'' eqv? ``%s''? %s\n" c1 c2 (eqv? c1 c2)

   ;; tell me what you know!
   unicode/describe c1

.. code-block:: console

   $ idio unicode
   Does ``ħ'' eqv? ``ħ''? #t
   Does `` '' eqv? `` ''? #t
   0020;;Zs;;;;;;;;;;;; # Separator White_Space 

*******
Strings
*******

Strings are the usual ``"``-delimited sequences of "characters", here,
Unicode code points.  Normal escape sequences are available: ``\n``,
``\t`` etc. and ``\u...`` (for up to four digit hex sequences) and
``\U...`` (for up to eight digit hex sequences).

There is an additional ``\x..`` (for up to two digit hex sequences)
which is only useful for constructing non-UTF-8 pathnames.

Multi-line strings are perfectly reasonable.

.. code-block:: idio
   :caption: :file:`strings.idio`

   s1 := "hello\nworld"

   s2 := "hello
   world"

   printf "Does ``%s'' equal? ``%s''? %s\n" s1 s2 (equal? s1 s2)

   ;; enter one directly (source files are UTF-8)
   s1 = "ħello"

   ;; or using an escape sequence
   s2 = "\u0127ello"

   printf "Does ``%s'' equal? ``%s''? %s\n" s1 s2 (equal? s1 s2)

   ;; Note that you only need pass as many hex digits as necessary to
   ;; distinguish the code point so long as the following characters are
   ;; not possible hex digits.
   ;;
   ;; We can't say \u127ello as the "up to four digits" will consume 127e
   ;; which is ቾ (U+127E ETHIOPIC SYLLABLE CO).
   ;;
   ;; In the next example U+0050 LATIN CAPITAL LETTER P can be reduced to
   ;; \u50 because the next code point is l (U+006C LATIN SMALL LETTER L)
   ;; which is not a hex digit.
   s1 = "\u50lay time!"

   ;; unicode/describe works for strings too which is helpful when the
   ;; visual representation is confusing, for example, this is not é
   ;; (U+00E9 LATIN SMALL LETTER E WITH ACUTE)
   unicode/describe "é"

.. code-block:: console

   $ idio strings
   Does ``hello
   world'' equal? ``hello
   world''? #t
   Does ``ħello'' equal? ``ħello''? #t
   0065;;Ll;;;;;;;;;;0045;;0045 # Letter Lowercase Alphabetic ASCII_Hex_Digit Uppercase=0045 Titlecase=0045 
   0301;;Mn;;;;;;;;;;;; # Mark Extend 

String Indexing
===============

You can make integer-index accesses of strings which return Unicode
code points.

You can capture substrings of strings from an index position
:samp:`{p0}` through to but excluding another index position (which
defaults to the string's length).

.. code-block:: idio
   :caption: :file:`string-access.idio`

   s1 := "ħello"

   ;; s1.0 is slightly slower
   printf "first code point is %s (or %s)\n" (string-ref s1 0) s1.0

   printf "last code point is %s\n" s1.-1

   slen := string-length s1

   ss1 := substring s1 1 (slen - 1)

   printf "s1 from 1 up to %d is %s\n" (slen - 1) ss1
   printf "ss1 is %d code points\n" (string-length ss1)

   ;; you can loop over the code points in strings
   for c in (substring s1 0 3) {
     write c				; output a reader-friendly format
     (newline)
   }

   printf "the first l of %s is at index %d\n" s1 (string-index s1 #\l)
   printf "the last l of %s is at index %d\n" s1 (string-rindex s1 #\l)

.. code-block:: console

   $ idio string-access
   first code point is ħ (or ħ)
   last code point is o
   s1 from 1 up to 4 is ell
   ss1 is 3 code points
   #U+0127
   #\e
   #\l
   the first l of ħello is at index 2
   the last l of ħello is at index 3

Split/Join/Trim
===============

:lname:`Idio` defaults to shell-like behaviour for splitting strings
in that multiple adjacent instances of a delimiter are consumed
together giving the sense of splitting a line of text into fields or
words.  You can, of course, be more exacting than that.

.. code-block:: idio
   :caption: :file:`string-parts.idio`

   ;; two SPACEs at start and end and a TAB in the middle
   s1 := "  hello	world  "

   words := split-string s1 " \t"
   printf "words  are %s\n" words

   ;; fields is similar but uses IFS as the delimiter (which defaults to
   ;; the usual " \t\n") and returns an array with the first element
   ;; being the original string
   printf "fields are %s\n" (fields s1)

   printf "joined up words are %s\n" (join-string "-+-" words)

   printf "right-stripped string is  '%s'\n" (strip-string s1 " ")
   printf "double-stripped string is '%s'\n" (strip-string s1 " " 'both)

.. code-block:: console

   $ idio string-parts
   words are ("hello" "world")
   fields are #[ "  hello	world  " "hello" "world" ]
   joined up words are hello-+-world
   right-stripped string is  '  hello	world'
   double-stripped string is 'hello	world'

.. include:: ./commit.rst
