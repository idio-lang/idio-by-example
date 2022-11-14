.. include:: ./global.rst

#################
Collecting Output
#################

``collect-output`` is a pipeline meta-command which returns the
command's output as a string with any trailing newlines removed.  This
is the equivalent to the shell's ``$(...)`` *Command Substitution*.

.. code-block:: idio
   :caption: :file:`collect-output.idio`

   ;; for simple commands
   sysname := collect-output uname -s

   printf "sysname is %s\n" sysname

   ;; or pipelines
   count := collect-output uname -s | wc -c

   printf "sysname is %s characters\n" count

.. code-block:: console

   $ idio collect-output
   sysname is Linux
   sysname is 6 characters
		
*********************
Asynchronous Commands
*********************

The job being run for ``collect-output`` is termed an *asynchronous
command*.  Whether it succeeds or fails does not affect whether
:lname:`Idio` chooses to exit itself as it is not on the critical path
of :lname:`Idio` itself.

:lname:`Idio` will, by default, issue a report if an asynchronous
command fails.

.. include:: ./commit.rst
