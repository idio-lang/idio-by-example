.. include:: ./global.rst

#########
Subshells
#########

It is often convenient to make multiple temporary changes to the
program state (current working directory, environment etc.) in a
subshell such that the main program does not have to reset its own
values.

In :lname:`Idio`, subshells use the ``{{...}}`` notation.

As a far too involved example, let's create a *virtualenv* and run a
script from it:

.. code-block:: idio
   :caption: :file:`simple-subshell.idio`

   printf "%d in %s: IDIOLIB is %s\n" PID PWD IDIOLIB

   {{
     tmp-dir := (libc/make-tmp-dir)

     ;; change directory in the subshell
     cd tmp-dir

     lib-dir := #S{lib/idio/${IDIO_MM_VERSION}}

     mkdir -p bin lib-dir

     ;; IDIO_EXE is the absolute path of the running executable
     ln -s IDIO_EXE bin

     ;; but IDIO_EXE is versioned, idio.M.N, and we want to use the
     ;; command idio
     ln -s #S{idio.${IDIO_MM_VERSION}} bin/idio

     ;; let's see what we did, there
     ls -l #S{${tmp-dir}/bin}

     with-output-to-file #S{${lib-dir}/tell-me.idio} (function #n {
       ;; Using an interpolated string serves no purpose, here, as there
       ;; is nothing to expand, however, it is habit-forming when
       ;; generating code
       puts #S{
   ;; this is the script for the virtualenv idio running in the subshell
   printf "%d in %s: IDIOLIB is %s\n" PID PWD IDIOLIB
   }
     })

     ;; report on the subshell
     printf "%d in %s: IDIOLIB is %s\n" PID PWD IDIOLIB

     ;; change PATH in the subshell
     PATH = #S{${tmp-dir}/bin:${PATH}}

     idio tell-me

     cd HOME

     rm -rf tmp-dir
   }}

   printf "%d in %s: IDIOLIB is %s\n" PID PWD IDIOLIB

.. code-block:: console

   $ idio simple-subshell
   226136 in /home/idf: IDIOLIB is /home/idf/.local/lib/idio/0.3
   total 0
   lrwxrwxrwx. 1 idf idf  8 Nov 15 11:47 idio -> idio.0.3
   lrwxrwxrwx. 1 idf idf 29 Nov 15 11:47 idio.0.3 -> /home/idf/.local/bin/idio.0.3
   226138 in /tmp/idio-tmp-EIUqVO: IDIOLIB is /home/idf/.local/lib/idio/0.3
   226143 in /tmp/idio-tmp-EIUqVO: IDIOLIB is /tmp/idio-tmp-EIUqVO/lib/idio/0.3:/home/idf/.local/lib/idio/0.3
   226136 in /home/idf: IDIOLIB is /home/idf/.local/lib/idio/0.3
		
Here, you can see the Process ID associated with the subshell is
different to the parent and is different to the :program:`idio`
running from the subshell.

.. include:: ./commit.rst
