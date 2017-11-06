**SCALA: Operations on String List**
--------------------------------
**Task:**

Given a list of strings - List[String] (“alpha”, “gamma”, “omega”, “zeta”, “beta”)

Find count of all strings with length 4

Convert the list of string to a list of integers, where each string is mapped to its corresponding length

Find count of all strings which contain alphabet ‘m’

Find the count of all strings which start with the alphabet ‘a’

**Solution:**

Create a list of strings:

    val strList = List[String]("alpha","gamma","omega","zeta","beta")



Find count of all strings with length 4
   

   strList.count(s => s.length == 4)


Convert the list of string to a list of integers, where each string is mapped to its corresponding length

       val mapList = strList.map(s => s.length)



Find count of all strings which contain alphabet ‘m’
 

      val countCStrM = strList.count(s => s.contains("m"))


Find count of all strings which start with the alphabet ‘a’

       val countAStr = strList.count(s => s.charAt(0).equals('a'))
       
       
![Output](https://user-images.githubusercontent.com/29932053/32453501-5c6ad18a-c2ea-11e7-9a2d-8905769fe361.png)




