# myctl - MySQL command line tool

MySQL command line tool for managing users, databases and privileges in
one MySQL database instance. The tool is intended to ease administration
from command line.

Currently the tool supports:
 * Database management
  * add databases, optionally restore contents from dump file
  * dump database contents to a file, gzipped or raw
  * remove database
  * restore database contents from a dump, raw or compressed (gzip, zip, bzip2)
 * Shell
  * get MySQL shell
  * execute an SQL statement, optionally for given database
 * User management
  * add users, optionally with database
  * remove users, including removal of databases user has access to
  * list users, including dbs user has access to
 * Privilege management
  * grant all privileges of a user to a database or all databases
  * revoke all privileges of a user to a database or all databases

### Important notes ###

#### Privilege management ####
The tool for simplicity sets only all/none privileges (excluding grant option).
So it does not provide fine grained privilege management. This is currently
not planned.

This also means that the tool will detect that the user has granted access
to a particular database, if it has at least one permission to it. Moreover the
`-- all --` in `user list` is detected by the select privilege only for the given
user.


### Installation

To install the tool run the `install` command. This will ask you for some
details and install the tool to the system. The tool can be installed
in two ways - system-wide or for current user.

```
$ myctl install
```

To uninstall the tool from the system run the `uninstall` command.

#### System-wide installation

This will install the tool for all users and setup a system-wide configuration.
A new group will be created and only users in this group will be able to use the tool.
The configuration file containing possibly sensitive information will be only
readable by group members. Root privileges are required for this type of installation.
  
#### User installation

This will install the tool only for current user. The tool will be installed in
`$HOME/bin/` directory. The directory will be created if it does not exist and you
may want to manually configure your PATH for this directory. The configuration
file will be created in the users home directory.


### Configuration file

The configuration file provides basic configuration for myctl tool.
Global configuration file is located at `/etc/myctl.conf`. This can be
overridden by local configuration file `~/.myctl.conf`. Configuration files
are consisted from bash variables. The configuration file will be created
by the `install` command, but can be also created manually. To verify
the correct installation run the `install` command, which should in this
case inform you that the tool is correctly installed.

```
$ myctl install
:: Tool is already installed and configured correctly. Run 'uninstall' command to remove.
```

#### Mandatory variables:
```
 MYSQL_USER      - MySQL administrator username
 MYSQL_PASSWD    - MySQL administrator password
```

#### Optional variables:
```
 MYSQL_HOST      - MySQL hostname (default: localhost)
 MYSQL_PORT      - MySQL port (default: 3309)
 DEF_HOST        - Default host/server for new user (default: localhost)
 DEF_PASSWD      - Default password for added user, myctl will ask
                   for password if not set
```


### Invocation
The tool is invoked by specifying a management space with an command
depending on the specific space. Currently these for management spaces
are supported: user, db, privilege and shell. The latter accepts no
commands and is intended to get you and admin mysql console or run
a SQL query.

Run the tool with no parameter to get list of support management spaces.
Refer to each management space help (via the -h option) to get list
of supported commands and options.

For example to add 'testuser' user you would run the add command
of the user management:
```
$ myctl user add testuser
```

The tool supports abbreviations of the commands. If the passed string matches
one management space or command, it will work also. So for the above command
you could simple write:
```
$ myctl us a testuser
```
Here us is the minimal string that matches user, due to existing uninstall
command:
```
$ myctl u a testuser
Error: More commands match "u": uninstall user 
```

By default the tool asks for confirmation of dangerous operations - removal
of any data. Pass the '-y' option just after the myctl command to override
this behavior. So for example to remove testuser without confirmation:
```
$ myctl -y user del testuser
:: User 'testuser@localhost' removed
```

### Examples

#### Listing database users
The `user list` command displays all database users with users
who have some privileges to it.
```
$ myctl user list
```

The users that have no permissions to any database have '-- none --' in the
privileges column. Users that have permissions to all databases have '-- all --'
in the privileges column.

If you supply another parameter after the list command, this will be used
for egrep on the whole user list.

So for example to list all users starting with wp_ you would use
```
$ myctl user list '^wp_.*@'
```

#### Adding user
Adding a user dbuser with default user password 'defaultpass' (set in DEF_PASSWD).
If DEF_PASSWD would not be set the tool would ask for user password.
```
$ myctl user add dbuser
:: Created user 'dbuser@localhost' with password 'defaultpass'
```

To add a user that is able to connect from a different host (10.0.0.1 in this
example) simply pass the 'host' after username separated with '@' sign
```
$ ./myctl user add dbuser@10.0.0.1
:: Created user 'dbuser@10.0.0.1' with password 'defaultpass'
```

Adding user dbuser with password 'secretSauce##'.
```
$ myctl user add -p "secretSauce##" dbuser
:: Created user 'dbuser@localhost' with password 'secretSauce##'
```

Adding a user dbuser with default password and add create a database
sampledb with all privileges for the created user.
```
$ myctl user add -d sampledb dbuser
:: Created user 'dbuser@localhost' with password 'defaultpass'
:: Created database 'sampledb'
:: Granted all privileges for 'dbuser@localhost' to 'sampledb'
```

More databases can be specified separated with commas.
```
$ myctl user add -d sampledb,sampledb2,sampledb3 testuser
:: Created user 'testuser@localhost' with password 'defaultpass'
:: Created database 'sampledb'
:: Granted all privileges for 'testuser@localhost' to 'sampledb'
:: Created database 'sampledb2'
:: Granted all privileges for 'testuser@localhost' to 'sampledb2'
:: Created database 'sampledb3'
:: Granted all privileges for 'testuser@localhost' to 'sampledb3'
```

To add user dbuser with default password, create a database
sampledb with all privileges for the created user and restore dump
from file 'sqldump' to the newly created database.
```
$ myctl user add -d sampledb -f sqldump dbuser
:: Created user 'dbuser@localhost' with password 'defaultpass'
:: Created database 'sampledb'
:: Granted all privileges for 'dbuser@localhost' to 'sampledb'
:: Restored MySQL dump '/home/user/sqldump' to database 'sampledb'
```

#### Removing user
To remove a user use the `del` command from the `user` management.
```
$ myctl user del dbuser
Really remove MySQL user 'dbuser@localhost' (y/n)? y
:: User 'dbuser@localhost' removed
```

To remove specific databases user has access to use the `-d` option.
```
$ myctl user del -d sampledb dbuser
Really remove MySQL user 'dbuser@localhost' and databases 'sampledb' (y/n)? y
:: Removed database 'sampledb'
:: User 'dbuser@localhost' removed
```

Sometimes is very useful to remove the user and all databases he has access to.
For this the tool supports the '-D' option.
```
$ myctl user del -D testuser
Really remove MySQL user 'testuser@localhost' and all databases he has access to (y/n)? y
:: Removed database 'sampledb'
:: Removed database 'sampledb2'
:: Removed database 'sampledb3'
:: User 'testuser@localhost' removed
```

#### Adding database
To add a new database use the `add` command from the `db` management space.
```
$ myctl db add sampledb
:: Created database 'sampledb'
```

You can grant access to the created database to one or more existing users
via the '-u' option. Users can be separated via commas. If the uses has no
host/server information the default host (specified via the variable DEF_HOST)
is used.
```
$ myctl db add -u dbuser@10.0.0.1,dbuser2 sampledb
:: Created database 'sampledb'
:: Granted all privileges for 'dbuser@10.0.0.1' to 'sampledb'
:: Granted all privileges for 'dbuser2' to 'sampledb'
```

#### Deleting database
To remove a database use the `del` command from the `db` management space.
```
$ myctl db del sampledb
Really remove MySQL database sampledb (y/n)? y
:: Database sampledb removed
```

#### Dumping database
To dump database use the `dump` command from the `db` management space.
By default the dump is saved to the current folder and the filename
is generated from the database name and current date. The dump is by
default gzipped.
```
$ myctl db dump sampledb
:: Dumped database 'sampledb' to 'sampledb-20141012_21-38.sql.gz'
```

To dump to a specific file pass the filename after the database name
```
$ myctl db dump sampledb mydump.gz
:: Dumped database 'sampledb' to 'mydump.gz'
```

You can disable the compression with the `-n` option, the dump will be
saved as raw SQL.


#### Restoring database
To restore database dump to an existing database use the `restore`
command from the `db` management space. The compression format is
auto-detected with the file(1) command.

```
$ myctl db restore sampledb sqldump 
:: Restored MySQL dump 'sqldump' to database 'sampledb'
```

You can force dropping the database contents using the `r` option.
```
$ myctl db restore -r sampledb sqldump 
:: Removed database 'sampledb' contents
:: Restored MySQL dump 'sqldump' to database 'sampledb'
```

#### Listing databases
The `db list` command displays all databases with users who have some
privileges to it.
```
$ myctl db list
```

The databases that have no users are marked with `-- none --` in the
users column.

If you supply another parameter after the list command, this will be used
for egrep on the whole user list.

So for example to list all users databases that have sample word use
```
$ myctl db list sample
Database                                Users with privileges
--------------------------------------------------------------------------------
sampledb                                dbuser2@localhost dbuser@10.0.0.1 
```

#### Granting privilege
To grant all privileges for an existing user to an existing database use the
`grant` command from the `privilege` management space.
```
$ myctl grant dbuser@localhost sampledb
:: Granted user 'dbuser@localhost' all privileges to database 'sampledb'
```

To grant privilege to all databases simply run the `grant` command with no
database. This operation will require confirmation.
```
$ myctl privilege grant dbuser@localhost
Do you want to add privileges to all databases for user dbuser@localhost (y/n)? y
:: Granted user 'dbuser@localhost' all privileges to all databases
```

#### Revoking privilege
To revoke all privileges for an existing user to an existing database use the
`revoke` command from the `privilege` management space.
```
$ myctl priv revoke dbuser sampledb
:: Revoked all privileges from user 'dbuser@localhost' to database 'sampledb'
```

To revoke all privileges from a user skip the database specification.
```
$ myctl priv revoke dbuser@10.0.0.1
Do you want to revoke privileges to all databases from user dbuser@10.0.0.1 (y/n)? y
:: Revoked all privileges from user 'dbuser@10.0.0.1' to all databases
```

#### Get MySQL shell or run SQL query
With the `shell` command you can get an admin MySQL shell.

```
$ myctl shell
```

You can also execute any SQL statement you like, optionally in a context
of a given database using the `-d` option.
```
# run a SELECT query on database sampledb
$ myctl shell -d sampledb 'SELECT * from sampletable'
```
