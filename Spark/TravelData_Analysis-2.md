**Travel Data Exercise using Scala Part 2**
---------------------------------------

Task:



1) Which route is generating the most revenue per year?



2) What is the total amount spent by every user on air-travel per year



3) Considering age groups of < 20, 20-35, 35 >, which age group is travelling the most every year.



**Dataset:**



> Note: The datasets are availabe in Dataset folder





*Dataset_Holidays:*

The dataset is of holiday details of travelers with columns: user_id, src, dest, travel_mode, distance, year_of_travel:

![enter image description here](https://user-images.githubusercontent.com/29932053/32736111-a767d1fe-c864-11e7-9921-5a27dc3109d8.png)

*Dataset_Transport:*

The dataset is of transport details with columns: travel_mode, cost_per_unit:

![enter image description here](https://user-images.githubusercontent.com/29932053/32736233-ea932cb2-c864-11e7-9fb8-3c0efb0979d4.png)

*Dataset_User_details:*

The dataset is of user details of travelers with columns: user_id, name,age:

![enter image description here](https://user-images.githubusercontent.com/29932053/32736321-23d55180-c865-11e7-8f66-35df42b47680.png)

**Solution:**

**Which route is generating the most revenue per year?**



To find route is generating the most revenue per year:

Create a Tupled RDD from the holiday’s data set (in text file format). Stored as holidaysRDD.


    val holidaysRDD = sc.textFile("hadoopSpark/dataset/Dataset_Holidays").map(x =>
	     {
         | val row = x.split(",")
         | (row.apply(0).toInt,row.apply(1),row.apply(2),row.apply(3),row.apply(4).toInt,row.apply(5).toInt)
         | });
![enter image description here](https://user-images.githubusercontent.com/29932053/32737533-5380b25a-c868-11e7-8af7-d5b25113a37f.png)


Create a Tupled RDD from the  Dataset_Transport data set (in text file format). Stored as transportRDD.

    val transportRDD = sc.textFile("hadoopSpark/dataset/Dataset_Transport").map(x =>{
         | val row = x.split(",")
         | (row.apply(0),row.apply(1).toInt)
         | });
         
    transportRDD.foreach(println);

![enter image description here](https://user-images.githubusercontent.com/29932053/32741304-cc8377c2-c873-11e7-90b7-905cbaf20473.png)

Using the RDD’s created above,

Map the holidaysRDD to only the 4th column with the 2nd and 3rd column (travel_mode, (src, dest)). This will be RDD modeRoute.

    val modeRoute = holidaysRDD.map( x => (x._4,(x._2,x._3)));

Then, join modeRoute with transportRDD 

     val route = modeRoute.join(transportRDD)
     
     route.foreach(println);
     
    
  ![enter image description here](https://user-images.githubusercontent.com/29932053/32741870-ad7266b6-c875-11e7-9e67-0fc719bb279f.png)

Now map the result such that you have the 1st column (src, dest and travel_mode) from holidaysRDD and the cost_per_unit from transportRDD

> src,dest --> x._2._1 
>travel_mode --> x._1 
>cost_per_unit --> x._2._2

    val routeMapped = route.map( x => ((x._2._1,x._1),x._2._2));
    
    route.foreach(println);
    
![enter image description here](https://user-images.githubusercontent.com/29932053/32742663-2332f8dc-c878-11e7-8b3d-68f05ec5b3e0.png)

Then, group the data by the key. Here the key is the (src, dest and travel_mode) as the tuple is ((src, dest and travel_mode), cost_per_unit)


Then, map the data such that we get the (src, dest and mode) and sum of the cost for every row that contains the key.

Then you sort the data by cost descending and get the 1st record.
Finally, display the result. . (mostRevenue)

    val mostRevenue = routeMapped.groupByKey().map( x => (x._1,x._2.sum)).sortBy( x => -x._2).first();

![enter image description here](https://user-images.githubusercontent.com/29932053/32743366-30c063fc-c87a-11e7-9bb5-60fb0dfb4591.png)


**What is the total amount spent by every user on air-travel per year?**



To find the total amount spent by every user on air-travel per year:

Using the holidaysRDD and transportRDD created above,

Map the holidaysRDD by grouping the data according to the 4th, 1st and 6th column (travel_mode, (user_id and year)). This is userYear.

    val userYear = holidaysRDD.map( x => (x._4,(x._1,x._6)));


Then, join userYear with transportRDD

    val userYearJoined = userYear.join(transportRDD);
    userYearJoined.foreach(println);
![enter image description here](https://user-images.githubusercontent.com/29932053/32745856-fe7c90ca-c881-11e7-8abc-e1649501b780.png)

 Then  map the result such that you have the 1st column (user_id and year) from holidaysRDD and the cost_per_unit from transportRDD. This is userYearExpense.
user_id and year --> x._2._1
cost_per_unit --> x._2._2

    val userYearExpense = userYearJoined.map( x => (x._2._1,x._2._2));
    userYearExpense.foreach(println);

![enter image description here](https://user-images.githubusercontent.com/29932053/32746361-608e7a52-c883-11e7-80cd-26be0e2a809e.png)

Then, group the data in userYearExpense by the key. Here the key is the (user_id, year) as the tuple is (user_id, year, cost_per_unit)

Then, map the data such that we get the sum of the cost for every row that contains the (user_id, year) (distinct).

 

Finally, display the result. (userTotExpense)

       val userTotExpense = userYearExpense.groupByKey().map( x => (x._1,x._2.sum)).sortByKey().foreach(println);
       
![enter image description here](https://user-images.githubusercontent.com/29932053/32746554-0d9da6dc-c884-11e7-8af1-0a416bc8e6d2.png)

**Considering age groups of < 20, 20-35, 35 >, which age group is travelling the most every year?**




To find which age group is travelling the most every year:

Create a Tupled RDD from the user data set (in text file format). Stored as userDetailsRDD.

    val userDetailsRDD = sc.textFile("hadoopSpark/dataset/Dataset_User_Details").map(x =>{val row = x.split(",")
     (row.apply(0),row.apply(1),row.apply(2).Int)});
![enter image description here](https://user-images.githubusercontent.com/29932053/32749998-35027b66-c88f-11e7-93d4-cfca15246112.png)

Using the holidaysRDD and userDetailsRDD created before,

Here we have to  create an Object named AGTravellingMost that holds the implementation:

Map the data in userDetailsRDD by grouping the data according to the 1st column(user_id) and 2nd column formed from checking the condition in the problem statement:
If user age < 20 column data is “<20” Else if user age > 35 column data is “35>”

Else column data is “20-35”

    val AgeGroup = userDetailsRDD.map(x => x._1 ->{
         | if( x._3 < 20) "<20"
         | else if (x._3 > 35) ">35"
         | else "20-35"
         | });

Now, filter holidaysRDD so that we get the (year, 1) for every user_id and join this to the above
 val AgeGroup = userDetailsRDD.map(x => x._1 ->{
         | if( x._3 < 20) "<20"
         | else if (x._3 > 35) ">35"
         | else "20-35"
         | });

This is AgeGroup.
Then, map the data in AgeGroup by new column to year and 1 for every row.

This data is then grouped by key. Here the key is the (new column, year) as the tuple is (new column, year, 1)

Then, map the data such that we get the sum of the 1’s for every row that contains the (new column, year).

Now, for every year, create a RDD and filter it by the year and sort it descending by 3rd column (sum of 1’s) and get the 1st record.


Using the holidaysRDD and userDetailsRDD created before,

Here I have created an Object named AGTravellingMost that holds the implementation:

Map the data in userDetailsRDD by grouping the data according to the 1st column(user_id) and 2nd column formed from checking the condition in the problem statement:
If user age < 20 column data is “<20” Else if user age > 35 column data is “35>”

Else column data is “20-35”

Now, filter holidaysRDD so that we get the (year, 1) for every user_id and join this to the above

This is AgeGroup.
Then, map the data in AgeGroup by new column to year and 1 for every row.

This data is then grouped by key. Here the key is the (new column, year) as the tuple is (new column, year, 1)

Then, map the data such that we get the sum of the 1’s for every row that contains the (new column, year).

Now, for every year, create a RDD and filter it by the year and sort it descending by 3rd column (sum of 1’s) and get the 1st record.



Using the holidaysRDD and userDetailsRDD created before,

Here I have created an Object named AGTravellingMost that holds the implementation:

Map the data in userDetailsRDD by grouping the data according to the 1st column(user_id) and 2nd column formed from checking the condition in the problem statement:
If user age < 20 column data is “<20” Else if user age > 35 column data is “35>”

Else column data is “20-35”

Now, filter holidaysRDD so that we get the (year, 1) for every user_id and join this to the above

This is AgeGroup.

Then, map the data in AgeGroup by new column to year and 1 for every row.

This data is then grouped by key. Here the key is the (new column, year) as the tuple is (new column, year, 1)

Then, map the data such that we get the sum of the 1’s for every row that contains the (new column, year).

Now, for every year, create a RDD and filter it by the year and sort it descending by 3rd column (sum of 1’s) and get the 1st record.




