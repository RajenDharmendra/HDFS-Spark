**SCALA: Operations on List of Tuples**
-----------------------------------

**Task:**

Create a list of tuples, where the 1st element of the tuple is an int and the second element is a string.

> Example - ((1, ‘alpha’), (2, ‘beta’), (3, ‘gamma’), (4, ‘zeta’), (5,
> ‘omega’))

For the above list, print the numbers where the corresponding string length is 4

Find the average of all numbers, where the corresponding string contains alphabet ‘m’ or alphabet ‘z’


**Solution:**

Create a list of tuples, where the 1st element of the tuple is an int and the second element is a string:

    val tupleList = List((1,"alpha"),(2,"beta"),(3,"gamma"),(4,"zeta"),(5,"omega"))

In the above command, we use the List command to create the tuples
![enter image description here](https://user-images.githubusercontent.com/29932053/32454001-dec455ba-c2eb-11e7-98e8-9de4dc9823cf.png)


For the above list, print the numbers where the corresponding string length is 4

Count of the number of Strings with length 4

       tupleList.count(s => s._2.length == 4)
![enter image description here](https://user-images.githubusercontent.com/29932053/32454106-32be0fbc-c2ec-11e7-864f-2ff4fabf16fd.png)

For each string with length 4, print the 1st part of the tuple (i.e. number: int)


       tupleList.foreach(s => if(s._2.length == 4) println (s._1))

![enter image description here](https://user-images.githubusercontent.com/29932053/32454157-658c5d86-c2ec-11e7-8b64-eb21cd8c4e04.png)

For each string with length 4, print the 2nd part of the tuple (i.e. word:String)

     tupleList.foreach(s => if(s._2.length == 4) println (s._2))
    
![enter image description here](https://user-images.githubusercontent.com/29932053/32454267-b526ad06-c2ec-11e7-94f0-4f7318b2f689.png)

Find the average of all numbers, where the corresponding string contains alphabet ‘m’ or alphabet ‘z’

Filter the list by checking the 2nd part of the tuple(string part) if it contains either ‘m’ or ‘z’


    val filterList = tupleList.filter(s => s._2.contains("m")|| s._2.contains("z"))
![enter image description here](https://user-images.githubusercontent.com/29932053/32454770-3187aab6-c2ee-11e7-8295-00f2f031d0c2.png)

Using the filtered list, map all the elements to their numeral part (1st)

    val mapList = filterList.map(s => s._1)
![enter image description here](https://user-images.githubusercontent.com/29932053/32454858-6ea69a42-c2ee-11e7-9834-529ee1128e22.png)

Using the foldLeft command which sequentially adds all numbers starting from the left, we get the addition of the numeral part of the mapped list

> The foldleft method for a List takes two arguments; the start value
> and a function. This function also takes two arguments; the
> accumulated value and the current item in the list. So here's what
> happens:
> 
> At the start of execution, the start value that you passed as the
> first argument is given to your function as its first argument. As the
> function's second argument it is given the first item on the list
> 
>1.The function is then applied to its two arguments, in this case a
> simple addition, and returns the result.

>2. Fold then gives the function the previous return value as its first argument and the next item in the list as its second argument,
> and applies it, returning the result.

>3. This process repeats for each item of the list and returns the return value of the function once all items in the list have been iterated over.

    val sum = mapList.foldLeft(0)((x,y) => x + y)

![enter image description here](https://user-images.githubusercontent.com/29932053/32455191-7f0aebe4-c2ef-11e7-9985-5690b179ce0b.png)

Finding the count of numbers in the mapped list by finding the length of the list
	
    val count = mapList.length
![enter image description here](https://user-images.githubusercontent.com/29932053/32455270-bc13f8fa-c2ef-11e7-8a99-c604c8b60520.png)

Finding the average of the numbers in the mapped list using the sum and count variables

    val avg = sum / count
![enter image description here](https://user-images.githubusercontent.com/29932053/32455323-f07f0882-c2ef-11e7-837e-9f030acb1e8b.png)
The above can be written more efficiently as below:
 

       val filterMapList = tupleList.filter(s => s._2.contains("m")||     s._2.contains ("z")).map(s => s._1)
        
    val sum = filterMapList.foldLeft(0)((x,y) => x + y)
    
    val avg = sum / mapList.length
![enter image description here](https://user-images.githubusercontent.com/29932053/32455432-45be551e-c2f0-11e7-9c5f-58478ced4953.png)






