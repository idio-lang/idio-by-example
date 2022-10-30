.. include:: ./global.rst

####
Case
####

Another conditional expression is ``case`` which uses ``memv`` to
compare lists of values and run a corresponding expression.

Each clause takes the form :samp:`(({v} ...) {expr} ...)` that is a
:lname:`C`-style ``switch`` like:

.. code-block:: c

   bool result;

   switch (c) {
   case 'a':
   case 'e':
   case 'i':
   case 'o':
   case 'u':
       result = 1;
       break;
   default:
       result = 0;
       break;
   }

transforms into:

.. code-block:: idio
   :caption: :file:`simple-case.idio`

   define (vowel? c) {

     (case
      c
      ((#\a #\e #\i #\o #\u) #t)
      (else #f))
   }

   for c in "hello" {
     printf "vowel? %s: %s\n" c (vowel? c)
   }

.. code-block:: console

   $ idio simple-case
   vowel? h: #f
   vowel? e: #t
   vowel? l: #f
   vowel? l: #f
   vowel? o: #t

.. include:: ./commit.rst
