**Creating UDFs on DataFrame's using Spark-SQL**
--------------------------------------------
**Task:**


Using UDFs on DataFrame:

Change firstname and lastname columns into:

Mr.first_two_letters_of_firstname     space   lastname

Example - michael, phelps becomes Mr.mi phelps


Add a new column called ranking using UDFs on DataFrame, where : gold medalist, with age >= 32 are ranked as pro

gold medalists, with age <= 31 are ranked amateur silver medalist, with age >= 32 are ranked as expert silver medalists, with age <= 31 are ranked rookie





**Dataset:**



The following dataset is a Sports Dataset with columns: firstname, lastname, sports, medal_type, age, year, country

***Note:*** Use the dataset given below:

Data in -> HDFS-Spark/Spark/Dataset/Sports_data.txt   
of this **github repo**

**Solution:**

Loading the data:-

    scala> val sportsRDD = sc.textFile("Sports_data.txt");
![enter image description here](https://user-images.githubusercontent.com/29932053/32846714-fb319eae-c9f5-11e7-827d-e4247cfdbbee.png)


    scala> val header = sportsRDD.first() ;
    
    scala> val sportsRDDFiltered = sportsRDD.filter(row => row != header);

![enter image description here](https://user-images.githubusercontent.com/29932053/32846868-65634bba-c9f6-11e7-932f-b3cf7c479446.png)

Loading the data into DataFrame

    Scala> val sportsDF= sportsRDDFiltered.map(lines=>lines.split(",")).map(arrays => (arrays(0),arrays(1),arrays(2),arrays(3),arrays(4),arrays(5),arrays(6))).toDF("firstname","lastname","sports","medal_type","age","year","country");
![enter image description here](https://user-images.githubusercontent.com/29932053/32847415-eae25690-c9f7-11e7-94ad-ed34e0361674.png)

Registering DataFrame as table:

    Scala> sportsDF.registerTempTable("sports");

    val sportsCData = sqlContext.sql("select * from sports");

    sportsCData.show;


![enter image description here](https://user-images.githubusercontent.com/29932053/32847415-eae25690-c9f7-11e7-94ad-ed34e0361674.png)

Based on DataFrame “sportsDF” we can find solution to the problem statements

1. Change firstname, lastname columns into
**Mr.first_two_letters_of_firstname<space>lastname**
for example - **michael, phelps** becomes **Mr.mi phelps**

***Creating UDF :-***

    scala> val convertName = udf((fname: String, lname: String) => { "Mr."+fname.slice(0,2)+" "+lname+""});

**Adding column “Fullname” to Data Frame using UDF and Removing columns “firstname” and “lastname” from  DataFrame **

    scala> val sportsChangedName = spartsDF.withColumn("Fullname",convertName($"firstname",$"lastname")).drop("firstname").drop("lastname");
   

    scala>sportsChangedName show(50);

![enter image description here](https://user-images.githubusercontent.com/29932053/32851302-9bede328-ca02-11e7-934d-1b30a199934d.png)


 
2.Add a new column called ranking using udfs on dataframe, where :
gold medalist, with age >= 32 are ranked as pro
gold medalists, with age <= 31 are ranked amateur
silver medalist, with age >= 32 are ranked as expert
silver medalists, with age <= 31 are ranked rookie

**Creating UDF:-**
Create a Spark SQL UDF that takes the medal_type and age as parameters and performs the change as required in the problem statement. AddRanking

Using the DataFrame SportsDF created above with the withColumn function, we:

Assign the name for the new column (ranking) and

Call the UDF AddRanking with the 1st argument as medal_type and the 2nd argument as the age

    def ADDRanking = udf((medal:String,age:Int)=>{
    if(medal =="gold" && age>=32) "pro"
    else if(medal =="gold" && age<=31) "amateur"
    else if(medal =="silver" && age>=32) "expert"
    else if(medal =="silver" && age<=31) "rookie"
    else "Poor"
    });



    scala> val sportsDataRanking = sportsDF.withColumn("Ranking",ADDRanking($"medal_type",$"age"));


    scala> sportsDataRanking.show(50);
![enter image description here](https://user-images.githubusercontent.com/29932053/32853245-a5fa3424-ca08-11e7-84e2-a36eb75a6723.png)


