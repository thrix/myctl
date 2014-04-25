# MySQL command line tool

MySQL command line tool for managing users, databases and displaying
various useful information from MySQL database.


### Installation:
TODO

### Configuration file

The configuration file provides basic configuration for myctl tool.
Global configuration file is located at `/etc/myctl.conf`. This can be
overriden by local configuration file `~/.myctl.conf`. Configuration file
contains bash varibles sourced by the tool.

#### Mandatory variables:
```
 MYSQL_USER      - MySQL administrator username
 MYSQL_PASSWORD  - MySQL administrator password
```

#### Optional variables:
```
 MYSQL_HOST      - MySQL hostname (default: localhost)
 MYSQL_PORT      - MySQL port (default: 3309
 DEF_PASSWORD    - Default password for added user
```

### Usage:

#### Listing database users
The `user list` command displays all database users with all their
databases. Actual privileges of users are ignored currently.

```
myctl user list
```

If you supply another parameter after the list command, this will
be used for egrep on the user list.

So for example to list all users starting with wp_ you would use

```
myctl user list 'wp_.*@'
```

#### Commands not working yet
```
# add user dbuser with default user password
myctl user add dbuser

# add user dbuser with password secretSauce##
myctl user add -p "secretSauce##" dbuser

# add user dbuser with default password and database sampledb
myctl user add -d sampledb dbuser

* run mysql shell
myctl shell
```
