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

#### Examples:
```
# add user dbuser with default user password
myctl user add dbuser

# add user dbuser with password secretSauce##
myctl user add -p "secretSauce##" dbuser

# add user dbuser with default password and database sampledb
myctl user add -d sampledb dbuser

# list users
myctl user list

* run mysql shell
myctl shell
```
