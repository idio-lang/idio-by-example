.. include:: ./global.rst

#######
C Types
#######

:lname:`Idio` makes use of :lname:`C` base types to interact with the
operating system.  In general, if it is operating system-oriented it
will be using :lname:`Idio` wrappers to :lname:`C` base types or
derivatives for arguments and return values.

In particular, :lname:`Idio` does not perform any implicit type
casting.  If you are manipulating a ``pid_t`` your (portable) code
cannot know if it is implemented by an ``int`` or ``long`` and so you
must reference a ``libc/pid_t`` [#]_ throughout.  The functions
expecting a ``libc/pid_t`` will error if you pass the wrong C type.

Many :lname:`Idio` functions wrapping :lname:`C` library functions
return "pointers" to structs (often where in :lname:`C` a pointer to
such a struct would have been passed to the library function as an
argument).  These are :lname:`Idio` values containing the underlying
:lname:`C` type so, here, a ``C/pointer``.

The struct pointer can be tagged with :abbr:`C Structure
Identification (CSI)` information which can be verified by subsequent
recipients and contains some structure information.

.. code-block:: idio
   :caption: :file:`simple-libc.idio`

   ;; libc/gettimeofday returns a C/pointer to a libc/struct-timeval
   tv0 := (libc/gettimeofday)

   ;; sleep(3) would normally be expecting a C unsigned int (or 'uint
   ;; in Idio) however this is a rare occasion when a fixnum can be
   ;; passed to C!  Otherwise we'd need to convert a fixnum into a
   ;; specific C base type, something like: (C/integer-> 1 'uint)
   libc/sleep 1

   tvd := libc/subtract-struct-timeval (libc/gettimeofday) tv0

   ;; the default printing format for a libc/struct-timeval is a stylised
   ;; floating point number
   printf "sleep 1 took %ss\n" tvd

   ;; we can pick it apart ourselves
   printf "aka %ds and %06dus\n" \
          (libc/struct-timeval-ref tvd 'tv_sec) \
          (libc/struct-timeval-ref tvd 'tv_usec)

   ;; more conveniently but slightly slower
   printf "aka %ds and %06dus\n" \
          tvd.'tv_sec \
          tvd.'tv_usec

   ;; What do we know about tvd?
   printf "tvd isa %s isa %s: " (type->string tvd) (C/pointer-name tvd)
   for member in (C/pointer-members tvd) {
     ;; Idio knows the libc/struct-timeval member values have libc/time_t
     ;; and libc/suseconds_t types however it will resolve these into
     ;; their underlying base types
     ;;
     ;; the types reported are platform-specific
     printf "%s isa %s; " member (type->string tvd.member)
   }
   printf "\n"

.. code-block:: console

   $ idio simple-libc
   sleep 1 took 1.000383s
   aka 1s and 000383us
   aka 1s and 000383us
   tvd isa C/pointer isa libc/struct-timeval: tv_sec isa C/long; tv_usec isa C/long; 

.. [#] Although the :lname:`C` namespace is flat, the :lname:`C`
       *typedefs* are associated with the library that introduces
       them.  In this case, ``pid_t`` becomes ``libc/pid_t``.

.. include:: ./commit.rst
