**INTRODUCTION TO DATAFRAMES**
--------------------------
**Task:**
1) What is the distribution of the total number of air-travelers per year
2) What is the total air distance covered by each user per year
3) Which user has travelled the largest distance till date
4) What is the most preferred destination for all users.

Use the dataset given below:
https://drive.google.com/drive/folders/0B_P3pWagdIrrVThBaUdVSUtzbms

or You can Use In Dataset 

**Solution:**
**Loading data to dataframe:-**

    scala> val holidaysRDD = sc.textFile("Dataset_Holidays");
    
    scala> val holidaysDF= holidaysRDD.map(lines=>lines.split(",")).map(arrays => (arrays(0),arrays(1),arrays(2),arrays(3),arrays(4),arrays(5))).toDF("Person_ID","Source","Destination","Mode","Distance","Year");
    
    holidaysDF.show();

![enter image description here](https://user-images.githubusercontent.com/29932053/32789205-409aa35a-c929-11e7-939c-42715b561f2e.png)

    scala> val transportRDD = sc.textFile("Dataset_Transport.txt");
    
    scala> val transportDF= transportRDD.map(lines=>lines.split(",")).map(arrays=>(arrays(0),arrays(1))).toDF("Transport_Name","Transport_ID");
    
    transportDF.show();


![enter image description here](https://user-images.githubusercontent.com/29932053/32789448-dd490d2c-c929-11e7-9280-31a9663693b2.png)

    scala> val userRDD = sc.textFile("S18_Dataset_User_details.txt");
    
    scala> val userDF= userRDD.map(lines=>lines.split(",")).map(arrays=>(arrays(0),arrays(1),arrays(2))).toDF("Person_ID","Name","Age");
    
    userDF.show();

![enter image description here](https://user-images.githubusercontent.com/29932053/32790290-0d13039e-c92c-11e7-865a-19c0fd24a9ce.png)

Creating temporary view out of all data frames.

    scala> holidaysDF.registerTempTable("holidays");
    scala> transportDF.registerTempTable("transport");
    scala> userDF.registerTempTable("user");
All the datasets have been loaded in to temporary tables.
These temporary tables and dataframes would be used to find solution to problem statements

Listing out temporary tables and column names:-

holidays     ->   "Person_ID","Source","Destination","Mode","Distance","Year"
transport    ->   "Transport_Name","Transport_ID"
user           ->   "Person_ID","Name","Age"

**Starting with problem statements now:-**

1) What is the distribution of the total number of air-travelers per year

    scala> holidaysDF.groupBy("year").count().show;

![enter image description here](https://user-images.githubusercontent.com/29932053/32790664-0b0a6708-c92d-11e7-94ac-318c05bdd8c7.png)


**2) What is the total air distance covered by each user per year**


    scala> val sqlContext = new org.apache.spark.sql.SQLContext(sc)
    scala> import sqlContext.implicits._   
    
    scala> val joinDF = holidaysDF.as("d1").join(userDF.as("d2"), $"d1.Person_ID" === $"d2.Person_ID").select($"d2.Name", $"d1.Year", $"d1.Distance");
    
    scala> val problem2DF= joinDF.groupBy("Name","Year").agg(sum("Fare"));
    
    scala>  problem2DF.show(150);
![enter image description here](https://user-images.githubusercontent.com/29932053/32791963-37120bd2-c930-11e7-9f82-2b262a5ed75d.png)

**3) Which user has travelled the largest distance till date**

    scala> val problem3DF= joinDF.groupBy("Name").agg(sum("Distance")).orderBy($"sum(Distance)".desc).show(1);
![enter image description here](https://user-images.githubusercontent.com/29932053/32792281-ff9a7224-c930-11e7-81e6-fc8a4fe4108e.png)

***Mark has travelled the most with 1600 Km.***

**4) What is the most preferred destination for all users.**

    scala> val problem4DF = holidaysDF.groupBy("Destination").count().orderBy($"count".desc).show(1);

![enter image description here](https://user-images.githubusercontent.com/29932053/32792508-7c479b58-c931-11e7-9218-a6ab11799a0c.png)

***IND (India) with visit count of 9 is the most preferred destination for all users.***


