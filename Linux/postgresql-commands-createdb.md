### Creating a Postgres database:
  - CREATE DATABASE db_name;
  - CREATE USER user WITH PASSWORD 'password';
  - GRANT ALL PRIVILEGES ON DATABASE db_name TO user;
  - ALTER USER user CREATEDB;
  - ALTER ROLE user SUPERUSER :: if you want a superuser role
 #### ADD POSTGIS extension to db:
  - \c db_name <- switch to database
  - CREATE EXTENSION postgis;
 
  some postgres commands:
    - /c db_name :: switch postgres database
    - /dt :: show the table columns
    - /l :: list all the databases
    - /du :: list all the database users
    - DROP DATABASE :: will the selected database
    - DROP USER :: will drop the selected database user