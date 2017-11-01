**Checking for/Creating Directories in the HDFS.**
--------------------------------------------------

Appending content to file in HDFS.

**Task 1:**
Check whether /user/mydir/hadoop directory exists or not in the HDFS.
If it doesn't exist, then create this.
Create a directory /user/mydir/hadoop.

**Task 2:**
Create a file in HDFS under directory /user/mydir/hadoop, with name word-count.txt.
Whatever we type on screen should get appended to the file.
Try to type (on screen) few lines from any online article or textbook.

**Solution:**
**Task 1:**
*Code:* 

1) To check if /user/mydir/hadoop exists in HDFS run the following script 

                if $(hadoop fs –test –d /user/mydir/hadoop);
                then
                  echo –e “\nDIRECTORY EXISTS\n”;
                else
                  echo –e “\nDIRECTORY DOES NOT EXIST\n”;
                fi



2) Create directory /user/mydir/hadoop

          hadoop fs –mkdir /user/mydir/hadoop

Note : After creating dir run the above script to check if dir exists or you can run 

          hadoop fs -ls /user/mydir/hadoop


**Task 2**:

*Code:* 

1)  To Create a file in HDFS under directory /user/mydir/hadoop, with name word-count.txt.

          hdfs dfs –touchz /user/mydir/hadoop/word-count.txt 

2) Whatever we type on screen should get appended to the file. 
This can be achieved in two ways 
*Method -- 1*
 We have to create a empty file in  local FS
	

 Create and add data to a file ‘wcount.txt’ in the local FS
	 

    cat > wcount.txt
Append the data in ‘wcount.txt’ in local FS to word-count.txt in the HDFS
	

    hadoop fs –appendToFile wcount.txt /user/mydir/hadoop/word-count.txt

*Method --2*
 Append Data to file by On-Screen by typing in the command line directly to HDFS file 

    hadoop fs –appendToFile - /user/mydir/hadoop/word-count.txt

Display the contents of the file using:
    

     hadoop fs –cat /user/mydir/hadoop/word-count.txt


