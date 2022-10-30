.. include:: ./global.rst

######
Blocks
######

Blocks introduce a lexical scope for variables declared within them.
You can still access variables declared outside, of course, unless you
shadow them as we'll see below.

They are commonly used as expressions because they have an implied
sequencing of expressions (top to bottom!).

A block returns the last evaluated value.  As ever, conditional
expressions (``if``, ``cond``) might alter the flow.

.. code-block:: idio
   :caption: :file:`blocks.idio`

   n := 10

   printf "1: n is %d\n" n

   n = 20

   printf "2: n is %d\n" n

   {
     ;; still referencing the top level n
     n = 30

     printf "3: n is %d\n" n

     ;; a block-local n shadows the top level n
     n := 40

     printf "4: n is %d\n" n

     ;; we still have the block-local in lexical scope
     n = 50

     printf "5: n is %d\n" n

     ;; a newer block-local (what are you doing?)
     n := 60

     printf "6: n is %d\n" n

   }
   ;; we didn't capture it but that block returned "#<unspec>" because
   ;; that's what printf, the last expression evaluated, returned

   ;; outside the block we're back to the top level n
   printf "7: n is %d\n" n

.. code-block:: console

   $ idio blocks
   1: n is 10
   2: n is 20
   3: n is 30
   4: n is 40
   5: n is 50
   6: n is 60
   7: n is 30

.. include:: ./commit.rst
