.. include:: ./global.rst

##############
Command Errors
##############

By default, :lname:`Idio` will exit with the same exit status as any
external command that fails (including signals).  This aggressive
nature is deliberate.

Some *filthy casuals* might consider that to be inconvenient so you
can suppress various aspects of this behaviour.

For each of these examples you can try running with the value set to
``#f``, the default, or ``#t`` by removing the comment character
``;``.

******************
suppress-pipefail!
******************

This is much like the *inverse* of the shell's ``set -o pipefail``
setting.  It defaults to ``#f``, ie. :lname:`Idio` will fail if any of
the processes in a pipeline fail.

.. code-block:: idio
   :caption: :file:`suppress-pipefail.idio`

   ;suppress-pipefail! = #t

   false | true

   (jobs)

.. code-block:: console

   $ idio suppress-pipefail
   $ echo $?
   1

   # edit suppress-pipefail.idio

   $ idio suppress-pipefail

   job      0: a?=#f: ((false) (true))
	      PID fl  status       cmd
     proc: 157346  C  (exit 1)     (false)
     proc: 157347  C  (exit 0)     (true)
     flags: C - completed; !C - not completed; S - stopped
    Started at Mon Nov 14 15:30:21 2022, ran for 0s
    R 0.011; U 0.005; S 0.014
		
***********************
suppress-exit-on-error!
***********************

This is much like the *inverse* of the shell's ``set -e`` (and ``set
-E`` if available) setting, defaulting to ``#f``, ie. :lname:`Idio`
will fail if the last process in a pipeline fails.

.. code-block:: idio
   :caption: :file:`suppress-exit-on-error.idio`

   ;suppress-exit-on-error! = #t

   true | false

   (jobs)

.. code-block:: console

   $ idio suppress-exit-on-error
   $ echo $?
   1

   # edit suppress-exit-on-error.idio

   $ idio suppress-exit-on-error


   job      0: a?=#f: ((true) (false))
	      PID fl  status       cmd
     proc: 157515  C  (exit 0)     (true)
     proc: 157516  C  (exit 1)     (false)
     flags: C - completed; !C - not completed; S - stopped
    Started at Mon Nov 14 15:36:03 2022, ran for 0s
    R 0.012; U 0.003; S 0.017
		
******************************
suppress-async-command-report!
******************************

There is no equivalent to this in the shell.  :lname:`Idio` does not
exit if an asynchronous command fails but it will report any failure
by default.

.. code-block:: idio
   :caption: :file:`suppress-async-command-report.idio`

   ;suppress-async-command-report! = #t

   iph := pipe-from false

   printf "iph is %s\n" iph

   close-handle iph
   
   (jobs)

.. code-block:: console

   $ idio suppress-async-command-report
   default-racse-handler: this async job result has been ignored:[158530]:
   job 158531: a?=#t: ((false))
	      PID fl  status       cmd
     proc: 158531  C  (exit 1)     (false)
     flags: C - completed; !C - not completed; S - stopped
    Started at Mon Nov 14 15:54:41 2022, ran for 0s
    R 0.009; U 0.003; S 0.006


   iph is #<H opr!   4:"/dev/fd/4":1:0>

   # edit suppress-async-command-report.idio

   $ idio suppress-async-command-report
   iph is #<H opr!   4:"/dev/fd/4":1:0>
		
.. note::

   #. "racse" stands for Report Asynchronous Command Status Error

   #. Asynchronous commands *are* given a Process Group ID even in
      scripts

.. include:: ./commit.rst
