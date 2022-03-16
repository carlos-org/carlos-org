---
layout: page
title: UD1 Kotlin Basics
permalink: /ud1/
---


Why Kotlin? {#why}
===================


Kotlin is a modern programming language than make developers happier…

![Kotlin's uses](../assets/images/Imagen1.png)  
  
[https://kotlinlang.org/](https://kotlinlang.org/)

Packets {#packets}
==================

____________________________

As in Java, in Kotlin the packages are placed at the top of the file:
```kotlin
    package com.example
    
    import com.example.*

    //...
```

  
  

Variable declaration {#variable}
==================================


Kotlin uses two different keywords to declare variables: val and var

*   Use val for a variable whose value never changes. You can't reassign a value to a variable that was declared using val.
*   Use var for a variable whose value can change.

In the example below, count is a variable of type Int that is assigned an initial value of 10:

```kotlin
var count: Int = 10
```
    

Int is a type that represents an integer, one of the many numerical types that can be represented in Kotlin. Similar to other languages, you can also use Byte, Short, Long, Float, and Double depending on your numerical data.

The var keyword means that you can reassign values to count as needed. For example, you can change the value of count from 10 to 15:

```kotlin
var count: Int = 10
count = 15
```

Some values are not meant to be changed, though. Consider a String called languageName. If you want to ensure that languageName always holds a value of "Kotlin", then you can declare languageName using the val keyword:

```kotlin
val languageName: String = "Kotlin"
```
   

These keywords allow you to be explicit about what can be changed. Use them to your advantage as needed. If a variable reference must be reassignable, then declare it as a var. Otherwise, use val.

## Type Inference


\Begin{multicols}{2}
Continuing the previous example, when you assign an initial value to **languageName**, the Kotlin compiler can infer the type based on the type of the assigned value.  

Since the value of "Kotlin" is of type String, the compiler infers that **languageName** is also a String. Note that Kotlin is a statically-typed language. This means that the type is resolved at compile time and never changes.  
In the following example, **languageName** is inferred as a String, so you can't call any functions that aren't part of the String class:

\End{multicols}


```kotlin 
val languageName = "Kotlin"
val upperCaseName = languageName.toUpperCase()
```
:::warning
Error 
```kotlin 
// Fails to compile
languageName.inc()
```
:::
      

**toUpperCase()** is a function that can only be called on variables of type *String*. Because the Kotlin compiler has inferred languageName as a String, you can safely call toUpperCase().inc(), however, is an Int operator function, so it can’t be called on a String. Kotlin’s approach to type inference gives you both conciseness and type-safety.

## Know values in compilation time

They can only be declared at the top level (outside of a class) or as a member of a class

They can only be declared with primitive or String values

```kotlin 
package com.cartatar.testkotlin

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle

const val PI=3.1416
```


## Null Safety



  
One of the most common mistakes in programming is the hated **NullPointerException**. It occurs when the programmer forgets to initialize an object or it has not been able to do so due to some kind of problem. To try to minimize the appearance of this type of error, Kotlin incorporates a series of measures, which we will discuss in this section.

Kotlin supports null safetly natively, giving us the ability to define whether a variable can be null or not. In this way, the compiler can catch potential NullPointerException errors at compile time, reducing the possibility of error in run time. By default, all variables in Kotlin are non-nullable. Thus, if we try to assign a null value to any variable, the compiler will throw an error:

```kotlin 
var saludo: String = "Hola"
saludo = null // Compiling Error
```
    
```kotlin 
var saludoNullable: String? = "Hello"
saludoNullable = null //Compiles
```


As shown in the example above, if we want to allow a variable to be null, we will have to define it by adding **?** to your data type.

Since **NullPointerException** errors occur when we try to access a method or property of a null variable, Kotlin will avoid such errors by denying the call to the methods or access to the affected properties. However, sometimes, it will be necessary to use these methods or properties. For this, we have several alternatives.


## Adding a preflight



We can check if a variable is null before performing an operation, just as we did in Java.

```kotlin 
val nameNullable: String? = "Juan"
if (nobreNullable != null) {
    println("Hola, ${nameNullable.toUpperCase()}.")
    println("Tu name tiene ${nameNullable.length} caracteres.")
} else {
    println("Hola, invitado")
}
```
 

If we try to access a nullable variable without this check, a compilation error occurs.

```kotlin 
val s: String? = "Hola"
print(s.length) // Compile Error 
```
  

For it to compile we have to use the code below.

```kotlin 
if (s != null) {
    print(s.length) // compiles
} 
```

Secure call with "**?**" operator.

  
  

The above check is simple, but requires a lot of code to do it. Kotlin offers us the ? operator in order to reduce that code, so that we can have the null check and the method call on the same line. Thus, the following code:

```kotlin 
name?.toUpperCase()

//it is equivalent to
    
if (name != null)
        name.toUpperCase()
else null
```


This operator will allow us multiple simultaneous checks:

```kotlin 
val currentCity: String? = user?.address?.ciudad 
```

In the example, *currentCity* will be null if user, address or *ciudad* are null.

## Elvis operator: "?:"



The Elvis operator ?: is used to offer a default value when the variable to be validated contains null. For example, the following code:

```kotlin 
name ?: "unknown"

//Equivalent code 

if (name != null) name
else "unknown"
```
 

One of the most common uses of this operator is to return a default value distinct to null when a method or property is null :

```kotlin 
val nameMayuscula = name?.toUpperCase() ?: "DESCONOCIDO"
val ciudadActual = usuario?.direccion?.ciudad ?: "desconocida"
```

The complexity of the expressions to the left of the Elvis operator can be even greater, as shown in the second example.

Also, we can use the right part of the operator to throw an exception or return a value. This will be very useful to us when we evaluate the preconditions of a function.

```kotlin 
val n = nameNullable ?: throw IllegalArgumentException("Name is null") 
```
 

If, in addition to returning a value, we need to execute multiple operations to the right of the elvis operator, it will be necessary to include them in a block of type run.

```kotlin 
val a = b ?: run {
        val valorSiBEsNull = c.obtenValor()
        almacena(valorSiBEsNull)
        valorSiBEsNull //This is the return value
    } 
```
  

## The "!!" operator



Through the !! operator. We can skip the null check of a variable, so that an exception of the type NullPointerException will be thrown if the variable is null.

```kotlin 
val name: String? = null
name!!.toUpperCase() // NullPointerException
```

  

It is used on those occasions when we as programmers are sure that a variable is not going to be null, even though it may contain that value, and we reaffirm ourselves before the compiler. Its appearance is quite common when we migrate Java code to Kotlin, but Kotlin's philosophy is contrary to that, so we must avoid its use as much as possible. Later, several alternatives are described to eliminate this operator in case of a migration from Java.

The ***let*** operator in Kotlin

During application programming we will encounter the following situation on several occasions:

```kotlin 
var propiedad: Int? = 42
fun unMetodo() {
    if (propiedad != null) {
        print(propiedad) // Error: even check null has been taken. Another thread is able to change the value to null
    }
} 
```
    

Since we are using a mutable variable (property), its value can be changed by another thread, just after having done the check. A quick alternative, which is used by the system when importing Java code, is to use the **!! operator** but, as we commented, it is an operator that we should avoid as far as possible.

Another possible solution would be to create a read-only copy of said variable:

```kotlin 
var propiedad: Int? = 42
fun unMetodo() {
    val copia = propiedad
    if (copia != null) {
        print(copia)
    }
} 
```

The previous code compiles, but one of the advantages of Kotlin is the reduction of unnecessary code (boilerplate code) and this code does not fit with this philosophy, since all we do is add more lines of code.

A more suitable alternative would be to use the let operator, which, internally, creates a copy of the variable to be checked in a local immutable variable that will be used in the block defined below. In this way, we could think of a possible solution like the following:

```kotlin 
var propiedad: Int? = 42
fun unMetodo() {
    propiedad.let {
        print(it) //it is propiedad after not null check
    }
} 
```
 

In this example, the let function checks to see if property is null. If not, make a copy of the property in it and run the ***Lambda***.

This solution is valid, but if inside the let block we need to call a function with its value, we may run into an error.

```kotlin 
var propiedad: Int? = 42
fun unMetodo() {
    propiedad.let {
        imprimir(it) // error. imprimir function expects Int (not nullable) but gets it (propiedad) which is nullable, then ERROR.
    }
}
fun imprimir(int: Int) {
    print(int)
}
```
  
In this case, our print function needs a value of type Int and receives a value of type Int ?. Luckily, this type of error is very easy to solve with the ***? Operator***, which will allow us to call our *let* operator in a safe way.

```kotlin 
var propiedad: Int? = 42
fun unMetodo() {
    propiedad?.let {
        imprimir(it)
    }
}
fun imprimir(int: Int) {
    print(int)
}  
```

The let function is more complex. If we look at its implementation, we will see that, in addition to the variable with which we call the function T, we have a return value R.

```kotlin 
inline fun <T, R> T.let(block: (T) -> R): R 
```

Let's see a more complete code where to show the interaction of our class with the rest of the code:

```kotlin 
class MiClase {
    var propiedad: Int? = 42

    fun unMetodo() {
        val valor = propiedad?.let {
            imprimir(it)
            "correcto"
        }
    }
    fun imprimir(i: Int) {
        print(i)
    }
} 
```

In this example, apart from printing the value of the variable, we return a text string indicating that the let block has been executed. But what happens in case property is null?

It is a good programming practice to cover this route, for which we can use the Elvis operator. In this way, if the variable exists we will capture it and use it, and if it is null we will show an error message:

```kotlin 
fun unMetodo() {
    propiedad?.let {
        imprimir(it)
    } ?: run {
        mostrarError()
    }
} 
```

In this way, we can use the value returned by the let block to inform the user, something that was not possible without the Elvis block.

```kotlin 
class MiClase {
    var propiedad: Int? = 42
    fun unMetodo() {
        val valor = propiedad?.let {
            imprimir(it)
            "correcto"
        } ?: "sin Valor"
        imprimir(valor)
    }
    fun imprimir(i: Int) {
        print(i)
    }
    fun imprimir(string: String) {
        print(string)
    }
} 
```


Conditionals {#conditionals}
============================

## If-else statements



Kotlin features several mechanisms for implementing conditional logic. The most common of these is an if-else statement. If an expression wrapped in parentheses next to an if keyword evaluates to true, then code within that branch (i.e. the immediately-following code that is wrapped in curly braces) is executed. Otherwise, the code within the else branch is executed.

```kotlin 
if (count == 42) {
    println("I have the answer.")
} else {
    println("The answer eludes me.")
}
```

You can represent multiple conditions using else if. This lets you represent more granular, complex logic within a single conditional statement, as shown in the following example:

```kotlin 
if (count == 42) {
    println("I have the answer.")
} else if (count > 35) {
    println("The answer is close.")
} else {
    println("The answer eludes me.")
}
```

Conditional statements are useful for representing stateful logic, but you may find that you repeat yourself when writing them. In the example above, you simply print a String in each branch. To avoid this repetition, Kotlin offers conditional expressions. The last example can be rewritten as follows:

```kotlin 
val answerString: String = if (count == 42) {
    "I have the answer."
} else if (count > 35) {
    "The answer is close."
} else {
    "The answer eludes me."
}

println(answerString)
```

## When



**when** defines a conditional expression with multiple branches. It is similar to the switch statement in C-like languages. Its simple form looks like this.

```kotlin 
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { // Note the block
        print("x is neither 1 nor 2")
    }
}
```

**when** matches its argument against all branches sequentially until some branch condition is satisfied.

**when** can be used either as an expression or as a statement. If it is used as an expression, the value of the first matching branch becomes the value of the overall expression. If it is used as a statement, the values of individual branches are ignored. Just like with if, each branch can be a block, and its value is the value of the last expression in the block.

The ***else branch*** is evaluated if none of the other branch conditions are satisfied. If when is used as an expression, the ***else branch*** is mandatory, unless the compiler can prove that all possible cases are covered with branch conditions, for example, with enum class entries and sealed class subtypes).

To define a common behavior for multiple cases, combine their conditions in a single line with a comma:

```kotlin 
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

You can use arbitrary expressions (not only constants) as branch conditions

```kotlin 
when (x) {
    parseInt(s) -> print("s encodes x")
    else -> print("s does not encode x")
}
```

You can also check a value for being in or !in a range or a collection:

```kotlin 
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

Another option is checking that a value is or !is of a particular type. Note that, due to smart casts, you can access the methods and properties of the type without any extra checks.

```kotlin 
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```


***when*** can also be used as a replacement for an if - else if chain. If no argument is supplied, the branch conditions are simply boolean expressions, and a branch is executed when its condition is true:

```kotlin 
when {
    x.isOdd() -> print("x is odd")
    y.isEven() -> print("y is even")
    else -> print("x+y is odd")
}
```

You can capture when subject in a variable using following syntax:

```kotlin 
var x:Int = 100
var b = when(x){
    in 1..10 -> true
    else -> false
}

println("b es $b")
```

  

Loops
=====



Kotlin's syntax for loops differs a bit from Java's.

## For loops



Kotlin intervals would be the classic for loops with i in Java.

```kotlin 
for (i in 1..3) {
    print("$i ") // "1 2 3"
}
```

```kotlin 
for (i in 1 until 4) … //1,2,3 **4 not inculded**
```
  
```kotlin 
for (i in 10 downTo 0 step 2) {
    print("$i ") // "10 8 6 4 2 0"
}
```

The for loop iterates through anything that provides an iterator. This is equivalent to the foreach loop in languages like C#. The syntax of for is the following:

```kotlin 
for (item in collection) print(item)
```

The body of for can be a block.

```kotlin 
for (item: Int in ints) {
    //...
}
```
 

A for loop over a range or an array is compiled to an index-based loop that does not create an iterator object.

```kotlin 
for (i in array.indices) {
    println(array[i])
}
```

  

Alternatively, you can use the ***withIndex*** library function:

```kotlin 
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```

  

## While Loops


***while*** and ***do-while*** loops execute their body continuously while their condition is satisfied. The difference between them is the condition checking time:

*   ***while*** checks the condition and, if it's satisfied, executes the body and then returns to the condition check.
```kotlin 
while (x > 0) {
    //Some code here
    x--    
}
```

*   ***do-while*** executes the body and then checks the condition. If it's satisfied, the loop repeats. So, the body of do-while executes at least once regardless of the condition.
```kotlin 
do {
    val y = retrieveData()
} while (y != null) // y is visible here!
```
  

## Repeat



The ***repeat()*** statement starts with the word repeat, followed by **()**.

*   Inside the parentheses () is the number of repetitions,
*   followed by curly braces {},
*   and inside the curly braces {}, is the code to repeat.

```kotlin
fun main() {
    repeat(5){
        println("Hello World")
    }
}
```
        

![Return result](../assets/images/ud2-2021-09-14-11-59-27.png){width=5cm,center}

::: note
Repeat Hello World 5 times
:::


## Break and continue in loops



Kotlin supports traditional ***break*** and ***continue*** operators in loops.

Kotlin has three structural jump expressions

*   return by default returns from the nearest enclosing function or anonymous function
*   break terminates the nearest enclosing loop
*   continue proceeds to the next step of the nearest enclosing loop

All of these expressions can be used as part of larger expressions:

```kotlin 
val s = person.name ?: return
```


The type of these expressions is the [Nothing type](https://kotlinlang.org/docs/exceptions.html#the-nothing-type).

### Break and continue labels

Any expression in Kotlin may be marked with a label. Labels have the form of an identifier followed by the @ sign, for example: abc@, fooBar@. To label an expression, just add a label in front of it.

```kotlin 
loop@ for (i in 1..100) {
    //...
}
```


Now, we can qualify a break or a continue with a label:

```kotlin 
fun main() {


loop@ for (i in 1..10) {
        for (j in 1..5) {
            if (i%2==0 && j==2) break@loop
            println("${i} - ${j}")
        }
    }
    //Prints
    //1 - 1
    //1 - 2
    //1 - 3
    //1 - 4
    //1 - 5
    //2 - 1
    
}
```

    

  
  

A ***break*** qualified with a label jumps to the execution point right after the loop marked with that label. A ***continue*** proceeds to the next iteration of that loop.

## Return at labels



With function literals, local functions and object expressions, functions can be nested in Kotlin. Qualified return allow us to return from an outer function. The most important use case is returning from a lambda expression. Recall that when we write this:

```kotlin 
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return // non-local return directly to the caller of foo()
        print(it)
    }
    println("this point is unreachable")
}

```

The return -expression returns from the nearest enclosing function - foo. Note that such non-local returns are supported only for lambda expressions passed to inline functions. To return from a lambda expression, label it and qualify the return:

```kotlin 
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit // local return to the caller of the lambda - the forEach loop
        print(it)
    }
    print(" done with explicit label")
}
```

Now, it returns only from the lambda expression. Oftentimes it is more convenient to use implicit labels: such a label has the same name as the function to which the lambda is passed.

```kotlin 
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // local return to the caller of the lambda - the forEach loop
        print(it)
    }
    print(" done with implicit label")
}
```

Alternatively, you can replace the lambda expression with an anonymous function. A return statement in an anonymous function will return from the anonymous function itself.

```kotlin 
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return  // local return to the caller of the anonymous function - the forEach loop
        print(value)
    })
    print(" done with anonymous function")
}
```

Note that the use of local returns in previous three examples is similar to the use of continue in regular loops. There is no direct equivalent for break, but it can be simulated by adding another nesting lambda and non-locally returning from it:

```kotlin 
fun foo() {
    run loop@{
        listOf(1, 2, 3, 4, 5).forEach {
            if (it == 3) return@loop // non-local return from the lambda passed to run
            print(it)
        }
    }
    print(" done with nested loop")
}
```

When returning a value, the parser gives preference to the qualified return:

```kotlin 
return@a 1
```
    

  
  
This means "return 1 at label @a " and not "return a labeled expression (@a 1)".


Webgraphy {#webgraphy}
===============================


*   [http://www.androidcurso.com/index.php/99-kotlin/](http://www.androidcurso.com/index.php/99-kotlin/)
*   [https://www.journaldev.com/19467/kotlin-let-run-also-apply-with](https://www.journaldev.com/19467/kotlin-let-run-also-apply-with)
*   [https://kotlinlang.org/](https://kotlinlang.org/)