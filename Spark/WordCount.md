

## **Counting the number of rows and words in a text file using Spark RDD**

**Task:**



1. Write a program to read a text file and print the number of rows of data in the document.



2. Write a program to read a text file and print the number of words in the document.



3. We have a document where the word separator is -, instead of space. Write a spark code, to obtain the count of the total number of words present in the document.


Sample document: 

This-is-my-first-assignment.
It-will-count-the-number-of-lines-in-this-document. The-total-number-of-lines-is-3

**Solution:**
1) Write a program to read a text file and print the number of rows of data in the document.

**Code & Output:**

Reading the data from the text file created above:

    var sampleDataRDD = sc.textFile([path to text file], [no. of partitions])
    var sampleDataRDD = sc.textFile("/FileStore/tables/Textfile1.txt",2)
To find the count of the number of lines in the text file document: 

    var lineCount = sampleDataRDD.count
![enter image description here](https://user-images.githubusercontent.com/29932053/32667773-ad3a3142-c609-11e7-96e6-4c1e8e73ca0f.png)



2) Write a program to read a text file and print the number of words in the document.


**Code & Output:**

To solve the problem given above, 
create the RDD sampleRDD that holds the contents of the text file that we are to count the number of words from.

We  have to  split the data in the RDD sampleRDD based on (space). This will give an array of words. Stored in wordSplit

Just in case “.” or “,” is taken as a word, we  have to  replace those symbols in wordSplit with (nothing). I.e. we  have removed them from the RDD. Stored in wordClean

Then we  have to map all the elements of wordClean (which is an array of words) with 1 and displayed this.
we have used the count function and counted the number of words in the document.

Then finally the  reduce function could have also been used to sum the 1’s in the RDD to get the count.
The result is printed.

    val wordSplit = sampleDataRDD.flatMap(x => x.split(" "))
    val wordClean = wordSplit.map(x => x.replace(".","")).map(x => x.replace(",",""))
    val wordMap = wordSplit.map(x => (x,1))
    wordMap.foreach(println)
    val wordCount = wordMap.count
    println("Word Count = "+wordCount)

> Word Count = 251



3) We have a document where the word separator is -, instead of space. Write a spark code, to obtain the count of the total number of words present in the document.




Sample document:

This-is-my-first-assignment. It-will-count-the-number-of-lines-in-this-document. The-total-number-of-lines-is-3


**Code & Output:**

To solve the problem given above:

Create the RDD sampleRDD that holds the contents of the text file that we are to count the number of words from.
And display the data in sampleRDD.

Split the data in sampleRDD by the word separator (-). This gives a resultant RDD that is an array of all the words in the file.

Then replaced all the ‘.’ in the RDD with nothing. I.e. I removed all ‘.’ (Step can be omitted)

After that, mapped all the elements (array of words) with 1 and used the count function to count the words in the file.

The reduce function could have also been used to sum the 1’s in the RDD to get the count.
Finally the result is printed.

    var sampleDataRDD3 = sc.textFile("/FileStore/tables/Threeline_seperater-263f3.txt",2)
    val wordCount3 = sampleDataRDD3.flatMap(x => x.split("-")).map(x => x.replace(".","")).map(x => x.replace(",","")).map(x => (x,1)).count
    println("Word Count = "+wordCount3)


> Word Count = 23
