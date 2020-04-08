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
- Wariacje
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
---
# Dolne ograniczenia typów
---
# Wariacje
---
# Biblioteka Shapeless
---
# Dziękuję za uwagę
