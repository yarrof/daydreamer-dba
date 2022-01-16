# Daydreamer DBA

## Story

One of your friends wants to get into data engineering but she knows next to nothing about the topic and lacks the foundations, so in short: she doesn't speak *SQL* at all.

*How should I get started?* she asked you to which you replied immediately ... *hold my beer, I got this*.

Now here you are, sitting in front of your laptop for several hours straight, trying to create a virtual environment with all the bells and whitles to use for her SQL studies.

You've already failed twice, but you feel this third attempt will bring you luck.

If all fails just direct her to *w3schools.com* and finish that beer ...

## What are you going to learn?

- How to install PostgreSQL server
- Configure the PostgreSQL server to allow remote connections
- What roles are in PostgreSQL and how to create user and group roles
- Grant privileges to users/groups

## Tasks

1. Start and configure a VM with a NAT network adapter.
    - Started a VM loaded and configured with an SSH server (either an existing one or a fresh one using [this image](https://github.com/CodecoolBase/short-admin-vms/releases/latest/download/ubuntu-18.04-base.ova))
    - The VM's Network settings are configured with a single _NAT_ adapter
    - Port 22 is forwarded between the host and the guest in the VM's NAT network adapter's settings
    - The guest VM's OS is reachable via `ssh` using the `ubuntu` user

2. Use the package manager to install the PostgreSQL server.
    - The latest postgres package is installed.
    - The server is active and running (verified with `sudo systemctl is-active postgresql`)

3. Create a new PostgreSQL superuser for your own account to access the server.
    - The user is created using the `createuser` command
    - The username is `ubuntu`
    - The password is `ubuntu`
    - The user is superuser

4. Create a database for your own account with the same as your account.
    - The DB is created using the `createdb` command

5. For security reasons by default the database server is only accessible locally.
In order for you or others to be able to access it from the *outside*, e.g. a host machine in a virtualized environment, the server must be configured to allow remote connections.
    - `listed_addresses` is enabled and modified in `postgresql.conf` to allow remote connections
    - `pg_hba.conf` is modified to allow connections from everyhost from all networks (`0.0.0.0/0`) using `md5` (password-based) authentication
    - After modifying the configuration files the server is restarted
    - Port 5432 on the guest is forwarded to port 15432 on the host (in the VM's network settings)

6. It is always a good idea to use a GUI to remotely access and manage a database.
pgAdmin is one of the best open-source, free choice for this, so make sure you can connect it to the DB.
    - The user added a new server in PgAdmin
    - `localhost` is used for the new server's host (or `127.0.0.1` if `localhost` doesn't work)
    - `15421` is used for the new server's port
    - A connection can be made to the server running on the guest from the host using the `ubuntu` user

7. To see whether everything works correctly run the `init.sql` in the starter repo and run a few basic queries against the dataset.
    - Running `select * from users where email like '%.com'` returns 18 rows of data

8. In order to allow *non*-superusers to connect to the database as well create a group with sufficient privileges and add user accounts/roles to the group as needed. Create 3 `maintainer` user and a `maintainer` user group with pgAdmin
    - A `maintainers` group is created
    - Two users, `maintainer1` and `maintainer2` are created and added/assigned to the `maintainers` group, their passwords should be the same as the username, `maintainer1` and `maintainer2` respectively

9. A user/group/role in itself isn't useful, to make it so you must assign the necessary rights to said roles/users/groups.
A *maintainer* should be granted the same privileges as a superuser so limit this in a meaningful way.
Assign rights to the usergroup to `INSERT`, `SELECT` and `UPDATE`
    - `maintainers` are allowed to connect to the database
    - `maintainers` are not allowed to grant privileges to other users
    - `maintainers` are granted the `SELECT` and `INSERT` privileges on the `ubuntu` database
    - `maintainers` *aren't* granted any other privileges (so they cannot `UPDATE` or `DELETE`, etc.)
    - Individual maintainer accounts aren't granted privileges, only the `maintainers` group

## General requirements

None

## Hints

- To create roles/users/groups use these commands:
  - [`CREATE ROLE`](https://www.postgresql.org/docs/current/sql-createrole.html)
  - [`CREATE USER`](https://www.postgresql.org/docs/current/sql-createuser.html)
  - [`CREATE GROUP`](https://www.postgresql.org/docs/current/sql-creategroup.html)
  - You can use `CREATE ROLE` instead `CREATE USER` and `CREATE GROUP` since the latter two are synonyms of the former
  - If one the commands don't seem to work on your end look at a different version of docs (these links point to the latest version of the PostreSQL docs)
- **When you are reading background materials:**
  - [Installing PostgreSQL](project/curriculum/materials/pages/tools/installing-postgresql.md). You can skip installing the `postgresql-contrib` package, also you don't need to create the `codecooler` database for this project

## Background materials

- <i class="far fa-exclamation"></i> [About Databases](project/curriculum/materials/competencies/database-basics/about-databases.md.html)
- <i class="far fa-exclamation"></i> [Installing PostgreSQL](project/curriculum/materials/pages/tools/installing-postgresql.md)
- <i class="far fa-exclamation"></i> [Allow Remote Connections](https://blog.bigbinary.com/2016/01/23/configure-postgresql-to-allow-remote-connection.html)
- <i class="far fa-book-open"></i> [pgAdmin Tutorial](https://linuxhint.com/pgadmin4_tutorial_beginners/)
- <i class="far fa-book-open"></i> [pgAdmin Documentation](https://www.pgadmin.org/docs/pgadmin4/development/getting_started.html)
