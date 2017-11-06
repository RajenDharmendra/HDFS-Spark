**Big Data Mini Project on USA Crime Analysis**
-------------------------------------------

**Task:**


Write a MapReduce/Pig program to calculate the number of cases investigated under each FBI code

Write a MapReduce/Pig program to calculate the number of cases investigated under Ward 32

Write a MapReduce/Pig program to calculate the number of arrests in theft district wise

Write a MapReduce/Pig program to calculate the number of arrests done between October 2014 and October 2015


**Dataset:**


Introduction:

This dataset contains attributes related to crimes taking place in various areas like type of crime, FBI code related to that criminal case, arrest frequency, location of crime etc.


Google Drive Link: 

https://drive.google.com/file/d/0B1QaXx7tpw3SaUJHOHBZclBXWG8/view?usp=sharing


Dataset Description:

The columns present in the dataset:

ID, Case Number, Date, Block, IUCR, Primary Type, Description, Location Description, Arrest, Domestic, Beat, District, Ward, Community Area, FBICode, X Coordinate, Y Coordinate, Year, Updated On, Latitude, Longitude, Location

![enter image description here](https://user-images.githubusercontent.com/29932053/32462135-5295e12e-c306-11e7-84f6-ef6c34a20c7d.png)


![enter image description here](https://user-images.githubusercontent.com/29932053/32462187-84fdc834-c306-11e7-80c7-42547e6ffcc4.png)


**Solution:**
Pig program to calculate the number of cases investigated under each FBI code

Start the pig terminal in local mode
Loading the dataset into the relation crimeDataset using the Pig CSV parser and specifying that the data is comma separated, not multiline, should be in Unix format and to skip the header

In this load statement, we haven’t specified the datatype of each column, therefore the default type of all columns of the dataset is bytearray

