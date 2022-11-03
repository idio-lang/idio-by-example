.. include:: ./global.rst

#######
IDIOLIB
#######

:envvar:`IDIOLIB` is a simple colon-separated list of directories for
:lname:`Idio` to look for library files in.

Non-absolute directories are ignored.

When :program:`idio` starts it will modify any user-supplied
:envvar:`IDIOLIB`:

#. by *prepending* the :file:`lib` directory that corresponds with the
   actual executable being run

#. if you used a *virtualenv*-style setup (where the :program:`idio`
   found on your :envvar:`PATH` is a symlink to a real executable)
   then :program:`idio` will *prepend* the :file:`lib` directory that
   corresponds with the virtualenv being used

In both cases, a :file:`.../lib` will only be prepended if the
executable/symlink `name` was :file:`.../bin/{name}`, ie. the
immediate directory name must be :file:`bin`.

Suppose we've been saving our example files in :file:`/tmp` and
running them from there using :file:`$HOME/.local/bin/idio` as the
executable:

.. code-block:: idio
   :caption: :file:`/tmp/print-IDIOLIB.idio`

   printf "IDIOLIB is %s\n" IDIOLIB

.. code-block:: console

   $ IDIOLIB=/tmp idio print-IDIOLIB
   IDIOLIB is /home/idf/.local/lib/idio/0.3:/tmp

Let's rearrange things into a virtualenv.  We need to symlink :program:`idio`:

.. code-block:: console

   $ mkdir -p /tmp/venv/bin
   $ ln -s $HOME/.local/bin/idio /tmp/venv/bin

   $ IDIOLIB=/tmp /tmp/venv/bin/idio print-IDIOLIB
   WARNING: extend-IDIOLIB: /tmp/venv/lib/idio/0.3 is not accessible
   IDIOLIB is /tmp/venv/lib/idio/0.3:/home/idf/.local/lib/idio/0.3:/tmp

Now that we have a venv we might as well put our example files in it
and then not have to set :envvar:`IDIOLIB`:

.. code-block:: console

   $ mkdir -p /tmp/venv/lib/idio/0.3
   $ mv /tmp/print-IDIOLIB.idio /tmp/venv/lib/idio/0.3

   $ /tmp/venv/bin/idio print-IDIOLIB
   IDIOLIB is /tmp/venv/lib/idio/0.3:/home/idf/.local/lib/idio/0.3

The overall result is :samp:`{virtualenv libdir}:{executable
libdir}:{user's IDIOLIB}`.

.. include:: ./commit.rst
