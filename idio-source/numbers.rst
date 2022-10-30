.. include:: ./global.rst

#######
Numbers
#######

There are two types of numbers in :lname:`Idio`: *fixnums* and
*bignums*.

Fixnums are the regular integers we throw around: ``1``, ``200``,
``-10`` etc..  They are pretty big, around +/- 2\ :sup:`61` (or +/- 2\
:sup:`29` on 32-bit systems), enough to keep command orchestration
users happy.

Bignums are for any floating point operations.  You'll automatically
get a bignum based on source code (``1.2`` or ``1e10``) or
mathematical operation (any division, anything that goes beyond the
limits of a fixnum!).

.. code-block:: idio
   :caption: :file:`numbers.idio`

   fn := 10

   printf "fn: fn? %s: %s\n" (fixnum? fn) fn

   bn := 1.2

   printf "bn: bn? %s: %s\n" (bignum? bn) bn

   r := fn + bn

   printf "fn + bn: fn? %s: bn? %s: %s\n" (fixnum? r) (bignum? r) r

   r = fn * bn

   printf "fn * bn: fn? %s: bn? %s: %s\n" (fixnum? r) (bignum? r) r

   r = fn + 1

   printf "fn +  1: fn? %s: bn? %s: %s\n" (fixnum? r) (bignum? r) r

   r = fn / 1

   printf "fn /  1: fn? %s: bn? %s: %s\n" (fixnum? r) (bignum? r) r


.. code-block:: console

   $ idio numbers
   fn: fn? #t: 10
   bn: bn? #t: 1.200000e+00
   fn + bn: fn? #f: bn? #t: 1.120000e+01
   fn * bn: fn? #f: bn? #t: 1.200000e+01
   fn +  1: fn? #t: bn? #f: 11
   fn /  1: fn? #f: bn? #t: 1.000000e+01

**************
Number Formats
**************

You can use a variety of formats for inputting numbers:

.. code-block:: idio
   :caption: :file:`number-formats.idio`

   printf "floating point numbers like %s %s %s\n" 1. 0.3e1 6E7

   ;; binary numbers with #b...
   n := #b101
   printf "#b is %b or %d\n" n n

   ;; octal numbers with #o...
   n = #o101
   printf "#o is %o or %d\n" n n

   ;; hexadecimal numbers with #x...
   n = #x101
   printf "#x is %x or %d\n" n n

.. code-block:: console

   $ idio number-formats
   floating point numbers like 1.000000e+00 3.000000e+00 6.000000e+07
   #b is 101 or 5
   #o is 101 or 65
   #x is 101 or 257

.. include:: ./commit.rst
