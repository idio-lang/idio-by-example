.. include:: ./global.rst

##############
Path Functions
##############

There are a number of path manipulation functions, primarily for
manipulating :envvar:`PATH`-like variables.

.. warning::

   As these functions intend to alter a variable, like :var:`PATH`,
   you **must** pass the symbol for the variable ``'PATH`` (otherwise
   :lname:`Idio` will evaluate the variable and pass a string.

.. code-block:: idio
   :caption: :file:`simple-path-mods.idio`

   P := "/a/b:/c/d"

   path-append 'P "/e/f"

   printf "P is %s\n" P

   ;; only if "/a/b" is not already in P
   path-append-unique 'P "/a/b"

   printf "P is %s\n" P

   path-remove 'P "/c/d"

   printf "P is %s\n" P

   ;; append only if /g/h is a directory
   path-append 'P "/g/h" :test d?

   printf "P is %s\n" P

   ;; foolishly duplicate P
   path-append 'P P

   printf "P is %s\n" P

   ;; clean it up (first instance of each element)
   P = trim-path P

   printf "P is %s\n" P

.. code-block:: console

   $ idio simple-path-mods
   P is /a/b:/c/d:/e/f
   P is /c/d:/e/f:/a/b
   P is /e/f:/a/b
   P is /e/f:/a/b
   P is /e/f:/a/b:/e/f:/a/b
   P is /e/f:/a/b

There are functions for :program:`cd`/:program:`pushd`/:program:`popd`
(and a reminder that you must wrap the function in parenthesis if not
passing any arguments).  These functions maintain the value of
:envvar:`PWD`.

There is a ``normalize-pathname`` function and simple
``dirname-pathname`` and ``basename-pathname`` functions (named to
avoid clashing with the similar external commands):

.. code-block:: idio
   :caption: :file:`simple-cd.idio`

   tmpdir := "/tmp/a/b/c/d"
   mkdir -p tmpdir

   cd tmpdir

   printf "pwd: "
   ;; run the external command pwd
   (pwd)

   printf "PWD: %s\n" PWD

   printf ".  is %s\n" (normalize-pathname ".")
   printf ".. is %s\n" (normalize-pathname "..")
   printf "dirname for PWD is %s\n" (dirname-pathname PWD)
   printf "basename for PWD is %s\n" (basename-pathname PWD)

.. code-block:: console

   $ idio simple-path-mods
   pwd: /tmp/a/b/c/d
   PWD: /tmp/a/b/c/d
   .  is /tmp/a/b/c/d
   .. is /tmp/a/b/c
   dirname for PWD is /tmp/a/b/c
   basename for PWD is d

.. include:: ./commit.rst
