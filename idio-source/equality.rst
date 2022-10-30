.. include:: ./global.rst

########
Equality
########

Equality is a nuanced thing.  If we ask the question, is the string
``"hello"`` *the same* as the string ``"hello"``?  What do we really
mean by "the same"?

#. are they the same location *in memory*?

   We can test any two objects for exact equivalence with ``eq?``.

#. for some values (numbers and strings, say) they might be in
   different locations in memory but they have the same value: ``1``
   is ``1`` and so long as the strings are the same length (and type!)
   and each Unicode code point is the same in each index of the
   strings then they are "the same"

   We can test any two objects for value equivalence with ``eqv?``.

#. Finally, for compound values, we can test if they are
   *semantically* the same with ``equal?``.

   The Scheme R5RS standards suggests "do they print the same?"

   This woolly definition reflects the tricky problem of accessing the
   elements of compound objects in a consistent manner.

.. code-block:: idio
   :caption: :file:`equality.idio`

   n1 := 1
   n2 := 2
   n3 := 1
   printf "(eq? n1 1) is %s\n" (eq? n1 1)
   printf "(eq? n1 n2) is %s\n"(eq? n1 n2)
   printf "(eqv? n1 n3) is %s\n" (eqv? n1 n3)

   s1 := "hello"
   s2 := "hello"
   printf "(eq? s1 \"hello\") is %s\n" (eq? s1 "hello")
   printf "(eq? s1 s2) is %s\n"(eq? s1 s2)
   printf "(eqv? s1 \"hello\") is %s\n" (eqv? s1 "hello")
   printf "(eqv? s1 s2) is %s\n" (eqv? s1 s2)
   
.. code-block:: console

   $ idio equality
   (eq? n1 1) is #t
   (eq? n1 n2) is #f
   (eqv? n1 n3) is #t
   (eq? s1 "hello") is #f
   (eq? s1 s2) is #f
   (eqv? s1 "hello") is #t
   (eqv? s1 s2) is #t

.. include:: ./commit.rst
