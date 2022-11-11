.. include:: ./global.rst

###################
Unicode Code Points
###################

The "characters" in strings (and as standalone values) are Unicode
code points, normally represented by ``#U+...`` for enough hexadecimal
digits to represent the code point.  Leading zeroes are not required
(but may be necessary, see below).

You can input a specific character with ``#\X`` for some *X*, a UTF-8
code point.

There are a limited number of ``#\{newline}`` *named* characters.

.. code-block:: idio
   :caption: :file:`code-points.idio`

   ;; ħ is U+0127 LATIN SMALL LETTER H WITH STROKE
   c1 := #U+127

   c2 := #\ħ

   ;; the unicode type is much like fixnum and can be compared with eqv?
   printf "Does <<%s>> eqv? <<%s>>? %s\n" c1 c2 (eqv? c1 c2)

   ;; SPACE
   c1 = #U+20

   ;; or using a named character
   c2 = #\{space}

   printf "Does <<%s>> eqv? <<%s>>? %s\n" c1 c2 (eqv? c1 c2)

.. code-block:: console

   $ idio code-points
   Does <<ħ>> eqv? <<ħ>>? #t
   Does << >> eqv? << >>? #t

There are a number of Unicode-derived *Category* and *Property*
predicates and a very limited set of conversion functions.

.. code-block:: idio
   :caption: :file:`unicode-functions.idio`

   c1 := #U+127

   if (Lowercase? c1) {
     printf "%s ->Uppercase %s\n" c1 (->Uppercase c1)
   }

   ;; tell me what you know!
   unicode/describe c1

.. code-block:: console

   $ idio unicode-functions
   ħ ->Uppercase Ħ
   0127;;Ll;;;;;;;;;;0126;;0126 # Letter Lowercase Alphabetic Uppercase=0126 Titlecase=0126 

.. include:: ./commit.rst
