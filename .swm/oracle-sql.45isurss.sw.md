---
title: Oracle SQL
---

**Oracle SQL** is a variant of **SQL(Structured Query Language)** used specifically within Oracle Database systems. It allows users to interact with databases to store, manipulate, and retrieve data efficiently. Oracle SQL extends standard SQL with additional features tailored to Oracle Database environments

### What is a Database?

A database is an organized collection of data stored and accessed electronically. Databases help in storing and managing data efficiently. They support the storage, manipulation, and retrieval of data in a structured manner. Databases are critical for various applications, ranging from small-scale personal projects to large-scale enterprise systems.

In the context of Oracle, a database consists of physical files on disk and logical structures within the database itself. These logical structures include tablespaces, segments, extents, blocks, and data files, among others.

Run the SQL command to create the database:

```sql
CREATE DATABASE mydb
   USER SYS IDENTIFIED BY sys_password
   USER SYSTEM IDENTIFIED BY system_password
   LOGFILE GROUP 1 ('/u01/app/oracle/oradata/mydb/redo01.log') SIZE 50M,
           GROUP 2 ('/u01/app/oracle/oradata/mydb/redo02.log') SIZE 50M,
           GROUP 3 ('/u01/app/oracle/oradata/mydb/redo03.log') SIZE 50M
   MAXLOGFILES 5
   MAXLOGMEMBERS 5
   MAXLOGHISTORY 1
   MAXDATAFILES 100
   CHARACTER SET AL32UTF8
   NATIONAL CHARACTER SET AL16UTF16
   DATAFILE '/u01/app/oracle/oradata/mydb/system01.dbf' SIZE 500M REUSE
   SYSAUX DATAFILE '/u01/app/oracle/oradata/mydb/sysaux01.dbf' SIZE 100M REUSE
   DEFAULT TABLESPACE users
      DATAFILE '/u01/app/oracle/oradata/mydb/users01.dbf'
      SIZE 100M REUSE AUTOEXTEND ON MAXSIZE UNLIMITED
   DEFAULT TEMPORARY TABLESPACE temp
      TEMPFILE '/u01/app/oracle/oradata/mydb/temp01.dbf'
      SIZE 50M REUSE
   UNDO TABLESPACE undotbs
      DATAFILE '/u01/app/oracle/oradata/mydb/undotbs01.dbf'
      SIZE 200M REUSE AUTOEXTEND ON MAXSIZE UNLIMITED;

```

#### Datatypes

Datatype is a classification that specifies the type of data that can be stored in a column of a table or a variable. Each datatype has a specific storage format, range of valid values, and constraints on the type of operations that can be performed on it

&nbsp;

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZGV2LWRvY3MtY29sbGVjdGlvbiUzQSUzQWFycGl0cGFyZWto" repo-name="dev-docs-collection"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
