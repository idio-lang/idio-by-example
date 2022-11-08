.. include:: ./global.rst

############
File Handles
############

File handles are values used for traditional operating system I/O.

We can create input (to be read from) and output (to be written to)
file handles and can create file handles from existing (operating
system) file descriptors.

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
   :caption: :file:`file-descriptors.idio`

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

   $ idio file-descriptors
   fd is 4 (a C/int)
   words.txt contains this is contents
   fd2 is 4 (a C/int)
   contains this is contents

.. include:: ./commit.rst
