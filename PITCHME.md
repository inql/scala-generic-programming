---
marp: true
theme: simple
class:
  - lead
paginate: true
header: 'Programowanie Generyczne w języku Scala'
footer: 'Dawid Bińkuś'
---
<!-- _class: invert -->
# Programowanie Generyczne
##### W języku Scala
###### Dawid Bińkuś - Uniwersytet Gdański

---
# Zagadnienia
- Wprowadzenie
- Klasy generyczne
- Metody polimorficzne
https://docs.scala-lang.org/tour/polymorphic-methods.html
- Górne ograniczenia typów
https://docs.scala-lang.org/tour/upper-type-bounds.html
- Dolne ograniczenia typów
https://docs.scala-lang.org/tour/lower-type-bounds.html
- Wariancje
https://docs.scala-lang.org/tour/variances.html
- Biblioteka Shapeless
https://jto.github.io/articles/getting-started-with-shapeless/

---
# Wprowadzenie
---
# Klasy generyczne
```scala
class Stack[T] {
  var elems: List[T] = Nil
  def push(x: T) { elems = x :: elems }
  def top: T = elems.head
  def pop() { elems = elems.tail }
}
```
- definiowana za pomocą wyrażenia [T]
- parametr T definiuje jakie typy są zwracane bądź przyjmowane jako argumenty w ramach danej klasy
---
## Przykład zastosowania
```scala
object GenericsTest extends App {
  val stack = new Stack[Int]
  stack.push(1)
  stack.push(2)
  println(stack.top)
  stack.pop()
  println(stack.top)
}
```
- klasa Stack zdefiniowana z typem Int
---
## Klasy generyczne i podtypy
```scala
class Fruit
class Apple extends Fruit
class Banana extends Fruit

val stack = new Stack[Fruit]
val apple = new Apple
val banana = new Banana

stack.push(apple)
stack.push(banana)
```
- klasy Apple i Banana są potomkami klasy Fruit więc mogą być użyte w klasie generycznej z wykorzystaniem typu Fruit

---
- Podtypowanie typów generycznych jest domyślnie określane jako niezmienne. Oznacza to tyle, że w sytuacji gdy mamy dwie klasy o danych typach np. MyClass[T] i MyClass[S] to MyClass[T] jest podtypem klasy MyClass[S] tylko wtedy gdy S = T
---
# Metody polimorficzne
---

```scala
def listOfDuplicates[A](x: A, length: Int): List[A] = {
  if (length < 1)
    Nil
  else
    x :: listOfDuplicates(x, length - 1)
}
```
- tworzenie metod polimorficznych wygląda podobnie jak tworzenie klas
- metody mogą być parametryzowane zarówno przez wartość jak i przez typy
---
## Przykład zastosowania
```scala
println(listOfDuplicates[Int](3, 4))  // List(3, 3, 3, 3)
println(listOfDuplicates("La", 8))  // List(La, La, La, La, La, La, La, La)
```
- przypadek pierwszy -> wywołujemy metodę polimorficzną i określamy jej typ z góry
- przypadek drugi -> w Scali nie jest wymagane jawne podanie typu metody polimorficznej. W tym przypadku kompilator wniosku typ danej metody bazując na typach podanych w argumentach.
---
# Górne ograniczenia typów
```scala
abstract class Animal {
 def name: String
}

abstract class Pet extends Animal {}

class Cat extends Pet {
  override def name: String = "Cat"
}

class Dog extends Pet {
  override def name: String = "Dog"
}

class Lion extends Animal {
  override def name: String = "Lion"
}

class Cage[P <: Pet](p: P) {
  def pet: P = p
}
```
---
# Dolne ograniczenia typów
---
# Wariancje
---
```scala
class Stack[+T] {
  def push[S >: T](elem: S): Stack[S] = new Stack[S] {
    override def top: S = elem
    override def pop: Stack[S] = Stack.this
    override def toString: String =
      elem.toString + " " + Stack.this.toString
  }
  def top: T = sys.error("no element on stack")
  def pop: Stack[T] = sys.error("no element on stack")
  override def toString: String = ""
}
```
- dodawane podczas definiowania abstrakcji klasy (a nie jej użytkowników jak ma to miejsce w Javie)
- Adnotacja +T określa typ T tak, aby mógł być zastosowany wyłącznie w pozycji kowariantnej. 
- oznacza to, że Stack[T] jest podtypem Stack[S] jeżeli T jest podtypem S. 
---
## Przykład użycia
```scala
  var s: Stack[Any] = new Stack().push("hello")
  s = s.push(new Object())
  s = s.push(7)
```
- typ Object jest w pozycji kowariantnej (jest podtypem) typu Any więc może być użyty w naszej implementacji
- jako że typ Any jest rodzicem wszystkich typów w Scali, na stos możemy też umieścić typ Int
---
# Biblioteka Shapeless
---
# Dziękuję za uwagę
