.. include:: ./global.rst

###########
Pairs/Lists
###########

Pairs are a basic building block in all :lname:`Lisp` languages.  It
is a simple two-part value with one called the *head* and the other
called the *tail*.  You can access the head of a pair with ``ph``
(*pair head*) and the tail of a pair with ``pt`` (*pair tail*).

.. code-block:: idio
   :caption: :file:`simple-pairs.idio`

   p := pair 1 2
   printf "p is %s\n" p
   printf "ph of p is %s\n" (ph p)
   printf "pt of p is %s\n" (pt p)

.. code-block:: console

   $ idio simple-pairs
   p is (1 & 2)
   ph of p is 1
   pt of p is 2

Either of the head or tail can be *any* other value including other
pairs.  This leads to some very rich data structures.

It also leads to some tersely named accessors such as ``phtt`` which
is shorthand for :samp:`ph (pt (pt {x}))`.

*****
Lists
*****

If the *tail* of a pair is another pair we can sense a linked list
forming where the "data" part is stored in the *head*.

This can be rather verbose to create manually so there is a
convenience function, ``list``.

There is also a slightly different printing format which does away
with some of the extra parentheses and "pair separators", ``&``.  The
result is the rather more traditional "list" printed form:
:samp:`({e1} {e2} {e3} ...)`.

.. code-block:: idio
   :caption: :file:`simple-lists.idio`

   l1 := pair 1 (pair 2 (pair 3 #n))
   printf "l1 is %s\n" l1
   printf "ph of l1 is %s\n" (ph l1)
   printf "pt of l1 is %s\n" (pt l1)

   l2 := list 1 2 3
   printf "l2 is %s\n" l2
   printf "ph of l2 is %s\n" (ph l2)
   printf "pt of l2 is %s\n" (pt l2)

.. code-block:: console

   $ idio simple-lists
   l1 is (1 2 3)
   ph of l1 is 1
   pt of l1 is (2 3)
   l2 is (1 2 3)
   ph of l2 is 1
   pt of l2 is (2 3)

The ``#n`` in the find ``pair`` of :samp:`{l1}`, above, marks a proper
list.  The vast majority of lists are proper lists cleanly ending with
a tail of ``#n`` (*nil* or *null*).  ``list`` always creates proper
lists.

Improper lists appear from time to time where that final tail is not
``#n``.

List Functions
==============

With a list we can test for membership of that list with variations on
the equality functions:

#. ``memq`` tests for membership using ``eq?``

#. ``memv`` tests for membership using ``eqv?``

#. ``member`` tests for membership using ``equal?``

These membership functions return ``#f`` if they fail but the
remainder of the list (inclusive of the matching element) if they
succeed:

.. code-block:: idio
   :caption: :file:`list-membership.idio`

   p1 := pair 1 2
   p2 := pair 1 2
   things := list 1 "hello" p1 'symbol
   printf "things is %s\n" things
   printf "memq 3 in things is %s\n" (memq 3 things)
   printf "memq 1 in things is %s\n" (memq 1 things)
   printf "memq p1 in things is %s\n" (memq p1 things)
   printf "memv \"hello\" in things is %s\n" (memv "hello" things)
   printf "member p2 in things is %s\n" (member p2 things)

.. code-block:: console

   $ idio list-membership
   things is (1 "hello" (1 & 2) symbol)
   memq 3 in things is #f
   memq 1 in things is (1 "hello" (1 & 2) symbol)
   memq p1 in things is ((1 & 2) symbol)
   memv "hello" in things is ("hello" (1 & 2) symbol)
   member p2 in things is ((1 & 2) symbol)

Association Lists
=================

If we make the "data" part more complicated, in particular, a list
itself, we can treat this list of lists as a lookup table where the
key is the first element in each list.

These *association lists* are very common and have equivalent key
lookup functions:

#. ``assq`` tests the key using ``eq?``

#. ``assv`` tests the key using ``eqv?``

#. ``assoc`` tests the key using ``equal?``

.. code-block:: idio
   :caption: :file:`alists.idio`

   ;; by quoting the list of lists we prevent the evaluator trying to
   ;; run functions called a, b and c
   data := '((a "apple" fruit)
	     (b "banana" fruit)
	     (c "carrot" vegetable))

   ;; symbols are *always* eq?

   sym := 'b
   printf "assq %s is %s\n" sym (assq sym data)

.. code-block:: console

   $ idio alists
   assq b is (b "banana" fruit)

.. include:: ./commit.rst
