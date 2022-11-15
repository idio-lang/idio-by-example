.. include:: ./global.rst

#########
Variables
#########

Symbols in :lname:`Idio` and, by extension, variables can be fairly
flexible compared to many languages.  In particular, many punctuation
characters are valid in variable names such as ``a+1``, ``*foo*`` and
``number?``.

Variables are symbols that have been *bound* to a value.

The *first* time a variable is used it should be bound using the
``:=`` operator:

.. code-block:: idio
   :caption: :file:`simple-values.idio`

   a := #t
   printf "a is %s\n" a

   b := 101
   printf "b is %s or %X or %b\n" b b b

   c := 123e-2
   printf "c is %s or %5.1f\n" c c

   d := "Hello World"
   printf "d is %s and %.3s\n" d d

   e := current-output-handle
   printf "(e) is %s\n" (e)
   printf "e is %s\n" e

.. code-block:: console

   $ idio simple-values
   a is #t
   b is 101 or 65 or 1100101
   c is 1.230000e+00 or   1.2
   d is Hello World and Hel
   (e) is #<H ofw!iF   1:"*stdout*":5:86>
   e is #<PRIM current-output-handle>

Notice that ``e`` became bound to the same value that
``current-output-handle`` was bound to and so could be invoked in the
same way, ``(e)``, to return the value.

Subsequently, you can re-bind a variable to a different value using
the ``=`` operator.  Re-binding variables might be frowned upon in
certain circles but it is common enough in many paradigms:

.. code-block:: idio
   :caption: :file:`re-assignment.idio`

   define (foo a) {

     desc := "positive"
     if (a lt 0) {
       desc = "negative"
     }

     printf "%d is %s\n" a desc
   }

   foo 10
   foo -10


.. code-block:: console

   $ idio re-assignment
   10 is positive
   -10 is negative

``if``, like all expressions, returns a value so we could have defined
``foo`` as:

.. code-block:: idio

   define (foo a) {

     desc := if (a lt 0) "negative" "positive"

     printf "%d is %s\n" a desc
   }

or not even created the local variable, ``desc`` and put the ``if``
expression (in parentheses) on the end of the ``printf`` expression.

Readability is always a concern.

.. include:: ./commit.rst
