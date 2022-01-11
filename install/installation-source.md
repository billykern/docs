# Install self-hosted TimescaleDB from source
You can host TimescaleDB yourself, on any system, by downloading the source code
and compiling it. These instructions do not require the use of a package manager
or installation tool.

Before you start, make sure you have installed:

*   PostgreSQL 12 or later, with a development environment. For more information
    about PostgreSQL installation, including downloads and instructions, see the
    [PostgreSQL documentation][postgres-download].
*   CMake version 3.11 or later. For more information about CMake installation,
    including downloads and instructions, see the
    [CMake documentation][cmake-download].
*   C language compiler for your operating system, such as `gcc` or `clang`.

If you are installing from source on a Microsoft Windows system, you also need:
*   Visual Studio 2015 or later, packaged with CMake version 3.11 or later, and
    Git components.

<procedure>

### Installing self-hosted TimescaleDB from source
1.  At the command prompt, clone the Timescale GitHub repository:
    ```bash
    git clone https://github.com/timescale/timescaledb.git
    ```
1.  Change into the cloned directory:
    ```bash
    cd timescaledb
    ```
1.  Checkout the latest release. You can find the latest release tag on
    our [Releases page][gh-releases]:
    ```bash
    git checkout 2.5.1
    ```
1.  Bootstrap the build system:
    <terminal>

    <tab label='Linux'>

    ```bash
    ./bootstrap
    ```

    </tab>

    <tab label="Windows">

    ```powershell
    bootstrap.bat
    ```

    </tab>

    </terminal>
1.  Build the extension:
    <terminal>

    <tab label='Linux'>

    ```bash
    cd build && make
    ```

    </tab>

    <tab label="Windows">

    ```powershell
    cmake --build ./build --config Release
    ```

    </tab>

    </terminal>
1.  Install TimescaleDB:
    <terminal>

    <tab label='Linux'>

    ```bash
    make install
    ```

    </tab>

    <tab label="Windows">

    ```powershell
    cmake --build ./build --config Release --target install
    ```

    </tab>

    </terminal>

</procedure>

## Configure PostgreSQL after installing from source
When you install TimescaleDB from source, you need to do some additional
PostgreSQL configuration to add the TimescaleDB library.

<highlight type="important">
If you have more than one version of PostgreSQL installed, TimescaleDB can only
be associated with one of them. The TimescaleDB build scripts use `pg_config` to
find out where PostgreSQL stores its extension files, so you can use `pg_config`
to find out which PostgreSQL installation TimescaleDB is using.
</highlight>

<procedure>

### Configuring PostgreSQL after installing from source
1.  Locate the `postgresql.conf` configuration file:
    ```bash
    psql -d postgres -c "SHOW config_file;"
    ```
1.  Open the `postgresql.conf` file in your preferred text editor, and locate
    the `shared_preload_libraries` parameter. Uncomment the line, and
    add `timescaledb`:
    ```bash
    shared_preload_libraries = 'timescaledb'
    ```
    If you use other preloaded libraries, make sure they are comma separated.
1.  Restart the PostgreSQL instance:
    <terminal>

    <tab label='Linux'>

    ```bash
    service postgresql restart  
    ```

    </tab>

    <tab label="Windows">

    ```powershell
    pg_ctl restart
    ```

    </tab>

    </terminal>

</procedure>

When you have completed the installation, you need to configure your database so
that you can use it. The easiest way to do this is to run the `timescaledb-tune`
script, which is included with the `timescaledb-tools` package. For more
information, see the [configuration][config] section.

## Set up the TimeascaleDB extension
When you have PostgreSQL and TimescaleDB installed, you can connect to it from
your local system using the `psql` command-line utility. This is the same tool
you might have used to connect to PostgreSQL before, but if you haven't
installed it yet, check out our [installing psql][install-psql] section.

<procedure>

### Setting up the TimescaleDB extension
1.  On your local system, at the command prompt, connect to the PostgreSQL
    instance as the `postgres` superuser:
    ```bash
    psql -U postgres -h localhost
    ```
    If your connection is successful, you'll see a message like this, followed
    by the `psql` prompt:
    ```
    psql (13.3, server 12.8 (Ubuntu 12.8-1.pgdg21.04+1))
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.
    tsdb=>
    ```
1.  At the `psql` prompt, create an empty database. Our database is
    called `example`:
    ```sql
    CREATE database example;
    ```
1.  Connect to the database you created:
    ```sql
    \c example
    ```
1.  Add the TimescaleDB extension:
    ```sql
    CREATE EXTENSION IF NOT EXISTS timescaledb;
    ```
1.  You can now connect to your database using this command:
    ```bash
    psql -U postgres -h localhost -d example
    ```

</procedure>

You can check that the TimescaleDB extension is installed by using the `\dx`
command at the `psql` prompt. It looks like this:
```sql
tsdb=> \dx
List of installed extensions
-[ RECORD 1 ]------------------------------------------------------------------
Name        | pg_stat_statements
Version     | 1.7
Schema      | public
Description | track execution statistics of all SQL statements executed
-[ RECORD 2 ]------------------------------------------------------------------
Name        | plpgsql
Version     | 1.0
Schema      | pg_catalog
Description | PL/pgSQL procedural language
-[ RECORD 3 ]------------------------------------------------------------------
Name        | timescaledb
Version     | 2.5.1
Schema      | public
Description | Enables scalable inserts and complex queries for time-series data
-[ RECORD 4 ]------------------------------------------------------------------
Name        | timescaledb_toolkit
Version     | 1.3.1
Schema      | public
Description | timescaledb_toolkit

tsdb=>
```

## Where to next
Now that you have your first TimescaleDB database up and running, you can check
out the [TimescaleDB][tsdb-docs] section in our documentation, and find out what
you can do with it.

If you want to work through some tutorials to help you get up and running with
TimescaleDB and time-series data, check out our [tutorials][tutorials] section.

You can always [contact us][contact] if you need help working something out, or
if you want to have a chat.


[contact]: https://www.timescale.com/contact
[install-psql]: /how-to-guides/connecting/psql/
[tsdb-docs]: timescaledb/:currentVersion:/index/
[tutorials]: /timescaledb/:currentVersion:/tutorials/
[config]: /timescaledb/latest/how-to-guides/configuration/
[postgres-download]: https://www.postgresql.org/download/
[cmake-download]: https://cmake.org/download/
[gh-releases]: https://github.com/timescale/timescaledb/releases