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
- Górne ograniczenia typów
- Dolne ograniczenia typów
- Wariancje
- Biblioteka Shapeless

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
## Zastosowanie
```scala
object Main extends App {
  val cat = new Cat
  val dog = new Dog
  val lion = new Lion
  val catCage = new Cage[Pet](cat)
  val dogCage = new Cage[Dog](dog)
  val lionCage = new Cage[Lion](lion) 
  //type arguments [Lion] do not conform to class Cage's type parameter bounds [P <: Pet]
}
```
- ograniczenie klasy Cage przez P <: Pet spowodowało że klasa Lion dziedzicząca po Animal nie spełnia warunków utworzenia klasy
---
# Dolne ograniczenia typów
```scala
class Cage[P >: Pet](p: P){
  def pet: P = p
}

object Main extends App {
  val cat = new Cat
  val dog = new Dog
  val lion = new Lion
  val anyRef = new AnyRef
  val catCage = new Cage[Pet](cat)
  val dogCage = new Cage[Dog](dog)
  //błąd!
  val lionCage = new Cage[Lion](lion)
  //błąd!
  val anyRefCage = new Cage[AnyRef](anyRef)
}
```
---
- Ograniczenie P :> Pet powoduje że dany typ może być maksymalnie na poziomie typu Pet
- Dlatego też wszystkie standardowe typy w scali (np. AnyRef) są przez nią przyjmowane
---
### Ograniczenia typoów można ze sobą łączyć
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

class Lion extends WildAnimal {
  override def name: String = "Lion"
}

class WildAnimal extends Animal {
  override def name: String = "Wild animal"
}
```
---
```scala
class Cage[P >: Lion <: Animal](p: P){
  def pet: P = p
}

object Main extends App {
  val dog = new Dog
  val lion = new Lion
  val anyRef = new AnyRef
  val wildAnimal = new WildAnimal
  val dogCage = new Cage[Dog](dog) //błąd!
  val lionCage = new Cage[Lion](lion) //ok
  val anyRefCage = new Cage[AnyRef](anyRef) //błąd!
  val wildAnimalCage = new Cage[WildAnimal](wildAnimal) //ok
}
```
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
- biblioteka skupiająca się na programowaniu generycznym i opartym na typach
- rozwijana od 2011 roku

- https://github.com/milessabin/shapeless
- https://github.com/milessabin/shapeless/wiki

---
## Struktura HList
```scala
import shapeless.{ ::, HList, HNil }
case class Meal(name: String)

val list = 1 :: "String" :: Meal("Burger") :: HNil
```
- każdy element jest znany podczas kompilacji
- dostępne są wszystkie metody charakterystyczne dla list (take, head, tail, map, zip etc.)
---
### Przykład użycia
```scala
val stringValue = list.select[String] //zwraca "String" jako wartość typu String
val intValue = list.head //zwraca 1 jako wartość typu Int
val intV :: stringV :: mealV :: HNil = list //przykład zastosowania pattern matching
val listV = list.select[List[Int]] //błąd kompilacji, lista nie zawiera danych tego typu
```
---
## Funkcje polimorficzne z wykorzystaniem biblioteki shapeless
```scala
import shapeless.Poly1

object getLength extends Poly1{
  implicit def default[T] = at[T](t => 1)
  implicit def caseInt = at[Int](x => 1.toString.length)
  implicit def caseString = at[String](_.length)
  implicit def caseMeal = at[Meal](_.name.length)
}

getLength(list) //13
```
- dzięki zastosowaniu interfejsu Poly1 z biblioteki Shapeless byliśmy w stanie dostosować działanie metody dla różnych typów w obrębie jednej kolekcji.
---
## Coproduct
- rozwinięcie konceptu Either znanego ze standardowej Scali
- wspiera większość standardowych operacji (map, select, union)
---
### Either
```scala
scala> def div(x: Int, y: Int): Either[String, Int] = {
     | if (y == 0) Left("Wrong value provided")
     | else Right(x/y)
     | }
div: (x: Int, y: Int)Either[String,Int]

scala> div(1,0)
res2: Either[String,Int] = Left(Wrong value provided)

scala> div(1,1)
res3: Either[String,Int] = Right(1)
```
- Either umożliwia nam operowanie tylko na dwóch typach.
---
### Przykład użycia Coproduct
```scala
type ISB = Int :+: String :+: Boolean :+: CNil

val isbString = Coproduct[ISB]("test")
val isbInt = Coproduct[ISB](2)
val isbBoolean = Coproduct[ISB](True)

isbString.select[String] //zwróci Some("test")
isbString.select[Int] //zwróci None
isbString.select[Boolean] //zwróci None
```
---
### Przykład użycia z funkcją map
```scala
object size extends Poly1 {
  implicit def caseInt = at[Int](i => (i,i))
  implicit def caseString = at[String](s => (s,s.length))
  implicit def caseBoolean = at[Boolean](b => (b,1))
}
isbString map size //zwróci ("test",4)
isbInt map size //zwróci (2,2)
isbBoolean map size //zwróci (True,1)
```
---
# Dziękuję za uwagę
