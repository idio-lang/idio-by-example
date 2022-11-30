.. include:: ./global.rst

####
zlib
####

The ``zlib`` module is a simple wrapper around the zlib functions.

``gzip-compress`` and ``gzip-decompress`` manipulate *gzip* format
compression files.

``zlib-compress`` and ``zlib-decompress`` manipulate *zlib* format
compression files.

``deflate`` and ``inflate`` are closer to the native zlib functions
allowing manipulation of *gzip*, *zlib* and *raw* format compression
streams.

The following show how we can prepare and consume *gzip* format files
for use by external commands.

****************
gzip compression
****************

.. code-block:: idio
   :caption: :file:`gzip-compression.idio`

   import zlib

   tmp-file := (libc/make-tmp-file)

   ;; Use a non-ASCII code point
   str := "Ħello World\n"

   ;; write the gzip compressed string out to tmp-file
   ish := open-input-string str
   ofh := open-output-file tmp-file
   puts (gzip-compress ish) ofh
   close-handle ofh

   ;; zcat with external command

   ;; you may need gzcat on SunOS/Mac OS/OpenBSD
   zcat tmp-file

   rm tmp-file

.. code-block:: console

   $ idio gzip-compression
   Ħello World

******************
gzip decompression
******************

.. code-block:: idio
   :caption: :file:`gzip-decompression.idio`

   import zlib

   tmp-file := (libc/make-tmp-file)

   with-output-to tmp-file (function #n {
     puts "Ħello World\n"
   })

   ;; you might need -f on OpenBSD (as the file is short)
   gzip tmp-file

   gz-file := append-string tmp-file ".gz"

   ;; gzip decompress the contents of gz-file
   ifh := open-input-file gz-file
   puts (gzip-decompress ifh)
   close-handle ifh

   rm gz-file

.. code-block:: console

   $ idio gzip-decompression
   Ħello World

.. include:: ./commit.rst
