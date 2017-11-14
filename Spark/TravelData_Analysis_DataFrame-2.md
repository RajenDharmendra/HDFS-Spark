**INTRODUCTION TO DATAFRAMES**
--------------------------
**Task:**

1. Which route is generating the most revenue per year
2. What is the total amount spent by every user on air-travel per year
3. Considering age groups of < 20 , 20-35, 35 > ,Which age group is travelling the most every year.

***Note:*** Use the dataset given below:

Data in -> HDFS-Spark/Spark/Dataset of this github

**Solution:**

Loading data to dataframe:-

    Scala> val holidaysRDD = sc.textFile("Dataset_Holidays.txt");
    
    Scala> val holidaysDF= holidaysRDD.map(lines=>lines.split(",")).map(arrays => (arrays(0),arrays(1),arrays(2),arrays(3),arrays(4),arrays(5))).toDF("Person_ID","Source","Destination","Mode","Distance","Year");
    
    Scala> val transportRDD = sc.textFile("Dataset_Transport.txt");
    
    Scala> val transportDF= transportRDD.map(lines=>lines.split(",")).map(arrays=>(arrays(0),arrays(1))).toDF("Transport_Name","Fare");
    
    Scala> val userRDD = sc.textFile("Dataset_User_details.txt");
     val userDF= userRDD.map(lines=>lines.split(",")).map(arrays=>(arrays(0),arrays(1),arrays(2))).toDF("Person_ID","Name","Age");
     
Creating temporary view out of all data frames.

    scala> holidaysDF.registerTempTable("holidays");
    scala> transportDF.registerTempTable("transport");
    scala> userDF.registerTempTable("user");

All the datasets have been loaded in to temporary tables.
These dataframes would be used to find solution to problem statements

Listing out dataframes and column names:-

holidaysDF     ->   "Person_ID","Source","Destination","Mode","Distance","Year"
transportDF    ->   "Transport_Name","Fare"
user DF          ->   "Person_ID","Name","Age"


Starting with problem statements now:-

**1. Which route is generating the most revenue per year**

    scala> val joinDF1 = holidaysDF.as("d1").join(transportDF.as("d2"), $"d1.Mode" === $"d2.Transport_Name").select($"d1.Source", $"d1.Destination", $"d1.Year",$"d2.Fare");
	joinDF1.show();
![enter image description here](https://user-images.githubusercontent.com/29932053/32795026-89d57e5a-c938-11e7-8e00-ccdfb3f8cde9.png)

    val problem1DF = joinDF1.groupBy("Source","Destination","Year").agg(sum("Fare")).orderBy($"sum(Fare)".desc);
    
    problem1DF.show();
![enter image description here](https://user-images.githubusercontent.com/29932053/32795158-e2daa7be-c938-11e7-944b-9ddf9ab69efb.png)

    val yearMax = problem1DF.groupBy("Year").agg(max("sum(Fare)"));
    
    yearMax.show();
    
![enter image description here](https://user-images.githubusercontent.com/29932053/32795358-6932f654-c939-11e7-8745-fd9c2a5540a0.png)


    val  mostRevenueRoute =  problem1DF.as("d1").join(yearMax.as("d2"), $"d1.Year" === $"d2.Year" && $"d1.sum(Fare)" === $"d2.max(sum(Fare))").select($"d1.Source", $"d1.Destination", $"d1.Year",$"d1.sum(Fare)").orderBy($"d1.Year");


![enter image description here](https://user-images.githubusercontent.com/29932053/32796131-c7ef510e-c93b-11e7-88dd-d02e146c1f91.png)

**Above is the list of most profitable routes per year.**

**2. What is the total amount spent by every user on air-travel per year**

    val join2DF = holidaysDF.as("d1").join(userDF.as("d2"), $"d1.Person_ID" === $"d2.Person_ID").select($"d2.Name", $"d1.Year", $"d1.Mode");
    
	join2DF.show();
![enter image description here](https://user-images.githubusercontent.com/29932053/32799068-824127be-c944-11e7-86ef-3f30ee18fb24.png) 

     val joinWithPrice = join2DF.as("d1").join(transportDF.as("d2"), $"d1.Mode" === $"d2.Transport_Name").select($"d1.Name", $"d1.Year", $"d1.Mode",$"d2.Fare");

	joinWithPrice.show();

![enter image description here](https://user-images.githubusercontent.com/29932053/32799151-de137cea-c944-11e7-95e8-08d58421c740.png)

    val problem2DF= joinWithPrice.groupBy("Name","Year").agg(sum("Fare"));

    problem2DF.show();
	
![enter image description here](https://user-images.githubusercontent.com/29932053/32799359-7ae283d6-c945-11e7-8018-87533a5c6141.png)

***Above is the list of total amount spent by every user on air-travel per year***

**3. Considering age groups of < 20 , 20-35, 35 > ,Which age group is traveling the most every year**

First we create a User Defined Function(udf) ageGrp

    val ageGrp = udf((age: String) => {  if(age.toInt < 20)  {  "<20";  }  else  { if(age.toInt > 35)  {  ">35";  }  else {  "20-35";  }}})

     val userDFGrp = userDF.withColumn("AgeGrp", ageGrp($"Age"))
     userDFGrp.show();


![enter image description here](https://user-images.githubusercontent.com/29932053/32799815-c0acce16-c946-11e7-8f1d-91f11a1c691b.png)

    val joinDF3 = holidaysDF.as("d1").join(userDFGrp.as("d2"), $"d1.Person_ID" === $"d2.Person_ID").select($"d2.AgeGrp", $"d1.Year", $"d1.Mode");

![enter image description here](https://user-images.githubusercontent.com/29932053/32799948-15a115c6-c947-11e7-933f-7463e28fbf1b.png)

    val groupedJoin = joinDF3.groupBy("AgeGrp","Year").count();

	groupedJoin .show();

![enter image description here](https://user-images.githubusercontent.com/29932053/32800104-7b470f66-c947-11e7-92c2-eb849a7510e6.png)

     val yearMax = groupedJoin.groupBy("Year").agg(max("count"));

	yearMax.show();
![enter image description here](https://user-images.githubusercontent.com/29932053/32800267-db055fc0-c947-11e7-8ee3-6c34df733fa1.png)


    val maxAgeGrp = groupedJoin.as("d1").join(yearMax.as("d2"), $"d1.Year" === $"d2.Year" && $"d1.count" === $"d2.max(count)").select($"d1.AgeGrp", $"d1.Year", $"d2.max(count)");

	maxAgeGrp.show();
	
![enter image description here](https://user-images.githubusercontent.com/29932053/32800451-5cd0f546-c948-11e7-8eba-08ee6b001966.png)

    val maxAgeGrpSortByYear = maxAgeGrp.orderBy("Year");
    
    maxAgeGrpSortByYear.show();
    
![enter image description here](https://user-images.githubusercontent.com/29932053/32800914-9cacf7ae-c949-11e7-9852-dc63a0ea1e6b.png)


In 1990 age group 20-35 travelled most with 5 trips
In 1991 age group 20-35 travelled most with 4 trips
In 1992 age group >35 travelled most with 4 trips
In 1993 age group <20 travelled most with 5 trips
In 1994 age group 20-35 travelled most with 1 trip



