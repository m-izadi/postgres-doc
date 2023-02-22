# INSTALLATION

## Docker (Recommended)

sudo docker run --name Postgres-DB -e POSTGRES_PASSWORD=123456 -p 5432:5432 -d postgres:alpine

* -e = Environment Variables
* -d = unknown (deattach)
sudo docker run --name Postgres-DB -e POSTGRES_PASSWORD=123456 -v /home/tito/Tito/DB-PostGres:/var/lib/postgresql/data -p 5432:5432 -d postgres:alpine

sudo docker exec -it -u postgres Postgres-DB psql
    *-u = UserName
    * Postgres-DB = Database Name
    * psql = run this command on container

## Connect local DB

    # psql -U postgres -d postgres -h 127.0.0.1
      * -U = UserName
      * -d = Select DataBase
      * -h = Host
  Run psql with postgres user
    # sudo -u postgres psql

## Connect to Docker

    # sudo docker exec -it -u postgres Postgres-DB psql
      * -u = UserName
      * Postgres-DB = Database Name
      * psql = run this command on container4

## Basic Command

    # \d                    Table Lists
    # \d "TABLENAMES"      show tables detailes

    # \dn - List schemas
    # \dt - List tables inside public schemas
    # \dt schema1. - List tables inside particular schemas. For eg: 'schema1'.
    # \dx                   List of Extentions
    # \du                   List Roles/Users and accesses

    # \l                    Show Databases
    # \q                    quit
    
    # \c -c postgres        Switch to Users
    # \c inc_backup         Switch DataBase
    
    # \conninfo             Connection Information

### NOSUPERUSER is the default

    # CREATE ROLE "role name" WITH PASSWORD '*****' SUPERUSER;

    ## USER SYNTAX
    # createuser --help
    #  createuser -ls titoangel -U postgres -h 127.0.0.1 -W "1234"
        * -ls = login + superuser
        * -U postgres = create new user with this user

## Create & Remove Database

    IN PSQL
    # CREATE DATABASE BerozresanDB WITH OWNER = postgres;
    
    IN UNIX(SELL)
    # createdb test1 -h 127.0.0.1



    IN PSQL
    # DROP DATABASE berozresanDB;
    
    IN UNIX(SELL)
    # dropdb test1 -h 127.0.0.1

## Create database from TEMPLATE

    # CREATE DATABASE new_template WITH TEMPLATE=template0

## Restart Password

    ALTER USER postgres PASSWORD 'newpassword';
