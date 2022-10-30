.. include:: ./global.rst

#######
Structs
#######

Structs are named-indexed collections of references to values.

Structs have two parts: a *type* which declares the *field* names and
an optional *parent* struct type; and an *instance* of a struct type
which has a reference to a value for each field.

.. code-block:: idio
   :caption: :file:`simple-structs.idio`

   ;; define a struct foo with fields a and b -- the define-struct form
   ;; does not define a parent type
   define-struct foo a b

   ;; create an instance of a foo with values 3 and 6
   foo1 := make-foo 3 6

   ;; a function to check the fields
   define (checker si) {
     if (not (foo? si)) {
       printf "a %s is not a foo: %s\n" (type->string si) si
     } {
       if ((struct-instance-ref si 'b) gt (struct-instance-ref si 'a)) {
         printf "b > a for %s\n" si
       } {
         printf "b <= a for %s\n" si
       }
     }
   }

   checker "bob"

   checker foo1

   ;; meddle with foo1
   foo1.b = 1
   checker foo1

.. code-block:: console

   $ idio simple-structs
   a string is not a foo: bob
   b > a for #<SI foo a:3 b:6>
   b <= a for #<SI foo a:3 b:1>

.. include:: ./commit.rst
