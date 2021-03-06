.. This Source Code Form is subject to the terms of the Mozilla Public
.. License, v. 2.0.  If a copy of the MPL was not distributed with this
.. file, You can obtain one at http://mozilla.org/MPL/2.0/.
..
.. Copyright 1997 - July 2008 CWI, August 2008 - 2016 MonetDB B.V.

.. This document is written in reStructuredText (see
   http://docutils.sourceforge.net/ for more information).
   Use ``rst2html.py`` to convert this file to HTML.

==========================================
The MonetDB MAPI and SQL client python API
==========================================


Introduction
============

pymonetdb is a native python client API for monetDB. This API is cross-platform,
and doesn't depend on any monetdb libraries.  It has support for
python 2.5+, including Python 3 and PyPy and is Python DBAPI 2.0 compatible.

This is a fork of the original python-monetdb API. python-monetdb is bundled
with the MonetDB software suite, but the release cycle is too long to fix
small issues. Also I prefer working in github which also enables the use
of travis-ci.

.. image:: https://travis-ci.org/gijzelaerr/pymonetdb.svg?branch=master
    :target: https://travis-ci.org/gijzelaerr/pymonetdb

Installation
============

To install the MonetDB python API run the following command from the
python source directory::

 # python setup.py install

That's all, now you are ready to start using the API.


Documentation
=============

The python code is well documented, so if you need to find
documentation you should have a look at the source code.  Below is an
interactive example on how to use the monetdb SQL API which should get
you started quite fast.


Running the test suite
======================

Install tox and run tox in the root.

You can control the testing behavior using environment variables::

 * MAPIPORT - what port is MonetDB running? _50000_ by default
 * TSTHOSTNAME -  where is MonetDB running? _localhost_ by default
 * TSTPASSPHRASE - what passphrase to test control command? _testdb_ by default
 * TSTDB -  what database to use for testing? _demo_ by default
 * TSTUSERNAME - username, _monetdb_ by default
 * TSTPASSWORD - password, _monetdb_ by default

Note that you first need to create and start a monetdb database. If you
want to run the control tests you need to set a passphrase and enable remote
control::

 $ monetdb create demo
 $ monetdb release demo
 $ monetdbd set control=yes <path to dbfarm>
 $ monetdbd set passphrase=testdb <path to dbfarm>


Examples
========

examples usage below::

 > # import the SQL module
 > import pymonetdb
 > 
 > # set up a connection. arguments below are the defaults
 > connection = pymonetdb.connect(username="monetdb", password="monetdb",
 >                                    hostname="localhost", database="demo")
 > 
 > # create a cursor
 > cursor = connection.cursor()
 > 
 > # increase the rows fetched to increase performance (optional)
 > cursor.arraysize = 100
 >
 > # execute a query (return the number of rows to fetch)
 > cursor.execute('SELECT * FROM tables')
 26
 >
 > # fetch only one row
 > cursor.fetchone()
 [1062, 'schemas', 1061, None, 0, True, 0, 0]
 >
 > # fetch the remaining rows
 > cursor.fetchall()
 [[1067, 'types', 1061, None, 0, True, 0, 0],
  [1076, 'functions', 1061, None, 0, True, 0, 0],
  [1085, 'args', 1061, None, 0, True, 0, 0],
  [1093, 'sequences', 1061, None, 0, True, 0, 0],
  [1103, 'dependencies', 1061, None, 0, True, 0, 0],
  [1107, 'connections', 1061, None, 0, True, 0, 0],
  [1116, '_tables', 1061, None, 0, True, 0, 0],
  ...
  [4141, 'user_role', 1061, None, 0, True, 0, 0],
  [4144, 'auths', 1061, None, 0, True, 0, 0],
  [4148, 'privileges', 1061, None, 0, True, 0, 0]]
 >
 > # Show the table meta data
 > cursor.description 
 [('id', 'int', 4, 4, None, None, None),
  ('name', 'varchar', 12, 12, None, None, None),
  ('schema_id', 'int', 4, 4, None, None, None),
  ('query', 'varchar', 168, 168, None, None, None),
  ('type', 'smallint', 1, 1, None, None, None),
  ('system', 'boolean', 5, 5, None, None, None),
  ('commit_action', 'smallint', 1, 1, None, None, None),
  ('temporary', 'tinyint', 1, 1, None, None, None)]

 
If you would like to communicate with the database at a lower level
you can use the MAPI library::

 > from pymonetdb import mapi
 > server = mapi.Server()
 > server.connect(hostname="localhost", port=50000, username="monetdb",
                  password="monetdb", database="demo", language="sql")
 > server.cmd("sSELECT * FROM tables;")
 ...


