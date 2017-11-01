Employee Data Analysis 
------------------------------------------

**Task:**

Calculate the number of employees corresponding to each skill from the table 'employee'. Use data emp_details in dataset.
**Solution:** 
Load the data in to local file system as emp_details.txt.

    nano emp_details.txt;
load the data in to the file save and exit then check the details in the file 

     cat emp_details.txt
Now start Hive 

    hive

Go to custom database we already created

    SHOW DATABASES;
    USE custom;
create table employee in custom database

    CREATE TABLE employee(emp_Name STRING,emp_Skill STRING,emp_Rating INT,emp_Location STRING)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ',';
Load data in to employee table

    LOAD DATA LOCAL INPATH '/home/path/to/your/file/emp_details.txt' INTO TABLE employee;

check the table 

    SELECT * FROM employee;
    
To calculate the number of employees corresponding to each skill: Selecting the Skill and Count (Can be Count(emp_Name) too) and grouping the result according to the Skill. So we get each Skill with a count of the employees.

    SELECT emp_Skill AS SKILL ,COUNT(emp_name) AS EMP_COUNT FROM employee GROUP BY emp_Skill;
or

    SELECT emp_Skill AS SKILL ,COUNT(*) AS EMP_COUNT FROM employee GROUP BY emp_Skill;
