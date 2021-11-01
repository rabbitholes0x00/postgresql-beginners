# README!

## 0x00 Install PostgreSQL

`Step 1` Install PostgreSQL on Linux
```zsh
sudo apt update; sudo apt upgrade -y;
sudo apt install postgresql postgresql-contrib
sudo -u postgres psql -c "SELECT version();"
```

`Step 2` Setting and  Check PostgreSQL
```zsh
sudo service postgresql start
sudo systemctl enable postgresql
sudo service postgresql restart
sudo service postgresql status
```

`Step 3` Activate Remote Access
```zsh
sudo nano /etc/postgresql/13/main/postgresql.conf
```
Put this on EOL `postgresql.conf`
```txt 
this listen_addresses = '*' # what IP address(es) to listen on;
```
Restart PostgreSQL
```zsh
sudo service postgresql restart
```

`Step 4` Put This on EOL `pg_hba.conf`
```txt
# All IPs
host    all             geekslifz              all                 trust
```
```zsh
sudo nano /etc/postgresql/13/main/pg_hba.conf
```

## 0x01 How to Use `createuser` & `createdb`

`Step 1` Login PostgreSQL Superuser and Connect `psql` Client
```zsh
sudo -i -u postgres psql
```

`Step 2` Create a New User {testuser}, Allow User to __Login__, but {NOT Creating Databases}
```zsh
createuser --login --pwprompt testuser
```
\* Enter password for new role: `qwerty`

`Step 3` Create a New DB {testdb}, Owned by {testuser}.
```zsh
sudo -i -u postgres createdb --owner=testuser testdb
```

`Step 4` Tailor the PostgreSQL configuration file /etc/postgresql/X/main/pg_hba.conf to allow non-default user testuser to login to postgresql server, by adding the following entry:
```zsh
cd /etc/postgresql/13/main; sudo nano pg_hba.conf
```
```txt
# TYPE  DATABASE    USER        ADDRESS          METHOD
local   testdb      testuser                     md5
```

`Step 5` Restart PostgreSQL Server:
```zsh
sudo service postgresql restart
```

## Manage User & DB Privileges

`Step 1` Login PostgreSQL Superuser and Connect `psql` Client
```zsh
sudo -i -u postgres psql
```

`Step 2` Grant ALL PRIVILEGES

__Templates__:
```txt
grant all privileges on database {dbname} to {username};
```

__Example__:
```zsh
grant all privileges on database ls_burger to geekslifz;
grant all privileges on database testdb to testuser;
```
## How to Connect `DB` Following `User`

`Step 1` Login PostgreSQL Superuser
```zsh
sudo -i -u postgres psql
```

`Step 2` Connect Some `DB` with following `User` to Connect `psql` Client

__Templates__:
```txt
psql -h {localhost} -d {dbname} -U geekslifz -p {port}
```

__Example__: 
```zsh
psql -h localhost -d ls_burger -U geekslifz -p 5433
psql -h localhost -d testdb -U testuser -p 5433
```

## 0x02 PostgreSQL User Management

`Step 1` Login PostgreSQL Superuser and Connect `psql` Client
```zsh
sudo -i -u postgres psql
```

## `Privileges`: Create New User

__Example__:
```txt
CREATE USER <username> [[ WITH] option [ ... ]]
```

__Commands__:
```txt
SUPERUSER | NOSUPERUSER | CREATEDB | NOCREATEDB | CRATERULE | NOCERATERULE | CREATEUSER | NOCERATEUSER | INHERIT | NOINHERIT | LOGIN | NOLOGIN | CONNECTION LIMIT {connlimit} [ENCRYPTED | UNENCRYPTED] PASSWORD '{password}' VALID UNTIL '{timestamp}' IN ROLE {rolename} [, ...] IN GROUP {rolename} [, ...] ADMIN rolename [, ...] USER [, ...] SYSID {uid}
```

## `Privileges`: Edit User

__Example__:
```txt
ALTER USER {username} [[WITH] option [ ... ]]
SUPERUSER | NOSUPERUSER | CREATEDB | NOCREATEDB | CRATERULE | NOCERATERULE | CREATEUSER | NOCERATEUSER | INHERIT | NOINHERIT | LOGIN | NOLOGIN | CONNECTION LIMIT {connlimit} [ENCRYPTED | UNENCRYPTED] PASSWORD '{password}' VALID UNTIL '{timestamp}'
```

## Create `User` with `SUPERUSER`, This `User` Can `Create DB`
```txt
CREATE USER {username} WITH PASSWORD '{password}' SUPERUSER CREATEDB; 
```

__Example__:
```zsh
CREATE USER geekslifz WITH PASSWORD 'qwerty' SUPERUSER CREATEDB;
```

## Create Normal User with `SUPERUSER`, This User Can `Create {DB, User}`
```txt
CREATE USER {username} WITH PASSWORD '{password}' SUPERUSER CREATEDB CREATEROLE;
```

__Example__:
```zsh
CREATE USER geekslifz WITH PASSWORD 'qwerty' SUPERUSER CREATEDB CREATEROLE;
```

## Change Username
```txt
ALTER USER {username} RENAME TO {new-username}
```

## Change Password
```txt
ALTER USER {username} WITH PASSWORD '{password}';
```

## Delete User
```txt
DROP USER {username} // delete user following username
```

## Decrease User `Privilege` Access
```txt
ALTER USER {username} NOCREATEDB;
```

## 0x03 PostgreSQL Configuration

`Automatic` & `Disable` Running PostgreSQL with `Systemctl`
```zsh
sudo systemctl enable postgresql
sudo systemctl disable postgresql
```

`Service` Management Commands
```zsh
$ sudo service postgresql start
$ sudo service postgresql stop
$ sudo service postgresql restart
$ sudo service postgresql reload
```

`Systemctl` Management Commands
```zsh
sudo systemctl start postgresql
sudo systemctl stop postgresql
sudo systemctl restart postgresql
sudo systemctl reload postgresql
sudo systemctl status postgresql
```

## 0x04 `psql` Explained

## `psql` meta-commands
* `\conninfo` take arguments other than 'dbname', such as 'username', 'host', 'port', etc.
* `\q` quit
* `\list` List available databases
* `\dt` List available tables
* `\d` Describe a table
* `\dn` List available schema

--h — --host=HOSTNAME | database server host or socket directory (default: “local socket”)
--p — --port=PORT | database server port (default: “5432”)
--U — --username=USERNAME | database username (default: “your_username”)
--w — --no-password | never prompt for password
--W — --password | force password prompt (should happen automatically)

## `psql` Parameters
* __Server__ `localhost`
This is the address for the server. You can use an IP address or the hostname of the machine on which database server is running. If you do not give any value, by default it is localhost.

* __Database__ `postgres`:
The name of the database with which you want to connect. The default name of the database is the same as that of the user. (If you are using Advanced Server, the default name would be edb.)

* __Port__ `5432`:
This is the port on which you have configured your instance while installing or initializing. The default port is 5432. (If you are using Advanced Server this would be 5444.)

__Username__ `postgres`:
This is the username that is created while the installation takes place. The default username for postgres is postgres. (If you are using Advanced Server it is enterprisedb.)

## `psql` Auth
* __Trust__, Dengan metode ini, peran dapat terhubung tanpa kata sandi, selama kriteria yang ditentukan dalam pg_hba.conf terpenuhi.
* __Password__, Peran dapat terhubung dengan memberikan kata sandi. Kata sandi dapat disimpan sebagai scram-sha-256md5 dan password (clear-text)
* __Ident__, Metode ini hanya didukung pada koneksi TCP/IP. Bekerja dengan mendapatkan nama pengguna sistem operasi klien, dengan pemetaan nama pengguna opsional.
* __Peer__, Sama seperti Ident tetapi hanya didukung pada koneksi lokal.

## PostgreSQL References:

`YouTube`

* [PostgreSQL for Beginners - Railsware Product Academy](https://www.youtube.com/watch?v=xV9xjzlthHk&list=PLlRtt3cDxl4VHPMJrdzldbf41GRR619gj)

**`Website`**:
* https://www.linuxid.net/25636/cara-install-dan-konfigurasi-postgresql-di-ubuntu-18-04
* https://www.postgresqltutorial.com/psql-commands/
* https://www3.ntu.edu.sg/home/ehchua/programming/sql/PostgreSQL_GetStarted.html
* https://www.linuxid.net/25636/cara-install-dan-konfigurasi-postgresql-di-ubuntu-18-04
* https://www.digitalocean.com/community/tutorials/how-to-manage-sql-database-cheat-sheet
* https://code.tutsplus.com/articles/sql-for-beginners-part-3-database-relationships--net-8561
* https://www.sukabumikode.com/v/58/membuat-mengubah-dan-menghapus-user-postgresql-di-terminal-ubunt
* https://stackoverflow.com/questions/1152260/postgres-sql-to-list-table-foreign-keys
