**Travel Data Exercise using Scala Part 1**
---------------------------------------
**Task:**



1) What is the distribution of the total number of air-travelers per year?



2) What is the total air distance covered by each user per year?



3) Which user has travelled the largest distance till date?



4) What is the most preferred destination for all users?

**Dataset:**




*S18_Dataset_Holidays:*

The dataset is of holiday details of travelers with columns: user_id, src, dest, travel_mode, distance, year_of_travel:
![enter image description here](https://user-images.githubusercontent.com/29932053/32736111-a767d1fe-c864-11e7-9921-5a27dc3109d8.png)

*S18_Dataset_Transport:*

The dataset is of transport details with columns: travel_mode, cost_per_unit:

![enter image description here](https://user-images.githubusercontent.com/29932053/32736233-ea932cb2-c864-11e7-9fb8-3c0efb0979d4.png)

*S18_Dataset_User_details:*

The dataset is of user details of travelers with columns: user_id, name,age:
![enter image description here](https://user-images.githubusercontent.com/29932053/32736321-23d55180-c865-11e7-8f66-35df42b47680.png)


**Solution:**

**What is the distribution of the total number of air-travelers per year?**



To find the total number of air travelers per year:

Create a Tupled RDD from the data set (in text file format). Stored as holidaysRDD.

    val holidaysRDD = sc.textFile("hadoopSpark/dataset/Dataset_Holidays").map(x =>
	     {
         | val row = x.split(",")
         | (row.apply(0).toInt,row.apply(1),row.apply(2),row.apply(3),row.apply(4).toInt,row.apply(5).toInt)
         | });
![enter image description here](https://user-images.githubusercontent.com/29932053/32737533-5380b25a-c868-11e7-8af7-d5b25113a37f.png)


Using the RDD created above,

Map the RDD to only the 1st and 6th column (user_id and year) and retrieve only the distinct rows (no duplicates).

Then, map the data by (year, 1). This will give us a count for every year.

Then, group the data by the key. Here the key is the year as the tuple is (year, 1)

Then, map the data such that we get the year and sum of the 1’s for every row that contains the year.
Finally, display the result. . (totAirTravellers)

    val totAirTravellers = holidaysRDD.map(x => (x._1,x._6)).distinct.map(x => (x._2,1)).groupBYKey.map( x => (x._1,x._2.sum));
    
    totAirTravellers.foreach(println);
    
![enter image description here](https://user-images.githubusercontent.com/29932053/32738002-99bdc3b0-c869-11e7-94a8-cf6221a25f4a.png)



**What is the total air distance covered by each user per year?**



To find the total air distance covered by each user per year:

Create a Tupled RDD from the data set (in text file format). Stored as holidaysRDD.

Using the RDD created above,

Map the RDD by grouping the data according to the 1st, 6th and 5th column (user_id, year and distance).

Then, group the data by the key. Here the key is the (user_id, year) as the tuple is (user_id, year, distance)

Then, map the data such that we get the sum of the distance for every row that contains the year (distinct).

For better readability, I have used the sortByKey() that sorts the data according to the key (user_id, year)



Finally, display the result. (totAirDistance)

    val totAirDistance = holidaysRDD.map( x => ((x._1,x._6),x._5)).groupByKey().map( x=> (x._1,x._2.sum)).sortByKey();
    
     totAirDistance.foreach(println);
![enter image description here](https://user-images.githubusercontent.com/29932053/32738753-e6635af2-c86b-11e7-8790-392c6e93f15c.png)


**Which user has travelled the largest distance till date?**




To find the user who has travelled the largest distance till date:


The base RDD to be worked for this problem is the resultant RDD of the previous problem i.e. totAirDistance

Using the RDD *totAirDistance*,

Map the RDD to only the 1st part of the 1st column and 2th column (user_id and distance) and retrieve only the distinct rows (no duplicates).

Then, group the data by the key. Here the key is the user_id as the tuple is (user_id and distance)

Then, map the data such that we get the user_id and sum of the distance for every row corresponding to the user_id.

Then, Sort the data by the distance in descending order so as to get the greatest distance first.

Here, two users have traveled the greatest distance, therefore we use the take(2) to get the 1st 2 rows only.
Finally, display the result. (userTravelledMost)



    val userTravelledMost = totAirDistance.map(x => (x._1._1,x._2)).groupByKey.map(x =>(x._1,x._2.sum)).sortBy(x => -x._2).take(3);
    
     userTravelledMost.foreach(println);
![enter image description here](https://user-images.githubusercontent.com/29932053/32739468-fa68ce36-c86d-11e7-8191-af52338c2aec.png)

**What is the most preferred destination.?**



To find the most preferred destination:

Create a Tupled RDD from the data set (in text file format). Stored as holidaysRDD.



Using the RDD created above,

Map the RDD by grouping the data according to the 3rd column (dest) with 1 for each row.

Then, group the data by the key. Here the key is the (dest) as the tuple is (dest, 1)

Then, map the data such that we get the sum of the 1’s for every corresponding to dest.

Finally, we sort the above resultant RDD (mostPreferredDest) by the sum of 1’s descending and retrieve the first row.

This gives us the most preferred destination.

	    val mostPreferredDest = holidaysRDD.map( x => (x._3,1)).groupByKey().map( x => (x._1,x._2.sum)).sortBy( x => -x._2).first();
![enter image description here](https://user-images.githubusercontent.com/29932053/32739867-17f94dda-c86f-11e7-8046-733d7c0ac0ee.png)


