# Scala From Scratch 

## Intro to Scala

### Agenda

* `var` versus `val`
* Class
* Object
* Case Class
* ...

### `var` versus `val`

* `var` - mutable reference
* `val` - immutable reference
* Says nothing about the referenced value
  * could be mutable or immutable

```
var x = 42
x = 66 // legal

val y = 66
y = 42 // illegal
```

### Class

* "A class is a blueprint for objects. Once you define a class, 
   you can create objects from the class blueprint with the 
   keyword new." (Prog in Scala)

```
class FooMutable {
  var value = 0

  // Increase internal counter by 1, 
  // returning the new counter
  def increment(): Int = 
    value += 1

  // Same as decrement, but subtract counter
  // by 1
  def decrement(): Int = 
    value -= 1
}
```

Let's use it from the Scala REPL.

* REPL - Read/Eval/Print/Loop
* Useful for understanding code to try it out

```
val x = new FooMutable
x.increment
x.decrement
```

* We forgot to make `value` to be immutable.
 * We defined a public contract of `increment` and `decrement`
 * `FooMutable`'s client could perform operations
    other than `increment` and `decrement`


```
class FooMutableFixed {
  // Keep it private to avoid a client's manipulation
  private var value = 0

  // Increase internal counter by 1,
  // returning the new counter
  def increment(): Unit =
    value += 1

  // Same as decrement, but subtract counter
  // by 1
  def decrement(): Unit =
    value -= 1

  // Output current `value`
  def current: Int = value

}
```

```
val x = new FooMutableFixed
x.value // compile-time error
x.increment
x.current
x.decrement
x.current
```

* It's standard to put `()` on a method call if it has a side effect
  * http://docs.scala-lang.org/style/method-invocation.html
* Side effect - "modifies some state outside its scope or has an 
  observable interaction with its calling functions or the outside world."
  - https://en.wikipedia.org/wiki/Side_effect_(computer_science)
* When learning Scala, avoid `var`'s, to embrace Functional Programming(FP)
  * FP in Scala favors immutability 
* Why? Immutability makes it easier to reason about/understand code
  * Story - work experience

``` 
class FooImmutable(val value: Int) {
 def increment = new FooImmutable(value + 1)
 def decrement = new FooImmutable(value - 1) 
}
```

```
val x = new FooImmutable(42)
x.increment
x.decrement
x.increment.decrement
```

* `value` is immutable and private
* To make it private, remove `val` 
* Observe that there's no `()` appended to each method's name

### Object

* Unlike Java, Scala does not have `static` members.
* "Sometimes, you want to have variables that are common to all objects" 
  (https://docs.oracle.com/javase/tutorial/java/javaOO/classvars.html).

```
// Java
public class MathUtility {
 public static int add(int x, int y) {
   return x + y;
 }
}
```

* "Methods and values that aren’t associated with individual instances of a class belong in singleton objects"
  (http://docs.scala-lang.org/tutorials/tour/singleton-objects.html)

```
object MathUtility {
  val Pi = 3.14

  def add(x: Int, y: Int): Int      = x + y
}
```

* A `companion` object shares the same name as a class in the same source

#### Person.scala

```
object Person {
  def reversedNames(first: String, last: String): String = 
    last + "," + first
}
class Person(first: String, last: String) 
```
* Additionally, a `companion` object has access to its companion 
  class's private fields, and vice-versa

```
object A {
 private val objX = 42
}
class A {
 private val classX = 66
}
```
 
* "If you are a Java programmer, one way to think of singleton objects is
   as the home for any static methods you might have written in Java" (Prog in Scala).

#### Creating a `main`

```
object Foo {
 def main(args: Array[String]): Unit = {
   println("hello world!")
 }
```

`Application`

* Scala provides another way to run an application:

```
object Foo extends App {
  // put main code here
  println("hello world!")
}
```

* However, due to initialization nuances, I recommend using `def main`, avoiding
  `App`. (http://www.scala-lang.org/api/2.12.x/scala/App.html)

```
scala> object F extends App {
     |   val x = 42
     |   println("hi")
     | }
defined object F

scala> F.x
res0: Int = 0

scala> F.main(Array.empty)
"hi"

scala> F.x
res2: Int = 42
```

### Case Classes



### Equality

* Use `==` to compare value (stack) or reference types (heap)
* `==` "routes to .equals, except that it treats nulls properly" (http://stackoverflow.com/a/7681243/409976)
* To compare objects via "reference equality," use `eq` (rarely used)

#### Value Types

* scala.Double, scala.Float, scala.Long, scala.Int, scala.Char, scala.Short, and scala.Byte are the numeric value types.
* scala.Unit and scala.Boolean
  -http://www.scala-lang.org/api/2.12.x/scala/AnyVal.html

#### Reference Types

* objects that are `new`'d, i.e. stored on the heap

#### `==` Examples

```
42 == 42 // outputs true

// Recall that case classes provide a reasonable
// 'equals' implementation
case class Person(id: Long, name: String)
Person(42, "Joe") == Person(42, "Joe") // outputs true
```

### Collections

#### List - `List[A]`

* Linked List (https://en.wikipedia.org/wiki/Linked_list)

- [1] --> [2] --> [3] --> Nil (termination of list)

* The `bread` in bread and butter of Scala and Functional Programming
* Bunch of elements with constant prepend, i.e. add to head, but linear append, add to tail

```
1 :: List[Int](2,3) // prepend
List[Int](2,3) :+ 4 // append
```

#### Set - `Set[A]`

* Collection of elements that contains no duplicates
* Calling `Set(1,2,3)` in Scala will, by default, use a `HashSet`.
* `HashSet` uses a Hash Trie (http://docs.scala-lang.org/overviews/collections/concrete-immutable-collection-classes.html#hash_tries)
  * Ensures a "reasonably fast lookups and reasonably efficient functional insertions and deletions"

```
Set[Int](1,2,3)
```

#### Map - `Map[K, V]`

* Consists of key-value pairs

```
val map = Map[Int, String]( (1, "hello"), (2, "world") )
map.get(1)  // returns Some("hello")
map.get(42) // returns None
```

### Recursion

* "Recursion occurs when a thing is defined in terms of itself or of its type."
  * https://en.wikipedia.org/wiki/Recursion

```
def factorial(n: Int): Int = {
  if (n < 0)       throw new RuntimeException("Invalid input. Must be >= 0.")
  else if (n == 0) 1 
  else             n * factorial(n - 1)
}
```

Let's break down `factorial(3)`:

```
factorial(3) 
3 * factorial(2)
3 * 2 * factorial(1)
3 * 2 * 1 * factorial(0)
3 * 2 * 1 * 1
```

Let's look at a possible implementation using Java:

```
public static int factorial(int x) {
  int start = 1;

  if(x < 0) {
    throw new RuntimeException("Invalid input. Must be >= 0.")
  }
  else {
    while(x != 0) {
      start = x * start;
      x--;
    }
    return start;
  }
}
```

* We cannot reason about the Java `factorial` method in the same way
* It's necessary to keep track of state, i.e. `start`

### Bread and Butter List Functions

* `map`

```
def map[A](list: List[A], f: A => B): List[B] = list match {
   case x :: xs => f(x) :: map(xs, f) 
   case Nil     => Nil
}
```

* `filter`
* `foldLeft`
* `foldRight`

### 


###

## References

* Programming in Scala, 3rd Edition; (Odersky, Spoon, and Venners)