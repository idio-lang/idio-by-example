.. include:: ./global.rst

##################
Idio Object System
##################

:lname:`Idio` uses a variant of :ref-author:`Gregor Kiczales`
TinyCLOS_ itself a simplified version of the *Common Lisp
Object System*.

In the Idio Object System (IOS) classes have super-classes and slots
(or fields) in a familiar way, however, the functions that operate on
classes are not elements of the classes but are, so-called, generic
functions which will can be applied to any set of arguments.

You then define methods, tied to the generic function by name, which
declare that for given parameter classes, use this method.

IOS supports `multiple dispatch
<https://en.wikipedia.org/wiki/Multiple_dispatch>`_ (aka
*multimethods*) where all of the arguments are relevant to the choice
of method not just one.

Let's try the SpaceObject collision example from the Wikipedia
multiple dispatch page:

.. code-block:: idio
   :caption: :file:`multiple-dispatch.idio`

   import object

   ;; no superclass
   define-class SpaceObject

   define-class Asteroid SpaceObject (size)

   define-class Spaceship SpaceObject (size)

   ;; very terse methods returning a string!
   define-method (collide-with (o1 Asteroid)  (o2 Spaceship)) "a/s"
   define-method (collide-with (o1 Spaceship) (o2 Asteroid))  "s/a"
   define-method (collide-with (o1 Spaceship) (o2 Spaceship)) "s/s"
   define-method (collide-with (o1 Asteroid)  (o2 Asteroid))  "a/a"

   define-method (collide (o1 SpaceObject) (o2 SpaceObject)) {
     if (and ((slot-ref o1 'size) gt 100)
	     ((slot-ref o2 'size) gt 100)) "Big boom!" (collide-with o1 o2)
   }

   so1 := make-instance Asteroid :size 101
   so2 := make-instance Spaceship :size 300

   printf "collide so1 so2: %s\n" (collide so1 so2)

   so3 := make-instance Asteroid :size 10
   so4 := make-instance Spaceship :size 10

   printf "collide so3 so4: %s\n" (collide so3 so4)

   so5 := make-instance Spaceship :size 101
   so6 := make-instance Spaceship :size 10

   printf "collide so5 so6: %s\n" (collide so5 so6)

The output has been chopped for readability:

.. code-block:: console

   $ idio multiple-dispatch
   collide so1 so2: Big boom!
   collide so3 so4: a/s
   collide so5 so6: s/s

.. include:: ./commit.rst
