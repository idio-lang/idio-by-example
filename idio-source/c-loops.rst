.. include:: ./global.rst

###############
C Style Looping
###############

:lname:`Idio` supports the :lname:`C` ``for`` and ``while`` style of
looping.  There is a name-clash so the :lname:`C` variant is named
``C/for``.

The standard :lname:`C` form has four elements:

.. parsed-literal::

   for (*init*; *test*; *incr*) *body*

which allow for fairly arbitrary :samp:`{init}`, :samp:`{test}` and
:samp:`{incr}` expressions.

In :lname:`Idio`, each loop variable gets its own :samp:`({name}
{init} {incr})` tuple in the :samp:`{var-clauses}` section:

.. parsed-literal::

   C/for *var-clauses* *test* *body*

:samp:`{name}` and the :samp:`{init}` expression are straight-forward
enough, the :samp:`{incr}` expression is the value to be used next
time round the loop.  In other words, you are not looking to modify
the variable :samp:`{name}` but rather calculating its value for next
time round.

.. code-block:: idio
   :caption: :file:`simple-C-for.idio`

   C/for (
	  (i 0 (i + 1))
   ) (i lt 5) {
     printf "i is %d\n" i
   }

.. code-block:: console

   $ idio simple-C-for
   i is 0
   i is 1
   i is 2
   i is 3
   i is 4

*****
while
*****

``while`` is, of course, simply a ``C/for`` without the
:samp:`{var-clauses}` element:

.. code-block:: idio
   :caption: :file:`simple-C-while.idio`

   i := 0

   while (i lt 5) {
     printf "i is %d\n" i
     i = i + 1
   }

.. code-block:: console

   $ idio simple-C-while
   i is 0
   i is 1
   i is 2
   i is 3
   i is 4



****************
break / continue
****************

The keywords ``break`` and ``continue`` are available in the
:samp:`{body}` element of both ``C/for`` and ``while``:

.. code-block:: idio
   :caption: :file:`simple-C-for-2.idio`

   C/for (
   	  (i 0 (i + 1))
   ) (i lt 5) {
     (cond
      ((i eq 1) (continue))
      ((i eq 4) (break))
      (else {
        printf "i is %d\n" i
      }))
   }

.. code-block:: console

   $ idio simple-C-for-2
   i is 0
   i is 2
   i is 3



.. include:: ./commit.rst
