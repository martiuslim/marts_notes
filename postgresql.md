# PostgreSQL

## Common Commands [Link to PostgreSQL Documentation](https://www.postgresql.org/docs/9.6/static/index.html)
- Start PostgreSQL `sudo systemctl start postgresql`
- Restart PostgreSQL `sudo systemctl restart postgresql`
- Logging into Postgres Super User `sudo su - postgres`
- Accessing client authentication configuration file `vim ~/data/pg_hba.conf`
- Create a user `createuser <user> `
- Create a database `createdb <db>`
- List all database user accounts `\du`
- List all databases `\l`
- List all tables in current database `\dt`

## Installing PostgreSQL 
- [Installation on Fedora](https://developer.fedoraproject.org/tech/database/postgresql/about.html)  
- Another tutorial can be found at the [PostgreSQL Wiki](https://wiki.postgresql.org/wiki/First_steps)
### Quick Start
```
$ sudo dnf install postgresql postgresql-server # install client/server
$ sudo postgresql-setup initdb                  # initialize PG cluster
$ sudo systemctl start postgresql               # start cluster
$ sudo su - postgres                            # login as DB admin
$ createdb quick-start                          # create testing database
$ psql -d quick-start                           # connect to the new database
psql (9.3.9)
Type "help" for help.

quick-start=# create table test (id int);       -- create testing table
CREATE TABLE
quick-start=# insert into test values (1);      -- insert some data
INSERT 0 1
quick-start=# select * from test;               -- query data
 id
----
  1
(1 row)

quick-start=#
```
### Controlling access to PG cluster (pg_hba.conf, users, passwords)
```
$ sudo su - postgres
$ createuser testuser -P
Enter password for new role:
Enter it again:
$ createdb testdb --owner testuser
$ # add 'local all testuser md5' line **before** 'local all all peer' line
$ # (typical mistake is "just" appending the line at the and of the file)
$ vim ~/data/pg_hba.conf
$ cat ~/data/pg_hba.conf | grep ^local
local   all             testuser                                md5
local   all             all                                     peer
$ ^D
$ sudo systemctl restart postgresql
$ psql -d testdb -U testuser
Password for user testuser:
psql (9.3.9)
Type "help" for help.

testdb=>
```

## Password Authentication Issues 
Possibly because of configuration settings in `pg_hba.conf` such as `local   all             postgres                                md5` without setting a password. Since there is no password set, the authentication will always fail since Postgres is expecting a md5 password for authentication. 

Fixes:
- Edit `pg_hba.conf`, usually found at `~/data/pg_hba.conf`. 
- Change `md5` to `trust` or `peer`. Take note of security issues if using `trust`.

### More Links:
- [Password authentication issue ](http://stackoverflow.com/questions/21483540/postgres-password-authentication-issue)
- [Rails Peer authentication failed](http://stackoverflow.com/questions/15306770/pg-peer-authentication-failed)
- [Peer authentication failed](http://stackoverflow.com/questions/15306770/pg-peer-authentication-failed)