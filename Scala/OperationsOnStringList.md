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

https://user-images.githubusercontent.com/29932053/32453201-a3a699d6-c2e9-11e7-873a-8a7e9fc1e430.png


