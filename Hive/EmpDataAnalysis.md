Employee Data Analysis 
------------------------------------------

**Task:**

Calculate the number of employees corresponding to each skill from the table 'employee'. Use data emp_details in dataset.

Find the employee count for a skill or set of skills for a particular location.


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
    
**Solution** for task --> Find the employee count for a skill or set of skills for a particular location.
we can use the partitioning feature in Hive and partition the data based on the field of our choice.Here we are going to partition based on location 

First we create a table named employee_location_Partitioned

    CREATE TABLE employee_location_Partitioned(emp_Name STRING,emp_Skill STRING,emp_Rating INT)
    PARTITIONED BY (emp_Location STRING);
Then we insert the data in to that table  based on Location . Here we use BBSR as a Location.

    INSERT OVERWRITE TABLE employee_location_partitioned
    PARTITION(emp_Location = 'BBSR')
    SELECT emp_Name,emp_Skill,emp_Rating FROM employee WHERE emp_Location = 'BBSR';

Now check the table employee_location_partitioned. it will have the data only of location BBSR

*Note:* This feature will be more useful in cases where the dataset is too big and/or we continuously query only a part of it.


