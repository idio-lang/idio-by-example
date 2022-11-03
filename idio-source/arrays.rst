.. include:: ./global.rst

######
Arrays
######

Arrays are integer-indexed collections of references to values.  You
can only access existing indices and you can only grow or shrink and
array by amending the start or end of the array.

The index can be negative to access elements from the end of the
array.  Of course you can't access a negative index that would be
generate a true negative index (ie. one whose absolute value is
greater than the array's length).

.. code-block:: idio
   :caption: :file:`simple-arrays.idio`

   ;; elements default to #f
   a1 := make-array 2
   printf "a1 is %d elements: %s\n" (array-length a1) a1

   array-set! a1 0 "friends"

   ;; the dot operator is more convenient but slightly slower
   a1.1 = "romans"

   ;; extend the array with push
   array-push! a1 "countrymen"

   ;; extend the array with unshift
   array-unshift! a1 "Ahem!"

   printf "a1 is %d elements: %s\n" (array-length a1) a1

   ;; Sack the speech writer!
   array-shift! a1
   printf "a1 is %d elements: %s\n" (array-length a1) a1

   ;; general access of elements
   printf "a1.1 is %s\n" a1.1

   ;; negative index, here the last element
   printf "a1.-1 is %s\n" a1.-1

   ;; static construction does not allow evaluation of elements
   a2 := #[ a1.1 ]
   printf "a2 is %d elements: %s\n" (array-length a2) a2

   a3 := #[ "romanes" "eunt" "domus" ]
   printf "a3 is %d elements: %s\n" (array-length a3) a3

   ;; you can use evaluated elements with array
   a4 := array a3.0 a3.1 a3.2
   printf "a4 is %d elements: %s\n" (array-length a4) a4

.. code-block:: console

   $ idio simple-arrays
   a1 is 2 elements: #[ #f #f ]
   a1 is 4 elements: #[ "Ahem!" "friends" "romans" "countrymen" ]
   a1 is 3 elements: #[ "friends" "romans" "countrymen" ]
   a1.1 is romans
   a1.-1 is countrymen
   a2 is 3 elements: #[ a1 . 1 ]
   a3 is 3 elements: #[ "romanes" "eunt" "domus" ]
   a4 is 3 elements: #[ "romanes" "eunt" "domus" ]

.. include:: ./commit.rst
