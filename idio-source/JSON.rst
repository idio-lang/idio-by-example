.. include:: ./global.rst

####
JSON
####

:lname:`Idio` can read and write JSON, the data interchange format.
Technically, it reads and writes JSON5 (a superset of JSON) and can
write JSON.

Few systems use JSON5 so you will probably write JSON most of the
time.

*******
Reading
*******

You can read JSON from strings, files and from file descriptors.  The
value returned will be an appropriate :lname:`Idio` type -- often a
hash table for the JSON object type.

The :lname:`Idio` source contains the example test files from
`<https://spec.json5.org>`_:

.. code-block:: idio
   :caption: :file:`reading-json.idio`

   import json5

   data := parse-file "ext/json5/src/examples/spec.json5.org/1.2.json5"

   printf "data is %s\n" data
   printf "andIn is %s\n" data."andIn"

With the output edited for readability:

.. code-block:: console

   $ idio reading-json
   data is #{
	("lineBreaks" & "Look, Mom! No \\n's!")
	("backwardsCompatible" & "with JSON")
	("andIn" & #[ "arrays" ])
	("andTrailing" & 8.675309e+06)
	("leadingDecimalPoint" & 8.675308e-01)
	("positiveSign" & 1)
	("trailingComma" & "in objects")
	("unquoted" & "and you can quote me on that")
	("hexadecimal" & 912559)
	("singleQuotes" & "I can use \"double quotes\" here")
	}
   andIn is #[ "arrays" ]

You'll note some idiosyncrasies, in particular that JSON5's use of
(ECMAScript's) *IdentifierName* as object *MemberName*\ s are
converted to :lname:`Idio` strings (rather than symbols) and numeric
conversions may differ.

*******
Writing
*******

You can generate JSON(5) from appropriate :lname:`Idio` values:
constants, strings, arrays and hash tables.

JSON
====

Here, we'll create a JSON-friendly hash table and generate both JSON5
and JSON from it.

.. code-block:: idio
   :caption: :file:`writing-json.idio`

   import json5

   data := #{}
   data."literals" = #[ #t #f #n ]
   data."numbers" = #[ +1 -1 1.0 ]
   data."strings" = #[ "hello" "the \U1F3BC musical score" ]

   printf "data is %s\n" data

   json5-str := generate data
   printf "JSON5 is %s\n" json5-str
   printf "JSON is the same? %s\n" (equal? json5-str (generate-json data))

The output has been edited for readability:

.. code-block:: console

   $ idio writing-json
   data is #{
	("strings" & #[ "hello" "the 🎼 musical score" ])
	("literals" & #[ #t #f #n ])
	("numbers" & #[ 1 -1 1.000000e+00 ])
   }
   JSON5 is {
     "strings": [
	 "hello",
	 "the 🎼 musical score"
       ],
     "literals": [
	 true,
	 false,
	 null
       ],
     "numbers": [
	 1,
	 -1,
	 1.0e+0
       ]
   }
   JSON is the same? #t

Here, the JSON and JSON5 output being the same won't be honoured, in
general, as we can't guarantee the order in which the keys of a hash
table are processed.

JSON5
=====

This time, we'll create a JSON5 hash table and only generate JSON5 (as
the elements are beyond the capabilities of JSON).  and JSON from it.

.. code-block:: idio
   :caption: :file:`writing-json5.idio`

   import json5

   data := #{}
   data.'literals = #[ -Infinity -NaN ]
   data.'numbers = #[ #xdecaf #x-C0FFEE ]
   data.'strings = #[ "hello" "the \U1F3BC musical score" ]

   printf "data is %s\n" data

   printf "JSON5 is %s\n" (generate data)

The output has been edited for readability:

.. code-block:: console

   $ idio writing-json5
   data is #{
	(literals & #[ -Infinity -NaN ])
	(numbers & #[ 912559 -12648430 ])
	(strings & #[ "hello" "the 🎼 musical score" ])
   }
   JSON5 is {
     literals: [
	 -Infinity,
	 -NaN
       ],
     numbers: [
	 912559,
	 -12648430
       ],
     strings: [
	 "hello",
	 "the 🎼 musical score"
       ]
   }

.. include:: ./commit.rst
