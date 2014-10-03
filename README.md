# myctl - MySQL command line tool

MySQL command line tool for managing users, databases, database
privileges and displaying useful information from MySQL database.

Currently the tool supports:
 * user management
  * add users, optionally with database
  * remove users, including some or all databases user has access to
  * list users, including dbs user has access to
 * database management
  * add databases, optionally restore contents from dump file
  * dump database contents to a file
  * remove database
  * restore database contents from a file
 * shell
  * get MySQL shell
  * execute an SQL statement, optionally for given database

### Installation:

To install the tool run the _install_ command.  The tool can be installed in two ways:

#### System-wide installation

This will install the tool for all users and setup a system-wide configuration.
A new group - myctl - will be created and only users in this group will be able
to use the tool. The configuration file containing possibly sensitive information
will be only readable by group members. Root privileges are required for this
type of installation.
  
#### User installation

This will install the tool only for current user. The tool will be installed in
$HOME/bin/ directory. The directory will be created if it does not exist and it
will be added to the PATH via .bashrc if you are running on bash. The configuration
file will be created in the users home directory.

### Configuration file

The configuration file provides basic configuration for myctl tool.
Global configuration file is located at `/etc/myctl.conf`. This can be
overriden by local configuration file `~/.myctl.conf`. Configuration files
are consisted from bash variables.

#### Mandatory variables:
```
 MYSQL_USER      - MySQL administrator username
 MYSQL_PASSWD    - MySQL administrator password
```

#### Optional variables:
```
 MYSQL_HOST      - MySQL hostname (default: localhost)
 MYSQL_PORT      - MySQL port (default: 3309)
 DEF_PASSWD      - Default password for added user, myctl will ask
                   for password if not set
```

### Usage:

#### Listing database users
The `user list` command displays all database users with users
who have some privileges to it. Privileges are currently not
distinguished.

```
myctl user list
```

If you supply another parameter after the list command, this will
be used for egrep on the user list.

So for example to list all users starting with wp_ you would use

```
myctl user list 'wp_.*@'
```

#### Adding user
To add user dbuser with default user password (set in DEF_PASSWD).

```
myctl user add dbuser
```

To add user dbuser with password 'secretSauce##'.
```
myctl user add -p "secretSauce##" dbuser
```

To add user dbuser with default password and add create a database
sampledb with all privileges for the created user.
```
myctl user add -d sampledb dbuser
```

To add user dbuser with default password and add create a database
sampledb with all privileges for the created user, restore dump
from file sqldump to the created database.
```
myctl user add -d sampledb -f sqldump dbuser
```

#### Get MySQL shell or run SQL query
With the `shell` subcommand you can get an admin MySQL shell.

```
myctl shell
```

You can also execute any SQL statement you like, optionally
in a context of a given database (-d option).
```
# run a SELECT query on database sampledb
myctl shell -d sampledb 'SELECT * from sampletable'
```
