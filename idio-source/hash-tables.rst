.. include:: ./global.rst

###########
Hash Tables
###########

Hash Tables are arbitrary value-indexed collections of references to
values.  The only value you cannot use as a key  is ``#n``.

.. code-block:: idio
   :caption: :file:`simple-hash-tables.idio`

   ;; use the default equivalence and hashing functions
   h1 := make-hash #n #n 2
   printf "h1 has %d keys: %s\n" (hash-size h1) h1

   hash-set! h1 'a "apple"

   ;; access it again
   printf "%s is for %s\n" 'a (hash-ref h1 'a)

   ;; the dot operator is more convenient but slightly slower
   printf "%s is for %s\n" 'a h1.'a

   ;; similarly for setting entries
   h1.'b = "banana"

   ;; the key is evaluated so key becomes the symbol c
   key := 'c
   h1.key = "carrot"

   printf "h1 has %d keys: %s\n" (hash-size h1) (hash-keys h1)

   ;; hash-keys returns a list so it is easy to iterate with
   for k in (hash-keys h1) {
     printf "%s is for %s\n" k h1.k
   }

   ;; hash-walk is another form of iterator
   define (walker k v) {
     printf "%s maps to %s\n" k v
   }

   hash-walk h1 walker

.. code-block:: console

   $ idio simple-hash-tables
   h1 has 0 keys: #{ }
   a is for apple
   a is for apple
   h1 has 3 keys: (a c b)
   a is for apple
   c is for carrot
   b is for banana
   a maps to apple
   c maps to carrot
   b maps to banana

.. note::

   The order in which keys are accessed is not well-defined.

.. include:: ./commit.rst
