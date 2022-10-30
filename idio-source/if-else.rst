.. include:: ./global.rst

#######
If/Else
#######

**
If
**

The basic conditional expression is ``if`` which takes two or three
expressions:

#. a *condition* -- the expression that should evaluate to ``#f`` or
   not-``#f``

#. a *consequent* -- the expression that should be evaluated if the
   condition was "true"

#. an optional *alternative* -- the expression that, if supplied,
   should be evaluated if the condition was ``#f``

.. note::

   There is no ``else`` keyword.

.. code-block:: idio
   :caption: :file:`simple-if.idio`

   a := #t

   if a {
     printf "a is true\n"
   } {
     printf "a is false\n"
   }

   if (not a) {
     printf "(not a) is true\n"
   } {
     printf "(not a) is false\n"
   }

   b := 0
   if b {
     printf "b is true\n"
   } {
     printf "b is false\n"
   }

.. code-block:: console

   $ idio simple-if
   a is true
   (not a) is false
   b is true


.. include:: ./commit.rst
