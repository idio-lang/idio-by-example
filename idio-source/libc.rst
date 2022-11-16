.. include:: ./global.rst

####
libc
####

``libc`` is a module which provides :lname:`Idio` interfaces to
:lname:`C` standard library functions.

As things stand, there is enough functionality for :lname:`Idio` to
work but there has been limited effort to be any more comprehensive.

*********
API Style
*********

All :lname:`C` names should transliterate exactly into :lname:`Idio`
names.  The idea, here, is that you can cut'n'paste from manual pages
into :lname:`Idio`.

Most :lname:`Idio` interfaces will raise a condition, usually an
instance of ``^rt-system-error``, if the underlying :lname:`C` API
reports an error.

In general, values returned from ``libc`` functions will be passed
opaquely back into other ``libc`` functions.  Periodically, you may
want to print values (which should do something sensible) or pass an
original value as an argument.  The latter will require some value
construction.

Most :lname:`Idio` interfaces will return a ``C/pointer`` value where
you would have had to allocate some memory and pass a pointer into the
:lname:`C` interface.  The returned value will be tagged so as to free
the memory when it is garbage collected.

Where possible, such ``C/pointer`` values will have been tagged with
some C Structure Identification (CSI) data which can include structure
names and members, accessors and printer.

Portability concerns means that some :lname:`C` structures have
limited support.  A case in point is ``libc/struct-rusage`` which only
handles the ``ru_utime`` and ``ru_stime`` members.

Corresponding standard library typedefs are available (mapping to the
underlying ``C`` base types) which you should use for portability
reasons.

Remember, the ``C`` comparison functions require that their arguments
be the same type -- there is no implicit integer promotion in
:lname:`Idio`.  Correspondingly, given a frequent comparison with
``0`` (zero) there are some standard ``C`` and ``libc`` "zero" values:

* ``C/0i`` and ``C/0u`` are :lname:`C` 0 as signed and unsigned
  integers

* ``libc/0pid_t``, ``libc/0uid_t``, ``libc/0git_t`` are portable
  values of :lname:`C` 0 as ``libc/pid_t``, ``libc/uid_t`` and
  ``libc/gid_t`` respectively

* you can construct any such convenience value with:

  :samp:`C/integer-> 0 {type}`

**************
passwd Example
**************

.. code-block:: idio
   :caption: :file:`simple-passwd.idio`

   import libc

   printf "EUID is %d, a %s\n" EUID (type->string EUID)

   ;; C comparisons must be done with like-types
   printf "I am %sroot\n" (if (C/== EUID 0uid_t) "" "not ")

   pw := getpwuid EUID

   if pw {
     ;; We know a non-#f result from getpwuid will be a struct-passwd
     ;; which is a C/pointer but we can do some clarity-inducing double
     ;; checking
     if (C/pointer? pw) {
       printf "pw is a C/pointer to a %s\n"  (C/pointer-name pw)
       printf "members: %@\n" (C/pointer-members pw)
     }

     ;; the accessor is (struct-passwd-ref pw pw_name) but "." can figure
     ;; it out from the CSI
     printf "%s " pw.pw_name

     ;; gecos might be empty
     gecos := pw.pw_gecos
     if ((string-length gecos) gt 0) {
       printf "is %s" gecos
     }
     printf "\n"

     ;; the CSI default printer deliberately uses a passwd(5)-style
     ;; output
     printf "The struct-passwd is %s\n" pw
   } {
     printf "No passwd entry\n"
   }

.. code-block:: console

   $ idio simple-passwd
   EUID is 1000, a C/uint
   I am not root
   pw is a C/pointer to a libc/struct-passwd
   members: pw_name pw_passwd pw_uid pw_gid pw_gecos pw_dir pw_shell
   idf is Ian Fitchet
   The struct-passwd is #<CSI libc/struct-passwd idf:x:1000:1000:Ian Fitchet:/home/idf:/bin/bash>

**************
rlimit Example
**************

Here's how the test suite reduces the number of file descriptors to
something more manageable to test what happens when they run out.

.. code-block:: idio
   :caption: :file:`simple-rlimit-1.idio`

   ;; print out the current state using an external command
   ulimit -n

   ;; construct a libc/rlim_t with the value 256
   lim := C/integer-> 256 libc/rlim_t

   ;; get the current rlimit
   rl := libc/getrlimit libc/RLIMIT_NOFILE

   ;; if it's greater than 256, set it to 256
   if (C/> (libc/struct-rlimit-ref rl 'rlim_cur) lim) {
     libc/struct-rlimit-set! rl 'rlim_cur lim
     libc/setrlimit libc/RLIMIT_NOFILE rl
   }

   ulimit -n

.. code-block:: console

   $ idio simple-rlimit-1
   1024
   256

The first example uses the ref and set accessors explicitly as it is
"quicker" (by avoiding having to look the accessors up in the CSI
information at runtime).  Those with fewer bits than time might
prefer:

.. code-block:: idio
   :caption: :file:`simple-rlimit-2.idio`

   ulimit -n

   lim := C/integer-> 256 libc/rlim_t

   rl := libc/getrlimit libc/RLIMIT_NOFILE

   if (C/> rl.rlim_cur lim) {
     rl.rlim_cur = lim
     libc/setrlimit libc/RLIMIT_NOFILE rl
   }

   ulimit -n

.. code-block:: console

   $ idio simple-rlimit-2
   1024
   256

You'll notice that the "set" has become an assignment.

.. include:: ./commit.rst
