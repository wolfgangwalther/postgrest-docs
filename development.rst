.. _build_source:

Build from Source
=================

.. note::

  We discourage building and using PostgREST on **Alpine Linux** because of a reported GHC memory leak on that platform.

To help with development, you'll need to build from source. `Stack <https://github.com/commercialhaskell/stack>`_ makes it easy. It will install any necessary Haskell dependencies on your system.

* `Install Stack <http://docs.haskellstack.org/en/stable/README.html#how-to-install>`_ for your platform
* Install Library Dependencies

  =====================  =======================================
  Operating System       Dependencies
  =====================  =======================================
  Ubuntu/Debian          libpq-dev, libgmp-dev, zlib1g-dev
  CentOS/Fedora/Red Hat  postgresql-devel, zlib-devel, gmp-devel
  BSD                    postgresql95-client
  OS X                   libpq, gmp
  =====================  =======================================

* Build and install binary

  .. code-block:: bash

    git clone https://github.com/PostgREST/postgrest.git
    cd postgrest

    # adjust local-bin-path to taste
    stack build --install-ghc --copy-bins --local-bin-path /usr/local/bin

.. note::

   - If building fails and your system has less than 1GB of memory, try adding a swap file.
   - `--install-ghc` flag is only needed for the first build and can be omitted in the subsequent builds.

* Check that the server is installed: :code:`postgrest --help`.

Running the Test Suite
======================

To properly run the test suite, you need a PostgreSQL database that the tests can run against. There are several ways to set up this database.

Testing with a temporary database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have PostgreSQL installed locally (:code:`initdb`, :code:`pg_ctl` and :code:`psql` should be on your PATH, no server needs to be running), you can run the test suite against a temporary database:

.. code:: bash

   test/with_tmp_db stack test

The :code:`with_tmp_db` script will set up a new PostgreSQL cluster in a temporary directory, set the required environment variables and run the command that you passed it as an argument, :code:`stack test` in the example above. When the command is done, the temporary database is torn down and deleted again.

Testing with Docker
~~~~~~~~~~~~~~~~~~~

The ability to connect to non-local PostgreSQL simplifies the test setup. One elegant way of testing is to use a disposable PostgreSQL in docker.

For example, if local development is on a mac with Docker for Mac installed:

.. code:: bash

  $ docker run --name db-scripting-test -e POSTGRES_PASSWORD=pwd -p 5434:5432 -d postgres
  $ POSTGREST_TEST_CONNECTION=$(test/create_test_db "postgres://postgres:pwd@localhost:5434" test_db) stack test
