.. include:: ./global.rst

########
Closures
########

All user-defined functions are closures.  Closures "close over" the
environment they were defined in.

For top level defined functions this broadly translates as being that
the function remembers the module it was defined in and therefore has
access to any internally defined functions and variables in that
module that would otherwise be inaccessible.

If a function is defined inside a block it also has access to any
block-local variables which gives rise to the idea of private
variables.

Remember that functions are first class values and can be returned
from blocks, functions or, as here, variables in an outer scope can be
bound to those values.

.. code-block:: idio
   :caption: :file:`simple-closures.idio`

   ;; Declare a couple of top level names which we'll assign the functions
   ;; to inside the block.
   ;;
   ;; By convention, functions that modify things have an exclamation
   ;; mark at the end of their names.
   incr-thing := #f
   set-thing! := #f

   {
     ;; n is local to the block and invisible outside the block
     n := 0

     ;; re-bind the top level name "set-thing!" to this anonymous
     ;; function inside the block where n is visible
     set-thing! = function (v) {
       n = v
     }

     ;; ditto for "incr-thing" which doesn't take any arguments
     incr-thing = function () {
       ;; here we'll look to return the current value and increment it
       ;; after
       t := n
       n = n + 1
       t
     }
   }

   ;; out here, n is no longer accessible except through the two
   ;; functions
   printf "value is %d\n" (incr-thing)
   printf "value is %d\n" (incr-thing)
   printf "value is %d\n" (incr-thing)
   set-thing! 37
   printf "value is %d\n" (incr-thing)
   printf "value is %d\n" (incr-thing)

.. code-block:: console

   $ idio simple-closures
   value is 0
   value is 1
   value is 2
   value is 37
   value is 38

.. include:: ./commit.rst
