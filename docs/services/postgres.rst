Postgres
========

.. note::

    This documentation applies to the latest version of hitchpostgres: version 0.6.2

First, install the necessary prerequisites for building postgres.

On Ubuntu/Debian::

    $ sudo apt-get install libpq-dev build-essential libreadline6-dev zlib1g-dev

On Mac OS X::

    $ brew install libtool automake readline

    $ brew link readline

On Arch::

    $ sudo pacman -S m4 base-devel automake readline zlib postgresql-libs

On Red Hat/CentOS/Fedora::

    $ sudo yum install make automake gcc eadline-devel zlib-devel llvm postgresql-libs postgresql-devel

Next, install the the plugin in your tests directory::

    $ hitch install hitchpostgres

In your test, define the postgres installation you will use, e.g. a system postgres:

.. code-block:: python

    import hitchpostgres

    postgres_package = hitchpostgres.PostgresPackage(version="9.3.9")
    postgres_package.build()
    postgres_package.verify()

To use, define the service after initializing the ServiceBundle object but before starting it.

.. code-block:: python

    # Service definition in engine's setUp:
    postgres_user = hitchpostgres.PostgresUser("newpguser", "pguserpassword")

    self.services['Postgres'] = hitchpostgres.PostgresService(
        postgres_package=postgres_package,                                          # Mandatory
        port=15432,                                                                 # Optional (default: 15432)
        users=[postgres_user, ],                                                    # Optional (default: no users)
        databases=[hitchpostgres.PostgresDatabase("databasename", newpguser), ]     # Optional (default: no databases)
        pgdata=None,                                                                # Optional location for pgdata dir (default: put in .hitch)
    )


Once it is running, you can interact with the service::

    In [1]: self.services['Postgres'].databases[0].psql("-c", "SELECT * FROM yourtable;").run()
    [ Prints output ]

    In [2]: self.services['Postgres'].databases[0].psql().run()
    [ Launches into postgres shell ]


