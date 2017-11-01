**Olympics Data Analysis**
----------------------

**Task:**

1. Write a Hive program to find the number of medals won by each country in swimming.
2. Write a Hive program to find the number of medals that India won year wise.
3. Write a Hive Program to find the total number of medals each country won.
4. Write a Hive program to find the number of gold medals each country won.

Use olympics_data.csv file in the data set 

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
