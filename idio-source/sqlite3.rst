.. include:: ./global.rst

#######
sqlite3
#######

The ``sqlite3`` module is a simple wrapper around the sqlite3 functions.

.. code-block:: idio
   :caption: :file:`simple-sqlite3.idio`

   import sqlite3

   ;; we'll use a temporary file for this db
   db-file := (libc/make-tmp-file)

   db = sqlite3-open db-file

   ;; sqlite3-exec will accept multiple SQL statements separated by ;
   sqlite3-exec db "
   create table foods   (id integer primary key, name text, type text);
   "

   ;; a prepared statement using named parameters.
   stmt := sqlite3-prepare db "insert into foods values (NULL, :name, :type);"

   ;; bind values to named parameters
   sqlite3-bind stmt ":name" "apple" ":type" "fruit"
   sqlite3-step stmt
   sqlite3-reset stmt

   ;; old-school knowledge of parameter indices
   sqlite3-bind stmt 1 "banana" 2 "fruit"
   sqlite3-step stmt
   sqlite3-reset stmt

   ;; individual bindings -- only accepts parameter indices
   sqlite3-bind-text stmt 1 "carrot"
   sqlite3-bind-text stmt 2 "vegetable"
   sqlite3-step stmt
   sqlite3-reset stmt

   ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

   ;; sqlite3-exec accepts a callback function for rows retrieved
   sqlite3-exec db "select * from foods where type = 'fruit';" (function (cols names) {
     printf "callback: %s\n" cols
   })

   ;; a row at a time
   stmt = sqlite3-prepare db "select * from foods where id = ?;"
   sqlite3-bind stmt 1 1
   printf "id 1: %s\n" (sqlite3-step stmt)
   sqlite3-reset stmt

   ;; pick out columns from the result
   stmt = sqlite3-prepare db "select * from foods where id = ?;"
   sqlite3-bind stmt 1 3
   sqlite3-step stmt
   printf "cols: %s is a %s\n" (sqlite3-column stmt 1) (sqlite3-column stmt 2)
   sqlite3-reset stmt

   ;; db (and any associated statements) will be finalized in due course,
   ;; otherwise
   sqlite3-close db

   rm db-file

.. code-block:: console

   $ idio simple-sqlite3
   callback: ("1" "apple" "fruit")
   callback: ("2" "banana" "fruit")
   id 1: (1 "apple" "fruit")
   cols: carrot is a vegetable

.. include:: ./commit.rst
