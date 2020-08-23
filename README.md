# HDFS--SPARK
Complete HDFS and SPark
HDFS Commands
1. version
Command Usage
version
Command Example
hdfs dfs version
Description
Prints the Hadoop version
2. mkdir
Command Usage
mkdir <path>
Command Example
hdfs dfs -mkdir /user/dataflair/dir1
Description
Takes path URI’s as an argument and creates directories.
Creates any parent directories in path that are missing (e.g., mkdir -p in Linux).
Learn various features of Hadoop HDFS from this HDFS features guide.
3. ls
Command Usage
ls <path>
Command Example
hdfs dfs -ls /user/dataflair/dir1
Description
It displays a list of the contents of a directory specified by path provided by the user, showing the names, permissions, owner, size and modification date for each entry.
4. put
Command Usage
put <localSrc> <dest>

Command Example
hdfs dfs -put /home/dataflair/Desktop/sample /user/dataflair/dir1
Description
Copies the file or directory from the local file system to the destination within the DFS.
Learn Internals of HDFS Data Write Pipeline and File write execution flow.
5. copyFromLocal
Command Usage
copyFromLocal <localSrc> <dest>
Command Example
hdfs dfs -copyFromLocal /home/dataflair/Desktop/sample /user/dataflair/dir1
Description
Similar to put command, but the source is restricted to a local file reference.
Learn Internals of HDFS Data Read Operation, How Data flows in HDFS while reading the file.
6. get
Command Usage
get [-crc] <src> <localDest>
Command Example
hdfs dfs -get /user/dataflair/dir2/sample /home/dataflair/Desktop

Description
Copies the file or directory in HDFS identified by the source to the local file system path identified by local destination.

7. copyToLocal
Command Usage
copyToLocal <src> <localDest>
Command Example
hdfs dfs -copyToLocal /user/dataflair/dir1/sample /home/dataflair/Desktop
Description
Similar to get command, only the difference is that in this the destination is restricted to a local file reference.
8. cat
Command Usage
cat <file-name>
Command Example
hdfs dfs -cat /user/dataflair/dir1/sample
Description
Displays the contents of the filename on console or stdout.
9. mv
Command Usage
mv <src> <dest>
Command Example
hadoop fs -mv /user/dataflair/dir1/purchases.txt /user/dataflair/dir2
Description
Moves the file or directory indicated by the source to destination, within HDFS.
10. cp
Command Usage
cp <src> <dest>
Command Example
hadoop fs -cp /user/dataflair/dir2/purchases.txt /user/dataflair/dir1
Description
Copies the file or directory identified by the source to destination, within HDFS.


11. moveFromLocal
Command Usage

moveFromLocal <localSrc> <dest>
Command Example
hdfs dfs -moveFromLocal /home/dataflair/Desktop/sample /user/dataflair/dir1
Description
Copies the file or directory from the local file system identified by the local source to destination within HDFS, and then deletes the local copy on success.

12. moveToLocal
Command Usage

moveToLocal <src> <localDest>
Command Example
hdfs dfs -moveToLocal /user/dataflair/dir2/sample /user/dataflair/Desktop
Description
Works like -get, but deletes the HDFS copy on success.

13. tail
Command Usage

hdfs dfs -tail [-f] <filename>

Command Example
"hdfs dfs -tail /user/dataflair/dir2/purchases.txt
hdfs dfs -tail -f /user/dataflair/dir2/purchases.txt"
Description
Shows the last 1KB of the file on console or stdout.

14. rm
Command Usage

rm <path>
Command Example
hdfs dfs -rm /user/dataflair/dir2/sample

Description
Removes the file or empty directory present on the path provided by the user.

Command Example
hdfs dfs -rm -r /user/dataflair/dir2
Description
Recursive version of delete.

15. expunge
Command Usage

hdfs dfs -expunge
Command Example
hdfs dfs -expunge
Description
Used to empty the trash.

16. du
Command Usage
du <path>
Command Example
hdfs dfs -du /user/dataflair/dir1/sample
Description
Shows disk usage, in bytes, for all the files present on the path provided by the user; reporting of filenames are done with the full HDFS protocol prefix.

Command Example
hdfs dfs -du -s /user/dataflair/dir1/sample
Description
Like -du, but it prints a summary of the amount of disk usage of all files/directories in the path.

17. df
Command Usage

hdfs dfs -df [-h] URI [URI ...]
Command Example
hdfs dfs -df -h
Description
Displays free space.


18. touchz
Command Usage

touchz <path>
Command Example
hdfs dfs -touchz /user/dataflair/dir2
Description
It creates a file at the path containing the current time as a timestamp. Fails if a file already exists at a path, unless the file is already size 0.

19. test
Command Usage

hdfs dfs -test -[ezd] URI
Command Example
"hdfs dfs -test -e sample
hdfs dfs -test -z sample
hdfs dfs -test -d sample"
Description
The Hadoop test is used for file test operations.
It gives 1 output if a path exists; it has zero length, or it is a directory or otherwise 0.
Options:
-d: if the path given by the user is a directory, then it gives 0 output.
-e: if the path given by the user exists, then it gives 0 output.
-f: if the path given by the user is a file, then it gives 0 output.
-s: if the path given by the user is not empty, then it gives 0 output.
-z: if the file is zero length, then it gives 0 output.

20. text
Command Usage

hdfs dfs -text <source>

Command Example
hdfs dfs -text /user/dataflair/dir1/sample
Description
Takes a source file and outputs the file in text format. The allowed formats are zip and TextRecordInputStream.

21. appendToFile
Command Usage

hadoop fs -appendToFile <localsource> ... <dst>
Command Example
hadoop fs -appendToFile /home/dataflair/Desktop/sample /user/dataflair/dir1
Description
Append single sources or multiple sources from local file system to the file system at the destination. It also reads input from standard input and adds it to destination file system.

22. count
Command Usage

hdfs dfs -count [-q] <paths>
Command Example
hdfs dfs -count /user/dataflair
Description
Counts the number of directories, number of files present and bytes under the paths that match the specified file pattern.


23. find
Command Usage

hadoop fs -find <path> ... <expression> ...
Command Example
hadoop fs -find /user/dataflair/dir1/ -name sample -print

Description
Finds all files that match the specified expression and performs all the actions to them which are selected. If no path is specified then defaults to the present working directory. If none of the expression is specified then defaults to -print.


24. usage
Command Usage

hadoop fs -usage command
Command Example
hadoop fs -usage mkdir
Description
Return the help for an individual command.

