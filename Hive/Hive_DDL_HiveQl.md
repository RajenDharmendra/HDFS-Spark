**Concepts of Hive Data Definitions & Manipulations and HiveQL Manipulations**
------------------------------------------------------------------------

Hive Data Definitions

Hive Data Manipulations

HiveQL Manipulations

Hive Data Definitions

Creating a database schema

Dropping a database schema

Altering a database schema

Using a database schema

Showing database schemas

Describing a database schema

Creating tables

Dropping tables

Truncating tables

Renaming tables

Altering table properties

Creating views

Dropping views

Altering the view properties

Altering the view as select

Showing tables

Showing partitions

Show the table properties

Showing create table

HCatalog

WebHCat


1) **Creating a database schema**: How to create a database in Hive
The Create Database statement is used to create a database in Hive. By default, there is a database in Hive named default.

> The general format of creating a database is as follows: CREATE
> (DATABASE|SCHEMA) [IF NOT EXISTS] database_name [COMMENT
> database_comment] [LOCATION hdfs_path] [WITH DBPROPERTIES
> (property_name=property_value, ...)];

Where:
DATABASE|SCHEMA: These are the same thing. These words can be used interchangeably.
[IF NOT EXISTS]: This is an optional clause. If not used, an error is thrown when there is an attempt to create a database that already exists.
[COMMENT]: This is an optional clause. This is used to place a comment for the database. This comment clause can be used to add a description about the database. The comment must be in single quotes.
[LOCATION]: This is an optional clause. This is used to override the default location with the preferred one.
[WITH DBPROPERTIES]: This is an optional clause. This clause is used to set properties for the database. These properties are key-value pairs that can be associated with the database to attach additional information with the database.
Ex: 

    Create database if not exists Hive_learning
    Comment 'This is my first DB'
    Location '/my/directory'
    With dbproperties ('Created by'='User','Created on'='1-Jan-2015');


2) **Dropping a database schema:** Drop a database in Hive
Drop Database statements drop the database and the objects inside that database. When a database is dropped, its directory is also deleted. The general format of dropping a database is as follows:

> DROP (DATABASE|SCHEMA) [IF EXISTS] database_name [RESTRICT|CASCADE];

Where:
DATABASE|SCHEMA: These are the same thing. These words can be used interchangeably.
[IF EXISTS]: This is an optional clause. If not used, an error is thrown when there is an attempt to drop a database that does not exist.
[RESTRICT|CASCADE]: This is an optional clause. RESTRICT is used to restrict the database from getting dropped if there are one or more tables present in the database. RESTRICT is the default behavior of the database. CASCADE is used to drop all the tables present in the database before dropping the database
Ex:
**Drop database if exists Hive_learning restrict;**


3) **Altering a database schema:** Alter a database in Hive
The ALTER DATABASE command in Hive is used to alter dbproperties or set the dbproperties of a database. Using the ALTER DATABASE command, we can only alter dbproperties and nothing else.
The general format for altering a database is as follows:

> ALTER (DATABASE|SCHEMA) database_name SET DBPROPERTIES
> (property_name=property_value,..);

Where:
DATABASE|SCHEMA: These are the same thing. These words can be used interchangeably:
SET DBPROPERTIES (property_name=property_value, ...)
Ex:

    Alter database Hive_learning set dbproperties ('Created by' = 'User1', 'Created on'= '15-Jan-2015');

4) Using a database schema: Use a database in Hive
The USE DATABASE command is used to switch to the database, or it sets the database as the working database. The general format of using a database is as follows:

> USE (DATABASE|SCHEMA) database_name;

Where:
DATABASE|SCHEMA: These are the same thing. These words can be used interchangeably.
Ex:

    Use database Hive_learning;


5) Showing database schemas: Show databases in Hive
The SHOW DATABASE command is used to list all the databases in the Hive metastore. The general format is as follows:

> SHOW (DATABASES|SCHEMAS) [LIKE identifier_with_wildcards];

Where:
DATABASE|SCHEMA: These are the same thing. These words can be used interchangeably.
[LIKE]: Is an optional clause. This clause is used to filter the databases with the help of a regular expression. There can only be two wildcards in the regular expression, which are * for any character(s) or | for a choice.
Ex:

     SHOW database like 'Hive*';



6) Describing a database schema: Describe databases in Hive
The DESCRIBE DATABASE command is used to get information about the database, such as the name of the database, its comments, its location on the filesystem, and its dbproperties. 
The general format of using the command is as follows:

> DESCRIBE DATABASE/SCHEMA [EXTENDED] db_name;

Where:
DATABASE|SCHEMA: These are the same thing. These words can be used interchangeably.
[EXTENDED]: This is an optional clause. This clause will list all the dbproperties attached to a particular database in Hive.
Ex:

    Describe database extended Hive_learning;


7) **Creating tables:** Create tables in Hive
The CREATE TABLE statement creates metadata in the database. The table in Hive is the way to read data from files present in HDFS in the table or a structural format. The general format of using the command is as follows:

> CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]
> table_name [(col_name data_type [COMMENT col_comment], ...)] [COMMENT
> table_comment] [PARTITIONED BY (col_name data_type [COMMENT
> col_comment], ...)] [CLUSTERED BY (col_name, col_name, ...) [SORTED BY
> (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] [SKEWED BY
> (col_name, col_name, ...) ON ((col_value, col_value,..), (col_value,
> col_value,..), ...) [STORED AS DIRECTORIES] [ [ROW FORMAT row_format]
> [STORED AS file_format] | STORED BY 'storage.handler.class.name' [WITH
> SERDEPROPERTIES (...)] ] [LOCATION hdfs_path] [TBLPROPERTIES
> (property_name=property_value, ...)] [AS select_statement];

Where:
[TEMPORARY]: This is an optional clause. This clause is used to create temporary tables. 
[EXTERNAL]: This is an optional clause. This clause is used to create external tables the same as in the case of RDBMS. 
[IF NOT EXISTS]: This is an optional clause. If there is an attempt to create a table that is already present in the database, an error is thrown. 
[COMMENT col/table_comment]: This is an optional clause. This is used to attach comments to a particular column. 
[PARTITIONED BY]: This is an optional clause. This clause is used to create partitioned tables.
[CLUSTERED BY]: This is an optional clause. This clause is used for bucketing purposes. 
[SKEWED BY]: This option is used to improve performance for tables where one or more columns have skewed values. 
[LOCATION hdfs_path]: This option is used while creating external tables. This is the location where files are placed, which is referred to by the external table for the data.
[TBLPROPERTIES]: This is an optional clause. This clause allows you to attach more information about the table in the form of a key-value pair.
[AS select_statement]: Create Table As Select, popularly known as CTAS, is used to create a table based on the output of the other table or existing

Ex:

    CREATE TABLE olympics(
    Athelete STRING,
    Age INT,
    Country STRING,
    Year INT,
    Closing_Date STRING,
    Sport STRING,
    Gold_Medals INT,
    Silver_Medals INT,
    Bronze_Medals INT,
    Total_Medals INT)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '/t';

8) **Dropping tables:** Drop a table in Hive
DROP TABLE command removes the table from the database, including the data from the table. 
The general format of using the DROP TABLE command is as follows:

> DROP TABLE [IF EXISTS] table_name [PURGE];

Where:
[IF EXISTS]: Is an optional clause. If not used, an error is thrown when there is an attempt to drop a table that does not exist in the database.
[PURGE]: Is an optional clause. If specified, the data is not saved in the Trash folder under the home directory and is lost forever.
Ex:

    Drop table if exists Hive_Test_table1 purge;


9) **Truncating tables:** Truncate a table in Hive
The TRUNCATE command removes all rows from the table as well as from the partition, but keeps the table structure as it is. 
The general format of using the truncate table command is as follows:

> TRUNCATE TABLE table_name [PARTITION partition_spec];

Where:
partition_spec: (partition_column = partition_col_value, partition_column = partition_col_value,...)
Ex:

    Truncate table Sales;


10) **Renaming tables:** Rename a table in Hive
The renaming command renames the old table name with a new table name. The general format of using the RENAME table command is as follows:

> ALTER TABLE table_name RENAME TO new_table_name;

Ex:

     Alter Table Hive_Test_table1 RENAME TO Hive_Test_table;
