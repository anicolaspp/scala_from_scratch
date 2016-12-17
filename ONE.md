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
   keyword new." (Prog in Scala, 3rd Ed)

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
  def increment(): Int =
    value += 1

  // Same as decrement, but subtract counter
  // by 1
  def decrement(): Int =
    value -= 1
}
```

```
val x = new FooMutableFixed
x.value // compile-time error
x.increment
x.decrement
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
 def increment: FooImmutable = new FooImmutable(value + 1)

 def decrement: FooImmutable = new FooImmutable(value - 1) 
}
```

* `value` is immutable and private
* To make it private, remove `val` 




  