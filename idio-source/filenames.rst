.. include:: ./global.rst

#########
Filenames
#########

Filenames are handled more strictly in :lname:`Idio` than many other
languages.  The :lname:`Idio` source code is expected to be encoded in
UTF-8 and correspondingly, the strings-as-filenames passed to
functions will be UTF-8 encoded strings.

Filenames in Unix-oriented filesystems have no encoding whatsoever,
they are simple arrays of bytes (excluding ASCII NUL) and using ``/``
as a directory separator.

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

.. include:: ./commit.rst
