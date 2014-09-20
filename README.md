# myctl - MySQL command line tool

MySQL command line tool for managing users, databases, database
privileges and displaying useful information from MySQL database.


### Installation:
TODO

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
