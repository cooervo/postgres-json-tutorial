### Sudo to postgres user
`sudo su postgres`

`psql -d postgres -U postgres`

### Create Read Only and Read Write users

read only:

	CREATE USER app_ro WITH PASSWORD 'myPassword';

read & write:

	CREATE USER app_rw WITH PASSWORD 'myPassword';

### To ALTER users (change password)

	ALTER USER app_rw WITH PASSWORD 'myNewPassword';

### Create a DB:

	CREATE DATABASE myapp;

### Connect to DB:

	\c myapp

### List Roles 

	\du

Output:

	                             List of roles
	 Role name |                   Attributes                   | Member of
	-----------+------------------------------------------------+-----------
	 app_ro    |                                                | {}
	 app_rw    |                                                | {}
	 postgres  | Superuser, Create role, Create DB, Replication | {}


### Grant permissions

Here we are saying that user app_ro has permission to connect to myapp DB:

	GRANT CONNECT ON DATABASE myapp to app_ro;

Now the same for user app_rw:

	GRANT CONNECT ON DATABASE myapp to app_rw;

### List all Databases

	\l
Output:

	                                  List of databases
	   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges
	-----------+----------+----------+-------------+-------------+-----------------------
	 myapp     | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | postgres=CTc/postgres+
	           |          |          |             |             | app_ro=c/postgres    +
	           |          |          |             |             | app_rw=c/postgres
	 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
	 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
	           |          |          |             |             | postgres=CTc/postgres
	 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
	           |          |          |             |             | postgres=CTc/postgres

		   

### Alter DEFAULT privileges to roles app_ro & app_rw so they can write or read

Change roles default privileges for all tables (new or old ones).

For read only:

	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES to app_ro;
	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON SEQUENCES to app_ro;

For read and write:

	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, UPDATE, INSERT, DELETE ON TABLES TO app_rw;
	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, UPDATE ON SEQUENCES to app_rw;

### List all access privileges to tables

\z

	                                     Access privileges
	 Schema |      Name       |   Type   |     Access privileges     | Column access privileges
	--------+-----------------+----------+---------------------------+--------------------------
	 public | test_2          | table    |                           |
	 public | test_2_id_seq   | sequence |                           |
	 public | test_new        | table    | postgres=arwdDxt/postgres+|
	        |                 |          | app_ro=r/postgres        +|
	        |                 |          | app_rw=arwd/postgres      |
	 public | test_new_id_seq | sequence | postgres=rwU/postgres    +|
	        |                 |          | app_ro=r/postgres        +|
	        |                 |          | app_rw=rw/postgres        |

Notice
	
	app_ro=r //means app_ro can only read  
	app_rw=rw //means it can read and write	


### To quit

	\q

### Connect to myapp DB with role app_ro

	psql -h localhost -U app_ro myapp



# Running an example

1. ssh into VPS:
		
		ssh root@my-domain.com
		
2. Create user for postgres:

 `adduser user_x`
 
3. From `user_x@home$` in linux open `nano script.sql`

  If user has been created before:
   `sudo -i -u user_name`

4. Run the script: 

		psql -d database_x -f script.sql

5. Back to UNIX root:
  `exit`

6. At `root@home`:

		sudo API_KEY=api-fizz-buzz  forever start server.js

**Remember to add privileges to user_x**:

Connect privileges:
	
	GRANT CONNECT ON DATABASE database_x to user_x; 

For read only:

	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES to user_ro;
	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON SEQUENCES to user_ro;
	
For read and write:

	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, UPDATE, INSERT, DELETE ON TABLES TO user_rw;
	ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, UPDATE ON SEQUENCES to user_rw;
