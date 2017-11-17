**Twitter Tweet Analysis**
----------------------

**Sentiment​​ analysis ​​on ​​demonetization**
Let us find out the views of different people on the demonetization by analysing the tweets from twitter. Here is the dataset where twitter tweets are gathered in CSV format. You can download the​​ dataset ​​from the ​​below​​link

***Note:*** Use the dataset given below:

Data in -> HDFS-Spark/Spark/Dataset/AFFIN of this github

for  tweets.csv  use below  link
https://drive.google.comopen?id=0ByJLBTmJojjzNkRsZWJiY1VGc28

**AFINN.txt**

Below is a sample (15 rows) of the data. It has 2 columns: word, rating

![enter image description here](https://user-images.githubusercontent.com/29932053/32962178-7e9ecd76-cb99-11e7-9e05-76174b500d6e.png)

**demonetization-tweets.csv**

Below is a sample of the data. It has 15 columns:
![enter image description here](https://user-images.githubusercontent.com/29932053/32962270-d5d90d22-cb99-11e7-8bf7-ea40fedd96ea.png)

id,text, favorited, favoriteCount, replyToSN, created, truncated, replyToSID, id, replyToUID, statusSource, screenName, retweetCount, isRetweet, retweeted

**Solution:**



We begin by reading the data file demonetization-tweets.csv as a text file from the local FS using the spark context object sc.

    //reading "demonetization-tweets.csv" file
    val tweets = spark.sparkContext.textFile("path/to/file/demonetization-tweets.csv");
    tweets.foreach(x => println(x));
![enter image description here](https://user-images.githubusercontent.com/29932053/32962484-a707c8ac-cb9a-11e7-84ae-aff8d3005711.png)

 filtering data with ',' as delimiter and resulting fields having length >= 2

      val filtertweets = tweets.map(x => x.split(",")).filter(x=>x.length>=2);

      filtertweets.foreach(x => x.foreach(println));

![enter image description here](https://user-images.githubusercontent.com/29932053/32962654-3b4692c8-cb9b-11e7-80a8-ddefe278de73.png)

replacing " with blank space inside field 0 and field 1 and changing all characters of field 1 to lower case of field 2 and furthermore, creating tuple having two fields

    val replacefiltertweets = filtertweets.map(x => (x(0).replaceAll("\"",""),x(1).replaceAll("\"","").toLowerCase));
    
      replacefiltertweets.foreach(x => println(x._1 + "------"+ x._2));
   
   ![enter image description here](https://user-images.githubusercontent.com/29932053/32962792-c5cdfac6-cb9b-11e7-888b-21c2f61470d0.png)

  tuple of two elements is created, where second element of tuple contains words separated by space

      val tupletweets = replacefiltertweets.map(x => (x._1,x._2.split(" "))).toDF("id","words");
  tupletweets -->> sql.DataFrame
		

      tupletweets.show(12000);
   ![enter image description here](https://user-images.githubusercontent.com/29932053/32962907-33fc6f00-cb9c-11e7-8912-24817359b85b.png)


temporary view is created from tupletweets dataframe

      tupletweets.registerTempTable("tweets");

 

     sqlContext.sql("select * from tweets").show(12000);
     
![enter image description here](https://user-images.githubusercontent.com/29932053/32963078-ce1d5d2e-cb9c-11e7-95c9-aedcd514116b.png)

explode function places each word on separate line along with id

    val explode = spark.sql("select id as id,explode(words) as word from tweets");
    
     explode.registerTempTable("tweet_word");

       sqlContext.sql("select * from tweet_word").show(120000); 

![enter image description here](https://user-images.githubusercontent.com/29932053/32963263-6f92e1ce-cb9d-11e7-92d2-b00b29760635.png)


reading AFINN-111.txt where elements of each row are separated by ', ' and thereby converting rdd to dataframe

      val afinn = sc.textFile("path/to/your/file/AFINN.txt").map(x => x.split("\t")).map(x => (x(0),x(1))).toDF("word","rating");


     afinn.show(2500);
![enter image description here](https://user-images.githubusercontent.com/29932053/32964816-6af07154-cba2-11e7-8c4a-12431d9052b6.png)

     afinn.registerTempTable("afinn");
     sqlContext.sql("select * from afinn").show(2500);

![enter image description here](https://user-images.githubusercontent.com/29932053/32964816-6af07154-cba2-11e7-8c4a-12431d9052b6.png)



afinn and tweet_word views are joined on the basis of common "words" field and for each id, average of rating is found to analyse sentiment on demonetization 

      val join = sqlContext.sql("select t.id,AVG(a.rating) as rating from tweet_word t join afinn a on t.word=a.word group by t.id order by rating desc");

      join.show(2500);
![enter image description here](https://user-images.githubusercontent.com/29932053/32966047-92a6ff16-cba6-11e7-936d-3719dda71bb1.JPG)

