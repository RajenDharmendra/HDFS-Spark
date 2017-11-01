<p><strong>Working with Local File System  HDFS and File Permissions</strong></p>

<p><strong>Problem Statement:</strong></p>

<p>Create a file max-temp.txt in local FS. <br>
Put some 10-15 records of date and temperature example:  <br>
dd-mm-yyyy,temperature <br>
Move this file to HDFS . <br>
Change the permission of the file /path/to/yourfile-in-HDFS/max-temp.txt, such that only the owner and the group members have full control over the file. Others do not have any control over it.</p>

<p>Code: </p>

<p>1) Create a file max-temp.txt in local FS and add data like: dd-mm-yyyy, temperature.</p>

<pre><code>cat &gt; max-temp.txt
</code></pre>

<p>2) Move the file to HDFS .</p>

<p><code>hadoop fs –copyFromLocal  max-temp.txt /path/to/HDFS-Location</code></p>

<p>Check if file has been successfully moved to the HDFS location.</p>

<pre><code>hdfs dfs –ls /path/to/hadoop
</code></pre>

<p>3)Change the permission of the file  /path/to/yourfile-in-HDFS/max-temp.txt.</p>

<p>Owner and Group have full control. Others have no control.</p>

<pre><code>hdfs dfs –chmod 770 /path/to/hadoop/max-temp.txt
</code></pre>

<p>Here 770 signifies:                                                     Execute(x) – 1, Write(w) – 2, Read(r) – 4.</p>



| Digit    | Title | Read   |Write|Execute|Control
| :------- | ----: | :---: |:---:|:---:|:---:|
| 7|Owner/User |  Yes  |Yes|Yes|Full
| 7   |Group|Yes   | Yes   |Yes|Full
| 0    |Others|No    |No  |No|No
