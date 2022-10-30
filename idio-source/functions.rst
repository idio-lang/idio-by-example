.. include:: ./global.rst

#########
Functions
#########

Functions are user-defined abstractions.

There is no explicit ``return`` statement.  The value returned is the
result of the last expression evaluated meaning that conditional
expressions such as ``if`` and ``cond`` can effect where the function
returns from in the source code.

You can declare zero or more parameters to the function.

.. code-block:: idio
   :caption: :file:`simple-function.idio`

   define (desc a) {
     if (a lt 0) "negative" "positive"
   }

   define (foo v) {
     printf "%d + 10 is %s\n" v (desc (v + 10))
     printf "%d - 10 is %s\n" v (desc (v - 10))
   }

   foo 5
   foo 15

.. code-block:: console

   $ idio simple-function
   5 + 10 is positive
   5 - 10 is negative
   15 + 10 is positive
   15 - 10 is positive

The expression used for the body of a function is usually a block to
combine a sequence of actions but can be any expression:

.. code-block:: idio
   :caption: :file:`function-bodies.idio`

   define (true-constant) #t

   define (true-string) "true"

   define (return-arg a) a

   define (add a b) (a + b)

   printf "(true-constant) is %s\n" (true-constant)
   printf "(true-string)   is %s\n" (true-string)
   printf "(return-arg 7)  is %s\n" (return-arg 7)
   printf "(add 7 13)      is %s\n" (add 7 13)

.. code-block:: console

   $ idio function-bodies
   (true-constant) is #t
   (true-string)   is true
   (return-arg 7)  is 7
   (add 7 13)      is 20

******************
Variadic Functions
******************

A function can declare its final parameter as variadic by separating
it with ``&``.  Any arguments supplied to the function over and above
the named formal parameters will be bundled into a list and be
available through the final parameter.  If no arguments over and above
the named formal parameters were supplied the variadic parameter will
have the value ``#n`` (the empty list).

.. code-block:: idio
   :caption: :file:`variadic-function.idio`

   define (desc a) {
     if (a lt 0) "negative" "positive"
   }

   define (foo base & mods) {
     if (null? mods) {
       printf "base is %d\n" base
     } {
       for mod in mods {
         printf "%d + %d is %s\n" base mod (desc (base + mod))
       }
     }
   }

   foo 5 10 -10
   foo 15

.. code-block:: console

   $ idio variadic-function
   5 + 10 is positive
   5 + -10 is negative
   base is 15

*******************
Anonymous Functions
*******************

Where a function is only going to be used once we don't need to
declare a named function (and pollute the namespace) but can pass an
anonymous function *in situ*.

An anonymous function is declared as:

.. parsed-literal::

   function (*params*) *body*

although as it is almost always an argument to something else it will
be wrapped in parentheses: :samp:`(function ({params}) {body})`.

The ``for`` looping construct we've been using is just syntactic sugar
around ``for-each``:

.. parsed-literal::

   for *var* in *vals* *body*

   for-each (function (*var*) *body*) *vals*

For example:

.. code-block:: idio
   :caption: :file:`anonymous-function.idio`

   vals := '(1 2 3)

   for val in vals {
     printf "val is %d\n" val
   }

   for-each (function (val) {
     printf "val is %d\n" val
   }) vals

.. code-block:: console

   $ idio anonymous-function
   val is 1
   val is 2
   val is 3
   val is 1
   val is 2
   val is 3

.. include:: ./commit.rst
