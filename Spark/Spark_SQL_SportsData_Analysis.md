**SPARK SQL**
---------
**Task:**
Using spark-sql, Find:
1. What are the total number of gold medal winners every year
2. How many silver medals have been won by USA in each sport



***Note:*** Use the dataset given below:

Data in -> HDFS-Spark/Spark/Dataset/Sports_data.txt of this github


**Solution:-**


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

**What are the total number of gold medal winners every year?**


Using the DataFrame that was created above, Get count of the rows of data that have the medal_type as gold for every year.



    val GoldMedalWinners = sqlContext.sql("select count(*) as WinnerCount ,year from sports  where medal_type ='gold' group by year");

     GoldMedalWinners.show();
     
![enter image description here](https://user-images.githubusercontent.com/29932053/32848105-a74df16c-c9f9-11e7-934f-66d5c1b05d54.png)

**How many silver medals have been won by USA in each sport?**


Using the DataFrame that was created above, Get sport and count of the rows of data that have the country as USA and medal_type as silver for every sport type.

    scala> val silverUSA = sqlContext.sql("select sports,count(*) as MedalCount  from sports where country = 'USA' and medal_type = 'silver'group by sports");

	silverUSA.show();
![enter image description here](https://user-images.githubusercontent.com/29932053/32849077-2a10b11e-c9fc-11e7-8ca0-7cd5219a937e.png)


You can also get the first and last name of the player with a silver medal for every sport and find the sum


    scala> val silverUSA2 = sqlContext.sql("select firstname,lastname,sports,count(*) as MedalCount  from sports where country = 'USA' and medal_type = 'silver'group by firstname,lastname, sports");
	
	silverUSA2.show();
![enter image description here](https://user-images.githubusercontent.com/29932053/32849296-bb2207de-c9fc-11e7-9e03-fc7555d23923.png)

