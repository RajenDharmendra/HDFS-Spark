**Olympics Data Analysis**
----------------------

**Task:**

1. Write a Hive program to find the number of medals won by each country in swimming.
2. Write a Hive program to find the number of medals that Canada won year wise.
3. Write a Hive Program to find the total number of medals each country won.
4. Write a Hive program to find the number of gold medals each country won.

Use *olympics_data.csv* file in the data set 

**Data Set Description:**
The data set consists of the following fields.

**Athlete:** This field consists of the athlete name 

**Age:** This field consists of athlete ages 

**Country:** This fields consists of the country names which participated in Olympics 

**Year:** This field consists of the year 

**Closing Date:** This field consists of the closing date of ceremony 

**Sport:** Consists of the sports name 

**Gold Medals:** No. of Gold medals 

**Silver Medals:** No. of Silver medals 

**Bronze Medals:** No. of Bronze medals 

**Total Medals:** Consists of total no. of medals


**Initialization:**

Using the custom database to create the tables that we use in previous tasks

**Solution:**
 Create table Olympics for the dataset ‘olympics_data.csv’ which accepts datasets that have fields delimited by ‘\t’ 

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

Load the dataset (with fields delimited by ‘\t’) ‘olympics_data.csv’ into the table Olympics

First load the data in local file system, and then copy in to the table	

    LOAD DATA LOCAL INPATH '/home/path/to/your/file/olympics_data.csv' INTO TABLE olympics;
    
  First load the data in local file system, and then copy in to the table	

    LOAD DATA LOCAL INPATH '/home/path/to/your/file/olympics_data.csv' INTO TABLE olympics;

**Task->1.** Write a Hive program to find the number of medals won by each country in swimming.

Selecting number of gold, silver, bronze and overall/total medals won by each Country for Swimming from the Olympics table.

For this, we use the sum function to sum up all the different types of medals for each Country by grouping the data with respect to the Country and only selecting records where the Sport = Swimming.

Here the total_medals is the final overall number of medals won by a Country in Swimming.

    SELECT Country,SUM(gold_medals) AS GOLD,SUM(silver_medals) AS SILVER,SUM(bronze_medals) AS BRONZE,SUM(total_medals) AS TOTAL FROM olympics WHERE Sport="Swimming" GROUP BY Country;
    
    
**Task->2 **  Write a Hive program to find the number of medals that Canada won year wise.
  This we can achieve by partitioning the  table

**Partitioning the Table:**
1) Creating the table ‘Olympics_Country_Partitioned’ and partitioning it by Country
2) Inserting data into the partitioned table and specifying the partition factor for Country as Canada. 
Then loading the data from the table Olympics (Only data specific to Country = Canada) into the partitioned table.
Now the partitioned table contains data exclusively for records where Country = Canada.

       CREATE TABLE olympics_country_partitioned(
       Athelete STRING,
       Age INT,
       Year INT,
       Closing_Date STRING,
       Sport STRING,
       Gold_Medals INT,
       Silver_Medals INT,
       Bronze_Medals INT,
       Total_Medals INT)
       PARTITIONED BY (Country STRING);

       INSERT OVERWRITE TABLE olympics_country_partitioned
       PARTITION(Country = 'Canada')
       SELECT Athelete,Age,Year,Closing_Date,Sport,Gold_Medals,Silver_Medals,Bronze_Medals,Total_Medals FROM olympics WHERE                          Country = 'Canada';
