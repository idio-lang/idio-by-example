.. include:: ./global.rst

###############
For Style Loops
###############

:lname:`Idio` supports a *for each* style of looping over compound
values.  ``for-each`` is a simple wrapper around ``map`` where ``map``
collects results and ``for-each`` doesn't.

They both take the form, :samp:`map {func} {list+}`, where
:samp:`{func}` should take as many arguments as there are lists in
:samp:`{list+}`.

.. code-block:: idio
   :caption: :file:`simple-for-each.idio`

   define (foo p) {
     printf "%s is for %s\n" (ph p) (pht p)
   }

   data := '((a "apple")
             (b "banana"))

   for-each foo data

.. code-block:: console

   $ idio simple-for
   a is for apple
   b is for banana

Defining a specific one-shot function is tiresome:

.. code-block:: idio
   :caption: :file:`simple-for-2.idio`

   data := '((a "apple")
             (b "banana"))

   for p in data {
     printf "%s is for %s\n" (ph p) (pht p)
   }

.. code-block:: console

   $ idio simple-for-2
   a is for apple
   b is for banana

A variation is to walk down each list simultaneously by passing
multiple variables as a list, one variable per list:

.. code-block:: idio
   :caption: :file:`simple-for-3.idio`

   data := '((a "apple")
             (b "banana"))

   for (e1 e2) in data {
     printf "%s and %s\n" e1 e2
   }

.. code-block:: console

   $ idio simple-for-3
   a and b
   apple and banana

Notice that the lists are walked in parallel which is orthogonal to
the previous example.

.. include:: ./commit.rst
