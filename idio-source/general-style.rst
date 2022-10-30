.. include:: ./global.rst

#############
General Style
#############

:lname:`Idio` has a very simple syntax but enforces a couple of
deliberate styles.

**********
Whitespace
**********

*Whitespace is important* in :lname:`Idio`.  This is mostly a
side-effect of allowing more complex symbols which allow a richer
expression.

* ``a+1`` is a symbol -- and therefore a potential variable name

* ``a +1`` is a list of the symbol ``a`` and the number ``1``

* ``a+ 1`` is a list of the symbol ``a+`` and the number ``1``

* ``a + 1`` is a list of the symbol ``a``, the symbol ``+`` and the
  number ``1``

*************
Line-Oriented
*************

:lname:`Idio` is *line-oriented* in a similar way to a shell.
Expressions are consumed up to an end-of-line whereon something might
be run.

Expressions can be multi-line, however.  Here, think of consuming
everything between matching braces (``{`` through to the corresponding
``}``) but also other kinds of brackets and strings.

This gives rise to a normal form for declaring functions:

.. code-block:: idio
   :linenos:

   define (foo a b) "

   documentation for foo

   " {

     if (do this) {
       do that
     }

     return computation
   }

Here, the documentation string starts on the line 1 but isn't complete
until line 5.  Before we reach the end of line 5 we start a code block
with ``{`` which isn't complete until the matching ``}`` in line 12.

There is nothing else on line 12 so we have successfully consumed a
"line" of expressions.

If we didn't use a documentation string we still require the body of
the function to begin on the same line as the declaration:

.. code-block:: idio

   define (foo a b) {

     if (do this) {
       do that
     }

     return computation
   }

otherwise :lname:`Idio` will report an evaluation error as the
definition of ``foo`` has no body value.

***********************
One Expression per Line
***********************

:lname:`Idio` only accepts one expression per "line" (noting the
potential multi-line consumption from the previous section).

You cannot put two expressions on one line as :lname:`Idio` infers
that the first expression will be a function and the remaining
expressions are arguments to that function:

.. code-block:: idio

   (1 + 2) (3 * 4)

Here, :lname:`Idio` will consume it all and, eventually, think that
you've asked it to run the expression ``3 12``, ie. the function ``3``
called with the argument ``12``.

``3`` is not a function.

Line Continuation
=================

You can use regular shell-like line continuation characters (a ``\``
followed by a newline) to extend a series of expressions over multiple
lines.

How readable it is is a matter for debate:

.. code-block:: idio

   printf \
     "This is %s\n" \
     "easy on the eye"

No Expression Delimiters
========================

There are no expression delimiters in :lname:`Idio` to get around this
one expression per line mode.  In the shell you might use a *control
operator* such as ``;`` to delimit two expressions on one line, such
as:

.. code-block:: bash

   do this ; do that

There is no such thing in :lname:`Idio`.

Technically, the above is syntactically valid :lname:`Idio` but only
because ``;`` starts a line comment and the expression is reduced to
just ``do this``.

*****
Lists
*****

:lname:`Idio` is nominally as *Lisp* in that everything is treated as
a list (of expressions).  There are no expression delimiters (other
than whitespace).

The first expression is expected to resolve to a function and the
remaining expressions are evaluated and passed as arguments to that
function.

As a nod to convenience and more shell-like parsing, all expressions
on a line are converted to a list:

.. code-block:: idio

   printf "Hello, %s\n" "Dave"

is identical to:

.. code-block:: idio

   (printf "Hello, %s\n" "Dave")

Beyond this implicit wrapping of the expressions on a line you must
wrap any sub-expressions in parentheses in the same manner you would
for many other languages.

.. code-block:: idio

   printf "7 * 3 is %d\n" (7 * 3)

.. note::

   :lname:`Idio` allows the use of *reader operators* which can
   transform the source code before it is passed to the evaluator.

   Here, had we forgotten to use parentheses:

   .. code-block:: idio

      printf "7 * 3 is %d\n" 7 * 3

   .. aside::

      Technically, it would have re-written it as ``(binary-* 7 3)``
      using the binary multiplication function which is slightly
      faster than ``(* ...)``, the general multiplication function,
      which takes any number of arguments.

   then the ``*`` *infix operator* would have re-written the line as:

   .. code-block:: idio

      printf "7 * 3 is %d\n" (7 * 3)

   anyway.

*********************
No-Argument Functions
*********************

There is a corner-case where convenience and functionality collide.
If you have a function that can take zero arguments then :lname:`Idio`
is unable to distinguish between you wanting to *get* the function
value (to return it, say) and you wanting to *invoke* the function
value (with no arguments).

Here, if you want to invoke such a function, you *must* wrap it in
parentheses.

This also affects running external commands without arguments.

.. include:: ./commit.rst
