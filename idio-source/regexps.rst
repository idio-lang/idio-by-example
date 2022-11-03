.. include:: ./global.rst

#######
Regexps
#######

:lname:`Idio` uses the POSIX :manpage:`regex(7)` regular expression
primitives ``regcomp`` and ``regexec``.

You can use them directly or commonly is variations on the ``case``

**********
regex-case
**********

``regex-case`` which works like a simplified ``cond`` except the
clause "conditions" are regular expressions to be matched.

If the regular expression matches, ``regex-case`` then supplies the
consequent block with the result of the call to ``regexec`` as the
variable :var:`r`.  As such :var:`r.0` is the whole of the matched
string, :var:`r.1` is the first matched sub-expression, :var:`r.2` the
second matched sub-expression, etc..

.. code-block:: idio
   :caption: :file:`simple-regex-case.idio`

   (regex-case
    (read-line)
    ("^([[:alpha:]][[:alnum:]_]*)=(.*)" {
      printf "%s is '%s'\n" r.1 r.2
    })
    (else {
      printf "no match\n"
    }))

Here we need to input a line for ``read-line``:

.. code-block:: console

   $ idio simple-regex-case
   foo=bar
   foo is 'bar'

************
pattern-case
************

Similarly, ``pattern-case`` provides something like the shell's
*Pattern Matching* where ``*`` and ``?`` are really ``.*`` and ``.``
respectively.

Note that your pattern string is implicitly anchored to the start and
end.

.. code-block:: idio
   :caption: :file:`simple-pattern-case.idio`

   for try in '(1 2 3 4) {
     (pattern-case
      (read-line)
      ("foo*" {
        printf "%s is foo-ish\n" r.0
      })
      ("bar?" {
        printf "%s is bar-ish\n" r.0
      })
      (else {
        printf "no match\n"
      }))
   }

Four lines of input, this time:

.. code-block:: console

   $ idio simple-pattern-case
   foo diddly-dee do
   foo diddly-dee do is foo-ish
   I'm feeling foo
   no match
   barb
   barb is bar-ish
   barbarosa
   no match

.. include:: ./commit.rst
