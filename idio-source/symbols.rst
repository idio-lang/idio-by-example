.. include:: ./global.rst

#######
Symbols
#######

Symbols are an unusual :lname:`Lisp`-ish type.  Most programming
languages use symbols exclusively as variables names.  In
:lname:`Idio` symbols get a :lname:`Lisp`-ish life and a shell-ish
life.

Symbols are like tags or names or flags you can pass around in their
own right, not associated with anything.  Think of an enumerated type
in :lname:`C` where the members of the type must resolve to some
integral value and subsequently tested for by, say, a ``switch``
statement.  In :lname:`Lisp` languages you can simply compare the
names for equality.

Symbols can are *always* be compared with ``eq?``.  Of interest, the
symbol ``x`` is the same as the symbol ``x`` where-ever it exists in
the program.

Symbols becomes variables in a traditional sense when they are bound
to values.  Subsequently, when the evaluator sees a symbol it will
replace the symbol with the value it references.

With our shell hat on, symbols are also the names of programs we
expect to be able to find on :envvar:`PATH`.  So, unlike almost every
programming language *except* shells, :lname:`Idio` does not raise an
error when a symbol is used and cannot be resolved to a value, it
simply carries on with the symbol.

Eventually, if the value in functional position in a list that
:lname:`Idio` wants to invoke is a symbol then it will look for a
program with the same name on the :envvar:`PATH`.

Nothing is quite perfect, though.  Frequently, to ensure the evaluator
**does not** resolve a symbol to its bound value the symbol will be
(single) quoted, ``'foo``, say.

.. code-block:: idio
   :caption: :file:`symbols.idio`

   ;; we've not used foo before so it should be a symbol but it's a good
   ;; habit to quote symbols just in case
   sym := 'foo

   printf "sym eq? 'foo: %s\n" (eq? sym 'foo)
   printf "sym eq? 'bar: %s\n" (eq? sym 'bar)
   printf "sym eq? 'foo: %s\n" (eq? sym 'foo)

   ;; again, we haven't used the symbol touch yet
   printf "symbol? touch: %s\n" (symbol? touch)

   ;; the symbol touch becomes the program touch
   touch "words.txt"

   ;; similarly ls becomes the program ls and -l is converted to a simple
   ;; string argument
   ls -l "words.txt"

.. code-block:: console

   $ idio symbols
   sym eq? 'foo: #t
   sym eq? 'bar: #f
   sym eq? 'foo: #t
   symbol? touch: #t
   -rw-rw-r--. 1 idf idf 0 Oct 27 16:56 words.txt

.. include:: ./commit.rst
