**What are kts files for?**
It's just a simple way that we have to see some Kotlin code and execute it without attaching it to an actual app.

**Basic Expressions**
* Semicolons are optional (usually not used. only if you have multiple expressions in the same line)
* You can create variables with the `var` keyword.
* Boolean conditions need parentheses (like other c-syntax based languages)
* `print` will send a message to the console. `printl` (print line) will send a message to the console and create a new line.
* Note: when we are using print to talk to the console, the console is not the phone. Android does not support printing without a new line!
  So when you do a print, it goes to a buffer until you do at least one `printl`.
* Variables, function and package names use camelCase.
* Constant names use UPPER_SNAKE_CASE.
* Data types use TitelCase. TitleCase also applies to Singleton variables and Objects.
* Conditionals are expressions. So you can call a function and put an `if` inside the function:
  `print(if (x<2) "less than 2" else "more or equals to 2")
  This is replacing the ternary that we are used to and that is not available in Kotlin.
* We don't have a `switch` in Kotlin, but we do have a `when`:
  ```
  when (price) {
      0 -> println("No price")
      1 -> {
          print("That's")
          print("cheap!")
      }
      in 2..10 -> println("Still cheap")
      11, 12, 13 -> println("Getting expensive")
      is String -> println("What's that?!")
      else -> println("Expensive!")
  ```
* We don't need a `break`, and `else` is what we know as `default`. 
* Loops: we have the standard `while` and `for-in`. If you want to use the old for(i = 0; i < 10; i++) you need to use a range, like: 
    ```
       for(i in 0..10) {
         println(i)
       }
    ```
    
**Variables and types**
* Kotlin is a statically typed language!
* It also inferes the type based on the assigned value. 
* Mutable variables use the keyword `var`.
* Immutable variables use the keyword `val` (value). We want to use `val` as much as possible:
  `val notMutableObject = String()`
* The difference between a constant an an immutable variable is: a constant needs to be defined at compilation time.
  When you are compiling the app, the value must be set. An immutable variable is a variable so it is a place in memory when you are executing the app.
  But once you set the value, it's immutable. 
* A constant is set with `const val`. 
* We don't have global constants so they must be part of a structure (such as an object or a class).
* We have structural equality:
  `if (price==aThirdPrice) {}`
  Executing the equals method of that objects to see if the contents are equal. -> "a"=="a" (true)
* We have referencial equality:
  `if (price===aThirdPrice) {}`
  "a"==="a" (false) because we have actually two objects. (Everytime we create a literal string it's a different object in memory).
* Core data types:
  ```
  val string: String
  val char: Char
  val integer: Int
  val double: Double
  val boolean: Boolean
  var what: Any // avoid Any as much as possible!
  ```
* We use double quotes for strings.
* We can use multi line strings by using tripple double quotes """blabla""". (Good for JSONs)
* On every string we can have a template expression:
  `val message = "The $price price is ${otherPrice * 1.1}"`

**Collections**
Here are some of them (not all):
* We use `List<Type>` and the the type of the collection inside the generic.
  In JavaScript we use `[]` and in Kotlin we use the `listOf` constructor.
  `val countries: List<String> = listOf("Argentina", "Brazil", "Canada")
* By default a list is immutable, so we can't add or remove items (unless you create a `mutableList`)
  `val cities: MutableList<String> = mutableListOf("Berlin", "London", "New York")`
  `cities.add("Paris")`
* We also have sets. A set is different from a list in that it can not contain duplicates:
  `val strings = hashSetOf("a", "b", "c", "3")` (will contain 3 elements, with no order)
* We also have a map, that is kind of a dictionary:
  `val readWriteMap = hashMapOf("foo" to 1, "bar" to 2)
* The SDK is written in Java, so when you talk to the Android SKD from Kotlin, sometimes you need to make some conversion.
  There is a way to convert a list into an Java array. It's a similar structure but not exactly the same. 
  `val javaArray = arrayOf(countries)`
  
**Null safety**
* For example a `String` must have a string value, but a `String?` accepts null
  `var nameWithNulls: String?`
* Not-null assertion operator: (!!)
  `val lengthForSure = myLastname!!.length`
  Here we are guaranteeing that `myLastname` has a value and is not `null`.
  (We try to avoid this operator usually)
* Safe calls:
  `val length = myLastname?.length`
* Default values (?:):
  `val lengthOrDefault = name?.count ?: 0` // This operator is called elvis operator
  
**Functions and Lambdas**
* If a function returns a value we have to add it:
  ```
  fun aFunctionReturning(): String {
    return "I'm a function"
  }
  ```
* If a functions is a one-linder it can be written like:
  `fun aFunctionReturning2(): String = "I'm a function"` (The `=` is kind of an implicit return)
* Even if your function argument has a default value you must declare a type:
  ```
  fun sum(a: Int, b: Int = 4): Int {
     return a+b
  }
  ```
* You can add `Extension functions`. It's a way that we have to inject new functions to any object or any type (even if you don't own the type).
  Let's add an `isEven` method to `integers`:
  ```
  fun Int.isEven() : Boolean {
    return this % 2 == 0
  }
  ```
  Now we can do `4.isEven()`
  This is heavily used in Kotlin!
* Lambda functions is a name that we use here for literal functions or anonymous functions. 
* A Lambda functions is a data type. The data type is "parantheses, thin arrow, return type":
  `val lambda: (Int, Int) -> Int`
* If no return type is expected we use `Unit`:
  `val myFunction: () -> Unit = {}` (You assign a value with a code block)
* If your lambda function has exactly one argument, you can reference it inside the finction with `it`.
* You can rename the arguments inside the code block followed by a thin arrow:
  ```
  val myFunction: (Int, Boolean) -> Unit = { int, isReady ->
     print(int)
  }
  ```
* If you have more than one argument you are forced to name them.
* Is Kotlin functional programming? You can kind of use some of the FP ideas, but when we are compiling for android, we are compiling for 
  a VM compatible with Java and in Java everything is a class.
  So, in android you can't create a global variable. Everything must be in some sort of class or structure.  



  
