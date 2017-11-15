**Working with Parquet File Format using Spark-SQL**
------------------------------------------------
**Task:**

Create a dataframe with 1 to 100 and save as parquet file.

**solution:**

Create a dataframe with 1 to 100 and save as parquet file.



Create an RDD to hold numbers from 1 to 100 by using the spark context object sc and the parallelize method. pdataRDD

    scala> val pDataRDD = sc.parallelize(1 to 100);

Create the DataFrame pDataDF from the RDD pdataRDD by using the function toDF().

    scala> val pDataDF = pDataRDD.toDF();
    scala> pDataDF.show();
    
![enter image description here](https://user-images.githubusercontent.com/29932053/32854628-374397b4-ca0d-11e7-8539-5fb8a8cc232b.png)

To save the data as a parquet file, use the write method with data type: parquet and filename

as parameter: [filename].parquet

**scala> pDataDF.write.parquet("datafile.parquet");**






