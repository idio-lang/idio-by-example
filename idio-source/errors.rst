.. include:: ./global.rst

######
Errors
######

:lname:`Idio` handles errors through the use of *conditions*.
Conditions, or rather, their handlers, are a complex subject but at
this stage we can say that when we do something problematic
:lname:`Idio` will raise a condition and, as we haven't done anything,
the default handlers will cause :lname:`Idio` to exit.

We can write our own handler around particular blocks of code where we
can react to conditions we anticipate or raise the condition to a
higher authority for them to decide.

Here we need to be a little leery as conditions and handlers are
working subtly differently to more common exception mechanisms.

In :lname:`Idio` we are handling the condition raised from an
individual expression within the dynamic scope (the runtime, if you
will) of the block of code we are wrapping.  The computation **has
not** been unwound so we are at liberty to have the failed expression
appear to have returned with a suitable value.

Of course, with a lot of problems, a missing file, say, there is
nothing useful to be substituted as the return value for
``open-input-file``, say.  In this case we would want to return from
the ``trap`` truncating the computation.  Such a ``trap-return``
behaves like other languages' ``try/except`` blocks.

``trap`` itself takes three arguments: a condition type (or list of
condition types); a unary function which will be passed the condition
instance; and the expression (usually a block) that the trap protects.

If no condition is raised, the trap returns the result of the last
expression evaluated in the block.

Let's try some risky behaviour.  Notice in all these examples, the
condition is raised in a function that is lexically outside of the
block the traps are protecting but in a function called during the
running of the block and hence why the condition is captured.

.. code-block:: idio
   :caption: :file:`error-handling-1.idio`

   define (div n d) {
     ;; risky division
     n / d
   }

   trap ^rt-divide-by-zero-error (function (c) {
     ;; send a message to stderr, of course
     eprintf "caught a risky division!\n"

     ;; the division failed so let's return #f in its place
     #f
   }) {
     printf "a: 1 / 0 is %s\n" (div 1 0)

     ;; we will add 3 to the result of calling div
     printf "b: 3 + (1 / 0) is %s\n" (3 + (div 1 0))
   }
      
   printf "all done!\n"

.. code-block:: console

   $ idio error-handling-1
   caught a risky division!
   a: 1 / 0 is #f
   caught a risky division!

   reset-condition-handler: ^rt-parameter-type-error: #<SI ^rt-parameter-type-error message:"bad parameter type: '#f' a constant is not a number" location:"x.idio:line 6:binary-+" detail:"">
   reset-condition-handler: restoring ABORT continuation #1: "ABORT to main => exit (probably badly)"
   sigsetjmp: script failed: exit (1)

Hmm.  Not so clever, ``#f`` is not a valid type for ``+``.

Let's try behaving like normal ``try/except`` handlers by calling
``trap-return``:

.. code-block:: idio
   :caption: :file:`error-handling-2.idio`

   define (div n d) {
     n / d
   }

   trap ^rt-divide-by-zero-error (function (c) {
     eprintf "caught a risky division!\n"

     ;; the division failed so let's return from the whole trap
     trap-return #f
   }) {
     printf "a: 1 / 0 is %s\n" (div 1 0)

     printf "b: 3 + (1 / 0) is %s\n" (3 + (div 1 0))
   }
   ;; trap did return #f but we didn't capture it

   printf "all done!\n"

.. code-block:: console

   $ idio error-handling-2
   caught a risky division!
   all done!

That's more familiar behaviour.

Finally, just for completeness, let's pass the condition up to a
higher authority -- although we know in this case it's likely to be
the default handlers which will exit the program:

.. code-block:: idio
   :caption: :file:`error-handling-3.idio`

   define (div n d) {
     n / d
   }

   trap ^rt-divide-by-zero-error (function (c) {
     ;; we can make a call to get a familiar report but with our own
     ;; message
     condition-report "risky division!" c

     ;; the division failed so let's raise the condition to a higher
     ;; authority
     raise c
   }) {
     printf "a: 1 / 0 is %s\n" (div 1 0)

     printf "b: 3 + (1 / 0) is %s\n" (3 + (div 1 0))
   }

   ;; In general, we may or may not get here depending on any other trap
   ;; handling between us and the default handlers.  In this case, there
   ;; are no other handlers and the default handlers will have exit the
   ;; program

   printf "all done!\n"

.. code-block:: console

   $ idio error-handling-3
   risky division!:x.idio:line 3:binary-/:^rt-divide-by-zero-error:bignum divide by zero: number(s) '(1 0)'

   reset-condition-handler: ^rt-divide-by-zero-error: #<SI ^rt-divide-by-zero-error message:"bignum divide by zero" location:"x.idio:line 3:binary-/" detail:"" number:(1 0)>
   reset-condition-handler: restoring ABORT continuation #1: "ABORT to main => exit (probably badly)"
   sigsetjmp: script failed: exit (1)

Here you can see our ``"risky division!"`` report with much of the
same information before the expected exit of the program.

.. include:: ./commit.rst
