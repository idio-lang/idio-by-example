.. include:: ./global.rst

##############
Command Values
##############

One of the primary uses of :lname:`Idio` is to orchestrate commands
for which the value is the exit status of the command and can be used
as a boolean in (logical) expressions.

.. code-block:: idio
   :caption: :file:`print-sleep.idio`

   printf "sleep 1 is %s\n" (sleep 1)

   if (sleep 1) {
     printf "still tired!\n"
   }

   printf "done sleeping!\n"

Here, you'll see:

.. code-block:: console

   $ idio print-sleep
   sleep 1 is #t
   still tired!
   done sleeping!
		
Alternatively, replacing ``sleep 1`` with a call to the external
command ``false``:

.. code-block:: idio
   :caption: :file:`print-false-1.idio`

   printf "false is %s\n" (false)

   if (false) {
     printf "untrue?\n"
   } {
     printf "definitely false!\n"
   }

   printf "done falsifying!\n"

Here, you'll see:

.. code-block:: console

   $ idio print-false-1

Nothing!

That's because on the first line we ran an external command that fails
and the default behaviour of :lname:`Idio` is much like enabling ``set
-e`` in other shells.  We can disable that (later!) or comment the
first line out:

.. code-block:: idio
   :caption: :file:`print-false-2.idio`

   ;printf "false is %s\n" (false)

   if (false) {
     printf "untrue?\n"
   } {
     printf "definitely false!\n"
   }

   printf "done falsifying!\n"

Here, you'll see:

.. code-block:: console

   $ idio print-false-2
   definitely false!
   done falsifying!

This time ``false`` doesn't cause :lname:`Idio` to exit because it is
being used in a logical expression, much like in a regular shell:

.. code-block:: bash

   if false ; then
       echo "untrue?"
   else
       echo "definitely false!"
   fi

********
Thoughts
********

There's generally little value to be had in printing the value of an
external command as it can only be ``#t`` or ``#f`` (and you won't see
``#f`` most of the time as :lname:`Idio` will have exited).  The
details of the command are kept in Job Control data.

.. include:: ./commit.rst
