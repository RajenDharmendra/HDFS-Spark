**State-Wise Development Analysis in India**
----------------------------------------
**1. Executive Summary**

**1.1 Project Overview**
To develop the System to analyze the log data (In XML format) of government progress of various development activities.

**1.2 Purpose and Scope of this Specification**
The purpose of this project is to capture the data for analyzing the progress of various activities.

> **In scope** The following requirement will be addressed in phase 1 of
> Project: Developing system to handle the incoming log feed and store
> the information in Hadoop Cluster (Flume) Analyze the data and
> understand the progress Store the results in Hbase/RDBMS
> 
> **Out of scope** We can use this data and visualization and get more
> insights



**2.Product/Service Description**

**2.1 Assumptions**
Log will be generated in XML format and stored in a server

**2.2 Constraints**
Describe any item that will constrain the design options, including
This system may not be used for searching for now. But it will be used for analysis and saving the relevant information as of now
System will be using Hbase as a database


**3. Requirements**

The FLUME job which will format the data and place the data to HDFS
Pig/MapReduce job for parsing the XML data.
Create Pig scripts/MapReduce jobs to analyze the data
Create the Sqoop job to store the data in database

**Priority Definitions**
The following definitions are intended as a guideline to prioritize requirements.
*Priority 1* – Create FLUME job for fetching log files from spool directory the data
*Priority 2* – MapReduce/pig job to preprocess

**Problem Statement:**

Exporting the Data from the Local File System to the HDFS using Flume

Performing Analysis on the data (in xml form) using PIG to get results for the below problem statements:


Find out the districts who achieved 100 percent objective in BPL cards
Export the results to MySQL using Sqoop

Write a Pig UDF to filter the districts which have reached 80% of objectives of BPL cards.
Export the results to MySQL using Sqoop.
Dataset:


The dataset is an xml file that contains the State-Wise Development data for India

Google Drive Link: 
https://drive.google.com/file/d/0Bxr27gVaXO5sUjd2RWFQS3hQQUE/view?usp=sharing


Screenshot:
A sample view of the data in the xml file.
![enter image description here](https://user-images.githubusercontent.com/29932053/32508198-91baf9be-c3b7-11e7-957c-b175a8752213.png)

*Note: In this project we will use flume to collect and move data from LocalFS to HDFS*

**Exporting the Data from the Local File System to the HDFS using Flume**

To perform this task we have to execute the following steps:

Create the spool directory from where Flume will retrieve the data to be stored in the HDFS


*Note: We will export dataset from local filesystem to HDFS using Flume* 

Create the configuration document for the flume job. This will contain the necessary information for fetching log files from spool directory and storing these files in the HDFS

Below is the configuration file fileExport.conf, some important configurations are:
*Specifying the type of structure the file is coming in the channel: file
Specifying the capacity of the transmission channel
Specifying the type of source: spool directory source
Specifying the path of the spool directory
Specifying the suffix to be added to the name of the file in the spool directory
Specifying the path in the HDFS to store the data*

*Note: For detailed configuration guidelines --> https://flume.apache.org/FlumeUserGuide.html*

*flumeExport.conf* ->    		file contents

    SWDWPPAgent.channels.fchannel.type = file
    
    SWDWPPAgent.channels.fchannel.capacity = 200000
    SWDWPPAgent.channels.fchannel.transactioncapacity = 1000
    SWDWPPAgent.sorces.fsource.type = spooldir
    
    SWDWPPAgent.sorces.fsource.spooldir = /home/username/hdfsSpark/flumeExport
    SWDWPPAgent.sorces.fsource.fileHeader = false
    SWDWPPAgent.sorces.fsource.fileSuffix = .COMPLETED
    SWDWPPAgent.sinks.hdfssink.type = hdfs
    
    SWDWPPAgent.sinks.hdfssink.hdfs.path = hdfs://www.yourdomain.com:8020/user/username/flumeImport
    SWDWPPAgent.sinks.hdfssink.hdfs.batchSize = 1000
    SWDWPPAgent.sinks.hdfssink.hdfs.rollSize = 268435456
    SWDWPPAgent.sinks.hdfssink.hdfs.rollInterval = 0
    SWDWPPAgent.sinks.hdfssink.hdfs.rollCount = 50000000
    SWDWPPAgent.sinks.hdfssink.hdfs.writeFormat = Text
    
    SWDWPPAgent.sinks.hdfssink.hdfs.fileType = DataStream
    SWDWPPAgent.sources.fsource.channels = fchannel
    SWDWPPAgent.sinks.hdfssink.channel = fchannel
    
    SWDWPPAgent.sinks = hdfssink
    SWDWPPAgent.sources = fsource
    SWDWPPAgent.channels = fchannel

Create the folder flume_import in the HDFS that will hold the data from Flume Agent/Job

Execute the flume command that will create the flume job fetching data from the Local File System to the HDFS:

    flume-ng agent –n <agentName> –f <path to fileExport.conf>

This will now check the spool directory flume_export for the log file to export and then export/store it in the HDFS directory flume_import as given in the configuration file.

Checking the HDFS import directory flume_import to see if the data has been exported successfully


The xml file has been successfully exported
Also, below is the xml data file in the spool directory. As mentioned in the configuration file, the flume job has added the COMPLETED as suffix addition to the file name. This shows us that the file has been successfully read from the spool directory.


**Performing Analysis on the data (in xml form) using PIG** 


Find out the districts who achieved 100 percent objective in BPL cards. Export the results to MySQL using Sqoop

Starting the Pig Shell using the command pig (not local so we can access the HDFS)

    pig

Registering the piggybank jar that contains the executables for various pig functions. 

    REGISTER /usr/hdp/current/pig-client/piggybank.jar;
    
Defining the XML Parse function as XPath (name used to call the function) 

    DEFINE XPath org.apache.pig.piggybank.evaluation.xml.XPath();

Loading the data in the HDFS (that was exported using Flume) and using the XML Loader function to load the data into the relation -> A with every starting tag ‘row’ as one line of type: chararray with the name sdaIndia

    A = LOAD 'hdfs://www.yourdomain.com:8020/user/yourusername/flumeImport/StatewiseDistrictwisePhysicalProgress.xml' USING org.apache.pig.piggybank.storage.XMLLoader('row') as (sdaIndia:chararray);

Generating the rows (sdaIndia) in relation A by using the XML Parser XPath. Every tag under the main tag row will be separated by the tag name and given a pseudo name in the relation.

    B = FOREACH A GENERATE XPath(sdaIndia,'row/State_Name') AS State_Name,XPath(sdaIndia,'row/District_Name') AS District_Name,XPath(sdaIndia,'row/Project_Objectives_IHHL_BPL') AS PO_IHHL_BPL,XPath(sdaIndia,'row/Project_Objectives_IHHL_APL') AS PO_IHHL_APL,XPath(sdaIndia,'row/Project_Objectives_IHHL_TOTAL') AS PO_IHHL_TOTAL,XPath(sdaIndia,'row/Project_Objectives_SCW') AS PO_SCW,XPath(sdaIndia,'row/Project_Objectives_School_Toilets') AS PO_School_Toilets,XPath(sdaIndia,'row/Project_Objectives_Anganwadi_Toilets') AS PO_Anganwadi_Toilets,XPath(sdaIndia,'row/Project_Objectives_RSM') AS PO_RSM,XPath(sdaIndia,'row/Project_Objectives_PC') AS PO_PC,XPath(sdaIndia,'row/Project_Performance-IHHL_BPL') AS PP_IHHL_BPL,XPath(sdaIndia,'row/Project_Performance-IHHL_APL') AS PP_IHHL_APL,XPath(sdaIndia,'row/Project_Performance-IHHL_TOTAL') AS PP_IHHL_TOTAL,XPath(sdaIndia,'row/Project_Performance-SCW') AS PP_SCW,XPath(sdaIndia,'row/Project_Performance-School_Toilets') AS PP_School_Toilets,XPath(sdaIndia,'row/Project_Performance-Anganwadi_Toilets') AS PP_Toilets,XPath(sdaIndia,'row/Project_Performance-RSM') AS PP_RSM,XPath(sdaIndia,'row/Project_Performance-PC') AS PP_PC;


Displaying the results of the Load statement

    DUMP A;

![enter image description here](https://user-images.githubusercontent.com/29932053/32520982-e0e26ef0-c3df-11e7-902c-7497f3b69cbb.png)


Displaying the result of the Row Generating statement. All the data has been separated by tag name and formatted into a tuple of multiple fields.

    DUMP B;
![enter image description here](https://user-images.githubusercontent.com/29932053/32521097-39cdf02a-c3e0-11e7-8868-985f6c2e9f2a.png)

Generating column names pertaining to District and BPL information and finding the Percentage of performance achieved for the objective that was set for BPL Cards in India.

    C = FOREACH B GENERATE District_Name,PO_IHHL_BPL,PP_IHHL_BPL,ROUND_TO(((double)PP_IHHL_BPL/(double)PO_IHHL_BPL)*100,2) AS BPL_Percentage;



Filtering the above result for those records where 100% objective has been met and displaying the result.

    D = FILTER C BY BPL_Percentage==100;

The result of the above procedure:

![enter image description here](https://user-images.githubusercontent.com/29932053/32522307-d91ecd08-c3e4-11e7-8a63-1adbe0ce15a6.png)

Now we store the result in the HDFS for the Sqoop job to export the data to a MySQL database 

Storing the data in the HDFS under the path given below and separating the fields by tab space

    STORE D INTO '/home/username/path/to/folder/sdaIndia' USING PigStorage('\t');

To check if the file has been successfully stored in the HDFS, we check the output folder of its contents.
The data has been stored successfully as seen by the file named part-m-00000 that hold the output of the MapReduce job

    hadoop fs -cat /home/username/path/to/folder/sdaIndia/part-m-00000 


![enter image description here](https://user-images.githubusercontent.com/29932053/32523006-933becb4-c3e7-11e7-9c12-95048b392bfb.png)
Now we export the data in the HDFS to a Table in MySQL by the following steps:

Start the MySQL service and terminal and create the database and table to hold the data
Here we create table  named `SD_Analysis_100`


    mysql -u yourusername -h yourhostname -p 
    
    SHOW DATABASES;
    
    USE YOURDATABASE;
    
    SHOW TABLES;
    
    CREATE TABLE SD_Analysis_100(
    District_Name varchar(30),
    PO_IHHL_BPL int,
    PP_IHHL_BPL int,
    BPL_Percentage double
    );
    
    
    SELECT * FROM SD_Analysis_100;


**Using the Sqoop command given below:**
Specify the name of the database to hold the data
Specify the password of the VM (Can also be manually entered or got from a password file)
Specify the name of the table to hold the data
Specify the directory in the HDFS that holds the data
Specify how the fields are terminated
Specify the number of MapReduce jobs :1
Specify the column names to import to the MySQL table

    sqoop-export  \
    --connect jdbc:mysql://nn01.itversity.com:3306/retail_import  \
    --username=retail_dba \
    --password=itversity \
    --table=SD_Analysis_100 \
    --export-dir=hdfsSpark/miniProject/sdaIndia \
    --input-fields-terminated-by '\t' \
    --columns District_Name,PO_IHHL_BPL,PP_IHHL_BPL,BPL_Percentage \
    -m 1;
After exporting check the table

      SELECT * FROM SD_Analysis_100;
Output:


![enter image description here](https://user-images.githubusercontent.com/29932053/32557912-88800686-c471-11e7-98b2-24013e7f1d9f.png)

**Write a Pig UDF to filter the districts which have reached 80% of objectives of BPL cards. Export the results to MySQL using Sqoop.**

To filter the districts that have reached 80% of their objectives in BPL Cards

To Acheive the above results we will write a pig script
The following are the contents of the script

    --Pig UDF filter the Districts which have reached 80% of objectives of BPL Cards
    
    
    REGISTER /usr/hdp/current/pig-client/piggybank.jar;
    
    DEFINE XPath org.apache.pig.piggybank.evaluation.xml.XPath();
    
    REGISTER  /home/username/hdfsSpark/sdaIndia/udfFilter80.jar;
    
    DEFINE getPercentage sdaIndia_80.Filter80;
    
    A = LOAD 'hdfs://www.yourdomain.com:8020/user/username/flumeImport/StatewiseDistrictwisePhysicalProgress.xml' USING org.apache.pig.piggybank.storage.XMLLoader('row') as (sdaIndia:chararray);
    
    B = FOREACH A GENERATE XPath(sdaIndia,'row/State_Name') AS State_Name,XPath(sdaIndia,'row/District_Name') AS District_Name,XPath(sdaIndia,'row/Project_Objectives_IHHL_BPL') AS PO_IHHL_BPL,XPath(sdaIndia,'row/Project_Objectives_IHHL_APL') AS PO_IHHL_APL,XPath(sdaIndia,'row/Project_Objectives_IHHL_TOTAL') AS PO_IHHL_TOTAL,XPath(sdaIndia,'row/Project_Objectives_SCW') AS PO_SCW,XPath(sdaIndia,'row/Project_Objectives_School_Toilets') AS PO_School_Toilets,XPath(sdaIndia,'row/Project_Objectives_Anganwadi_Toilets') AS PO_Anganwadi_Toilets,XPath(sdaIndia,'row/Project_Objectives_RSM') AS PO_RSM,XPath(sdaIndia,'row/Project_Objectives_PC') AS PO_PC,XPath(sdaIndia,'row/Project_Performance-IHHL_BPL') AS PP_IHHL_BPL,XPath(sdaIndia,'row/Project_Performance-IHHL_APL') AS PP_IHHL_APL,XPath(sdaIndia,'row/Project_Performance-IHHL_TOTAL') AS PP_IHHL_TOTAL,XPath(sdaIndia,'row/Project_Performance-SCW') AS PP_SCW,XPath(sdaIndia,'row/Project_Performance-School_Toilets') AS PP_School_Toilets,XPath(sdaIndia,'row/Project_Performance-Anganwadi_Toilets') AS PP_Toilets,XPath(sdaIndia,'row/Project_Performance-RSM') AS PP_RSM,XPath(sdaIndia,'row/Project_Performance-PC') AS PP_PC;
    
    
    C = FOREACH B GENERATE State_Name .. PP_PC,ROUND_TO(getPercentage((double)PP_IHHL_BPL,(double)PO_IHHL_BPL),2) AS BPL_Percent;
    
    D = FILTER C BY BPL_Percent!=0.0;
    
    E = FOREACH (GROUP D ALL) GENERATE COUNT_STAR(D);
    
    STORE D INTO 'hdfsSpark/miniProject/sdaIndia80' USING PigStorage('\t');
    
    
we have created a Pig Script(with commands similar to the problem before) and execute it via the pig MapReduce shell

**The steps followed are explained as below:**

Registering the piggybank jar that contains the executables for various pig functions. Ex: Parse XML (Used in this assignment)


Defining the XML Parse function as XPath (name used to call the function) 

Registering the Pig UDF miniprojectudf created to filter the districts which have reached 80% of objectives of BPL cards. (Written in Java)

Defining getPercentage as the function to be used to execute the UDF in package filterBPL80 and class Filter80

Loading the data in the HDFS (that was exported using Flume) and using the XML Loader function to load the data into the relation A with every starting tag ‘row’ as one line of type: chararray with the name sdaIndia

Generating the rows (sdaIndia) in relation A by using the XML Parser XPath. Every tag under the main tag row will be separated by the tag name and given a pseudo name in the relation.
Generating all column and finding the Percentage of performance achieved, for the objective that was set for BPL Cards in India, by using a Pig UDF written in java and exported as a jar as below:

Below is an image of the Pig UDF 

![enter image description here](https://user-images.githubusercontent.com/29932053/32563729-fdf890b4-c47f-11e7-807b-519dd0a012a6.png)

The UDF exported as a jar
Place the UDF in the directory from where it is accessed

Filtering the above result for those records where percentage is 0.0% (The records that do not meet the 80% objective). Therefore giving us the records that have received 80% and above in BPL cards
Getting the count of the filtered records
Storing the results, i.e. the filter records into a directory in the HDFS and separating the fields by tab space

Executing the Pig Script in MapReduce mode (can access HDFS) as below:

    pig -x mapreduce /home/username/hdfsSpark/sdaIndia/sdaIndia80.pig;
    
The execution was successful

![enter image description here](https://user-images.githubusercontent.com/29932053/32563105-4e09133c-c47e-11e7-864a-0720d343c2c8.png)

Checking the contents of the folder statedistrictanalysis80 in HDFS that contains the filtered data

The data has been stored successfully as seen by the file named part-m-00000 that hold the output of the MapReduce job.

![enter image description here](https://user-images.githubusercontent.com/29932053/32563285-da9c012e-c47e-11e7-8c82-e1777cbc7494.png)
