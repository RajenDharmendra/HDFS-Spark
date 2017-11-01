


Creating a Table in Hive and Loading it with Data.
--------------------------------------------------

**Task:**
Create a database named 'custom'.

Create a table named temperature_data inside custom having below fields:
1. date (mm-dd-yyyy) format
2. zip code
3. temperature

The table will be loaded from comma-delimited file.

Load the dataset.txt (which is ',' delimited) in the table.

*Note*: Use Dataset Temp_Data

**Solution:**

1) Load the dataset in to local File System(FS) and name the file as dataset.txt
cat the file to see the contents
2) Now start Hive.
 By typing `hive` in the terminal
3) Listing the databases currently in the system.

     SHOW DATABASES;

4) Listing the tables currently in the system. 

    SHOW TABLES;


6) Creating the database ‘custom’ and using it (Now we can create tables inside ‘custom’.) 
	

    CREATE DATABASE custom;

7) Creating Temporary table ‘temp’ in custom Database
 

	   USE custom;

  

      CREATE TABLE temp(
      temp_date string,
      zipCode int,
      temperature int)
      ROW FORMAT DELIMITED
      FIELDS TERMINATED BY  ',' ;
      

8) Again listing the tables in ‘custom’ and Databases in the system. 
Temporary table ‘temp’ has been created. Database ‘custom’ has been created.

    SHOW TABLES;
9) To create the table temperature_data, 
Create a temporary table temp and load the ‘comma-delimited file’ contents of the dataset in it. (locally loaded)
Then  convert the date format in the original dataset from dd-mm-yyyy to mm-dd-yyyy as asked in the task and load it in the table temperature_data.






















