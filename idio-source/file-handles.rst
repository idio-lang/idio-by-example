.. include:: ./global.rst

############
File Handles
############

File handles are values used for traditional operating system I/O.

We can create input (to be read from) and output (to be written to)
file handles and create file handles from existing (operating system)
file descriptors.

.. code-block:: idio
   :caption: :file:`file-handles.idio`

   fn := "words.txt"
   ofh := open-output-file fn

   ;; hprintf printf's to the given handle
   hprintf ofh "this\nthat\nthe other\n"

   close-handle ofh

   ifh := open-input-file fn

   data := #[]

   ;; read one line (discarding the newline)
   data.0 = read-line ifh

   ;; read all remaining lines (which retains any newlines)
   data.1 = read-lines ifh

   close-handle ifh

   printf "data.0 is %s\n" data.0
   printf "data.1 is %s" data.1

.. code-block:: console

   $ idio file-handles
   data.0 is this
   data.1 is that
   the other

:samp:`open-output-file {file}` is the equivalent of :samp:`open-file
{file} "we"` using :manpage:`fopen(3)`-style mode flags and
``open-input-file`` similarly uses ``"re"`` mode flags.

****************
File Descriptors
****************

File handles can be created around already existing file descriptors
and file descriptors can be extracted from file handles.

.. code-block:: idio
   :caption: :file:`file-names.idio`

   fn := "words.txt"
   ofh := open-output-file fn
   hprintf ofh "this is contents\n"
   close-handle ofh

   ;; open words.txt using open(2)
   fd := libc/open fn libc/O_RDONLY

   printf "fd is %d (a %s)\n" fd (type->string fd)

   ;; wrap fd in a file handle
   fh := open-input-file-from-fd fd

   printf "%s contains %s" fn (read-lines fh)

   ;; go back to the start of the file
   rewind-handle fh

   fd2 := file-handle-fd fh
   printf "fd2 is %d (a %s)\n" fd2 (type->string fd2)

   printf "contains %s" (libc/read fd2 1024)

   ;; closes fd/fd2 as well
   close-handle fh

.. code-block:: console

   $ idio file-names
   fd is 4 (a C/int)
   words.txt contains this is contents
   fd2 is 4 (a C/int)
   contains this is contents

*********
Filenames
*********

Filenames are handled more strictly in :lname:`Idio` than many other
languages.  The :lname:`Idio` source code is expected to be encoded in
UTF-8 and correspondingly, the filenames passed to functions will be
UTF-8 encoded strings.

Filenames in Unix-oriented filesystems have no encoding whatsoever,
they are simple arrays of bytes (excluding ``/`` and ASCII NUL).

Any filename retrieved from the operating system will be tagged as a
*pathname*, a variant of strings.  A pathname and a string are not
*the same* and will fail equivalence tests.

That's inconvenient so there are some facilities to construct
pathnames from :lname:`Idio` strings:

* ``%P"..."`` constructs a pathname-tagged string using the same
  escape codes available to strings

* ``string->pathname`` will return a pathname-tagged string from the
  string passed as an argument

.. code-block:: idio
   :caption: :file:`file-names.idio`

   fn := "words.txt"
   pn := string->pathname fn

   ofh := open-output-file fn
   hprintf ofh "this is contents\n"
   close-handle ofh

   files := glob "*.txt"
   for file in files {
     puts "File: "
     write file				; prints %P"..." rather than ...
     (newline)

     printf "equal? %s? %s\n" fn (equal? file fn)
     printf "equal? %s? %s\n" pn (equal? file pn)
   }

.. code-block:: console

   $ idio file-names
   File: %P"words.txt"
   equal? words.txt? #f
   equal? words.txt? #t

Note that the printed representation of :samp:`{fn}` and :samp:`{pn}`
are the same.  Had we used ``write`` to get the reader-friendly
representation we'd have seen the difference.  The predicate
``pathname?`` may be useful.

**********
Predicates
**********

There are the usual pathname *predicates* such as ``f?``, ``d?`` and
``r?`` which are comparable to familiar shell predicates ``-f``,
``-d`` and ``-r``.

.. code-block:: idio
   :caption: :file:`file-predicates.idio`

   define (test-for fn) {
     (cond
      ((f? fn) {
        printf "%s is a file\n" fn
      })
      ((d? fn) {
        printf "%s is a directory\n" fn
      })
      (else {
        printf "%s is beyond knowledge\n" fn
      }))
   }

   fn := "words.txt"

   test-for "."

   ;; just in case
   if (f? fn) (delete-file fn)

   test-for fn
   touch fn
   test-for fn


.. code-block:: console

   $ idio file-predicates
   . is a directory
   words.txt is beyond knowledge
   words.txt is a file

.. include:: ./commit.rst
