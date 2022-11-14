.. include:: ./global.rst

###############
Temporary Files
###############

There are a number of variations on the theme of creating temporary
files and directories.  Here we'll look at ``make-tmp-file`` and
``make-tmp-dir``.

.. code-block:: idio
   :caption: :file:`temporary-file.idio`

   ;; use the default "idio-tmp-" filename root
   fn := (libc/make-tmp-file)

   printf "fn is %s\n" fn

   fh := open-output-file fn

   hprintf fh "Hello World\n"

   close-handle fh

   sb := libc/stat fn

   printf "fn is %d bytes long\n" sb.st_size

   libc/unlink fn

.. code-block:: console

   $ idio temporary-file
   fn is /tmp/idio-tmp-9jQIws
   fn is 12 bytes long

Alternatively, you can create a temporary directory:

.. code-block:: idio
   :caption: :file:`temporary-dir.idio`

   dn := libc/make-tmp-dir "secret"

   printf "dn is %s\n" dn

   fn := append-string dn "/foo"
   fh := open-output-file fn

   hprintf fh "Hello World\n"

   close-handle fh

   sb := libc/stat fn

   printf "%s is %d bytes long\n" fn sb.st_size

   ;; clean up everything
   rm -rf dn

.. code-block:: console

   $ idio temporary-dir
   dn is /tmp/secretIcDuyT
   /tmp/secretIcDuyT/foo is 12 bytes long

.. include:: ./commit.rst
