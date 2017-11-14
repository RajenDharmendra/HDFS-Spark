**INTRODUCTION TO DATAFRAMES**
--------------------------
**Task:**

1) Considering age groups of < 20 , 20-35, 35 > ,Which age group spends the most
amount of money travelling.

2) What is the amount spent by each age-group, every year in travelling?

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



First we create a User Defined Function(udf) ageGrp

    val ageGrp = udf((age: String) => {  if(age.toInt < 20)  {  "<20";  }  else  { if(age.toInt > 35)  {  ">35";  }  else {  "20-35";  }}})

     val userDFGrp = userDF.withColumn("AgeGrp", ageGrp($"Age"))
     
	 userDFGrp.show();

![enter image description here](https://user-images.githubusercontent.com/29932053/32799815-c0acce16-c946-11e7-8f1d-91f11a1c691b.png)

    val holidayWithPriceDF = holidaysDF.as("d1").join(transportDF.as("d2"), $"d1.Mode" === $"d2.Transport_Name").select($"d1.*", $"d2.Fare");
    
    holidayWithPriceDF.show();

![enter image description here](https://user-images.githubusercontent.com/29932053/32807836-35926f84-c95f-11e7-90e7-cf0aac7c97b5.png)

    val userHoliday = holidayWithPriceDF.as("d1").join(userDFGrp.as("d2"), $"d1.Person_ID" === $"d2.Person_ID").select($"d1.*", $"d2.*");
    
	userHoliday.show();
![enter image description here](https://user-images.githubusercontent.com/29932053/32808031-d66b7644-c95f-11e7-8837-2a8bf9273e3b.png) 

    val userGrpExpenditure = userHoliday.groupBy("AgeGrp").agg(sum("Fare")).orderBy($"sum(Fare)".desc); 

	userGrpExpenditure.show();
	
![enter image description here](https://user-images.githubusercontent.com/29932053/32809726-01a8b5aa-c966-11e7-926d-39a22f558ddb.png)

Age group 20-35 has spent most amounts i.e. 2210.0 on air travelling

**2) What is the amount spent by each age-group, every year in travelling?**

	   val userGrpYearlyExpenditure = userHoliday.groupBy("AgeGrp","Year").agg(sum("Fare")).orderBy($"AgeGrp", $"Year", $"sum(Fare)".desc);
  
    
	    userGrpYearlyExpenditure.show;
![enter image description here](https://user-images.githubusercontent.com/29932053/32809853-8f05a4da-c966-11e7-8397-5abffed2b757.png)

Above is a list of amount spent by each age-group, every year in travelling
