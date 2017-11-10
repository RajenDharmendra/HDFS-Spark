## **Analysis of a college student data-set using Spark RDD**



**Task1:**

1. Read the text file, and create a tupled rdd.

2. Find the count of total number of rows present.

3. What is the distinct number of subjects present in the entire school?

4. What is the count of the number of students in the school, whose name is Mathew and marks is 55?





**Task 2:**

1. What is the count of students per grade in the school?

2. Find the average of each student (Note - Mathew is grade-1, is different from Mathew in some other grade!)

3. What is the average score of students in each subject across all grades?

4. What is the average score of students in each subject per grade?

5. For all students in grade-2, how many have average score greater than 50?





**Task 3:**

Are there any students in the college that satisfy the below criteria:

1. Average score per student_name across all grades is same as average score per student_name per grade

Hint - Use Intersection Property.



**Dataset:**

The dataset is of college student’s details in the form of a text file (name, subject, grade, marks):

https://github.com/RajenDharmendra/HDFS-Spark/blob/master/Spark/Dataset/StudentDataset

![enter image description here](https://user-images.githubusercontent.com/29932053/32673207-39b26894-c61c-11e7-98e3-9547227a766d.png)

**Solution 1:**


1. Read the text file, and create a tupled rdd.

	    val collegeRDD = sc.textFile("hadoopSpark/dataset/studentDataset.txt");

		collegeRDD.foreach(println);
![enter image description here](https://user-images.githubusercontent.com/29932053/32673299-a8d74d16-c61c-11e7-95b4-282f79da448d.png)

     val tupledRDD = collegeRDD.map(x => {
         | val row = x.split(",").toList
         | (row.apply(0),row.apply(1),row.apply(2),row.apply(3).toInt,row.apply(4).toInt)});

    tupledRDD.foreach(println);
![enter image description here](https://user-images.githubusercontent.com/29932053/32673299-a8d74d16-c61c-11e7-95b4-282f79da448d.png)


2. Find the count of total number of rows present.

	     val rowCount = tupledRDD.count;
3. What is the distinct number of subjects present in the entire school?
	
Map the data to only the subject column and retrieve only the distinct rows of data (No duplicate subject names)
Using the result from the above action, count the number of rows

    val subjectCount = tupledRDD.map(x => x._2).distinct.count;

> subjectCount: Long = 3

4. What is the count of the number of students in the school, whose name is Mathew and marks is 55?

Map the data to only the student_name, subject and marks columns

Filter the above result by rows where the student_name == “Mathew” and marks == 55

Using the collect command we can see the result tuple

And using the count command we get the count of the result i.e. the count of the number of students in the school whose name is Mathew and marks is 55.

     val studentMathew = tupledRDD.map( x => (x._1,x._2,x._4)).filter(x => x._1=="Mathew" && x._3==55).collect;

> studentMathew: Array[(String, String, Int)] =
> Array((Mathew,history,55), (Mathew,science,55))

    val studentMathewCount = tupledRDD.map( x => (x._1,x._2,x._4)).filter(x => x._1=="Mathew" && x._3==55).count;

> studentMathewCount: Long = 2

**Task 2:**



1. What is the count of students per grade in the school?

To find the count of students, the following steps have been used:

Select only the grade and student_name

Using the result we get from above, Select only distinct rows of data (No duplicates) Using the result from above, map all the grades with a 1

Using the result from above, reduce the data by key (grade) by summing the 1’s for each key

This gives use the number of students for each grade.

     val countOfStudentPerGrade = tupledRDD.map(x => (x._3,x._1)).distinct.map(x => (x._1,1)).reduceByKey((x,y)=> x+y);

> countOfStudentPerGrade: org.apache.spark.rdd.RDD[(String, Int)] =
> ShuffledRDD[24] at reduceByKey at <console>:31

     countOfStudentPerGrade.foreach(println)

> (grade-2,5) 
> 
> (grade-3,3)
> 
>  (grade-1,4)



2. Find the average of each student (Note - Mathew is grade-1, is different from Mathew in some other grade!)
To find the average each of student of each grade, the following steps have been used:

Select only the grade & student_name as one column and the addition of both the marks as one column

Using the result we get from above, group the data by the key (grade and student_name) Using the result from above, map the 1st column and the result of the following as the 2nd column:

Sum of the key-grouped marks divided by the count of the marks columns (2 columns)

This gives use the average of each student in each grade.


    val AVGStudent = tupledRDD.map(x => ((x._3,x._1),(x._4 + x._5))).groupByKey.map(x => (x._1,(x._2.sum.toDouble)/(x._2.size*2)));

    AVGStudent.foreach(println)


![enter image description here](https://user-images.githubusercontent.com/29932053/32676294-1d086f7a-c628-11e7-8196-8c80913b1a80.png)




