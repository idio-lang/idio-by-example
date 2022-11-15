.. include:: ./global.rst

#########
Pipelines
#########

Pipelines are implemented with another reader operator.

.. note::
   
   In this particular example, we hit a problem with ``-`` which is
   also a reader operator, usually seen as ``(3 - 1)``.

   We could escape this from the reader with ``\-`` however, when the
   arguments come to be expanded into string arguments for the
   external command, ``-`` is recognised as a function which has no
   string form suitable for an external command's arguments.

   As a result we want to force the use of the symbol with ``'-``.

   Alternatively, you could have used :file:`/dev/fd/0`.

.. code-block:: idio
   :caption: :file:`simple-pipeline.idio`

   for (fn text) in '(("foo.txt" "bar.txt")
   		      ("hello" "world")) {
     echo text > fn
   }

   fns := glob "*.txt"

   ls -l fns

   tar cf '- fns | gzip -c | tar tzvf '-

   rm fns

.. code-block:: console

   $ idio simple-pipeline
   -rw-rw-r--. 1 idf idf 6 Nov 14 14:33 bar.txt
   -rw-rw-r--. 1 idf idf 6 Nov 14 14:33 foo.txt
   -rw-rw-r-- idf/idf           6 2022-11-14 14:33 bar.txt
   -rw-rw-r-- idf/idf           6 2022-11-14 14:33 foo.txt
		
*************
Meta-Commands
*************

:lname:`Idio` supports some *meta-commands* when running external
commands.  Meta-commands are prefixes to the pipeline.  We'll look at
a couple here.  Other meta-commands deal with managing the input and
output of the pipeline which are covered in later examples.

bg-job
======

To background a job use the ``bg-job`` prefix:

.. code-block:: idio
   :caption: :file:`bg-job.idio`

   bg-job sleep 5

   ;; do some background processing ourselves...
   libc/sleep 2

   ;; report on all extant jobs
   (jobs)

   ;; display Idio's elapsed time
   printf "t+%ds\n" SECONDS

   ;; twiddle thumbs
   (wait)

   printf "t+%ds\n" SECONDS

.. code-block:: console

   $ idio bg-job

   job      0: a?=#f: ((sleep 5))
	      PID fl  status       cmd
     proc: 156308 !C  (running)    (sleep 5)
     flags: C - completed; !C - not completed; S - stopped
   Started at Mon Nov 14 14:50:22 2022, 2s ago; 
   t+3s
   t+6s

``jobs`` displays some status information about the outstanding jobs.
Each job is a pipeline of processes, albeit a single process in this
example.

.. note::

   The ``job 0`` is slightly disingenuous as it is the Process Group
   ID of the job, which is only set for an interactive shell.

   You can validate that with something like:

   .. code-block:: console

      $ idio
      Idio> load "bg-job"
      ...

``wait`` can be passed a list of jobs to wait for explicitly or, as in
this case, all outstanding jobs.

time
======

To get a report on the real, user and system time used by a job use
the ``time`` prefix.

This isn't something you'd normally do in a script but it may be more
convenient debug than extracting the information from the job's data.

.. code-block:: idio
   :caption: :file:`time.idio`

   time sleep 5
   printf "t+%ds\n" SECONDS
   (wait)

.. code-block:: console

   $ idio time
   t+5s
   Real 5.012
   User 0.004
   Syst 0.008

.. note::

   A job's timings are kept in the job's structure.  They are
   *reported* as part of the job notification mechanisms.  For an
   interactive shell this is commonly just before the prompt is
   printed.  In a script, the notification mechanism is not so
   regimented.

   If the pipeline is the last expression in a script (and does not
   fail), as might have been the case here, then there is little
   chance for the notification mechanisms to fire before :lname:`Idio`
   shuts down.

   Calling ``wait`` will usually cause the notification mechanisms to
   fire.

.. include:: ./commit.rst
