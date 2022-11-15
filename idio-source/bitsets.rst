.. include:: ./global.rst

#######
Bitsets
#######

Bitsets are simple sets of integral-indexed bits originally introduced
to support attribute testing of Unicode char-sets so both integers and
Unicode code points can be used as indices.  You can use them as a set
of flags much like bitmasks in :lname:`C`.

The first bit is index 0 (zero).

.. code-block:: idio
   :caption: :file:`simple-bitsets.idio`

   ;; create a five element bitset -- all bits are initially unset
   bs1 := make-bitset 5

   printf "bs1 is %s\n" bs1

   ;; set the first two bits
   bitset-set! bs1 0
   bitset-set! bs1 1

   printf "bs1 is %s\n" bs1

   ;; if we know the starting bits we can create it immediately
   bs2 := #B{ 5 11 }
   printf "bs2 is %s\n" bs2

   ;; more than 8 bits
   bs3 := make-bitset 40

   ;; set the 9th and tenth bits
   bitset-set! bs3 8
   bitset-set! bs3 9

   printf "bs3 is %s\n" bs3

   ;; an inverted bs3
   bs4 := not-bitset bs3

   ;; clear the last bit
   bitset-clear! bs4 ((bitset-size bs4) - 1)

   printf "bs4 is %s\n" bs4

.. code-block:: console

   $ idio simple-bitsets
   bs1 is #B{ 5 }
   bs1 is #B{ 5 11000 }
   bs2 is #B{ 5 11000 }
   bs3 is #B{ 40 8:11000000 }
   bs4 is #B{ 40 0-0 00111111 10-18 11111110 }

The printed (and reader) form is a condensed set with listed ranges
all set (otherwise the bits are unset).  You can use the ``#B{ ... }``
form as bitset constructors.

In the previous example's output:

* :var:`bs3` has the first two bits set in the block starting at index
  8 (a hex index)

* :var:`bs4` has

  * all bits in the blocks starting at (hex) 0 through to the block
    starting at (hex) 0 inclusive are set

  * the *next* block has the first two bits unset then the rest set

  * all bits in the blocks starting at (hex) 10 through to the block
    starting at (hex) 18 inclusive are set

  * the *next* block has all the bits set except the last

*******
SRFI-14
*******

SRFI-14_ defines several sets of char-sets which use arrays of bitsets
(representing Unicode planes) to cover the 2\ :sup:`21` bits of
Unicode code points.

.. code-block:: idio
   :caption: :file:`simple-char-sets.idio`

   require SRFI-14

   ;; the ASCII-only variant of char-set:lower-case
   printf "%s\n" %char-set:lower-case

.. code-block:: console

   $ idio simple-char-sets
   #<SI sparse-char-set size=1114112 planes=#[
        #B{ 65536 60:01111111 68-70 11100000 }
        #f #f #f #f #f #f #f #f #f #f #f #f #f #f #f #f ]>

NB. The output has been edited for readability.

For the ASCII-only variant you can see the bitset for the first plane
is ``#B{ 65536 60:01111111 68-70 11100000 }`` which reads as bits from
index 61 (hex) through to 7a (hex).  Which looks reasonable.

Try ``char-set:lower-case`` (without the ``%``) for the complete
Unicode SRFI 14 definition of lower case code points.

.. include:: ./commit.rst
