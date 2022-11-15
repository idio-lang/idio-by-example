.. include:: ./global.rst

###################
Logical Expressions
###################

:lname:`Idio` allows you to use external commands in logical and
conditional expressions.  An external command is "true" if it exited
with a status of 0 (zero).  Any other form of exit is a logical
"false".

This would normally fly in the face of the aggressive behaviour of
:lname:`Idio` regarding external command exits but the effective
behaviour from using command logic is far more useful and all "exit on
error" code is implicitly suppressed in logical and conditional
expressions.

.. code-block:: idio
   :caption: :file:`command-logic.idio`

   ;; fall through the logical combination of five external commands
   (or (false)
       (not (true))
       (and (true)
   	 (not false)
   	 (false))
       (puts "All failed\n"))

   ;; a more familiar test using an pipeline of external commands
   if (env | grep SECRETSQUIRREL) {
     puts "Okay, Secret!\n"
   } {
     puts "Nothing to do.\n"
   }

.. code-block:: console

   $ idio command-logic
   All failed
   Nothing to do.
		
.. include:: ./commit.rst
