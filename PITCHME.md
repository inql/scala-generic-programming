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
wymysl sobie
- Klasy generyczne
https://docs.scala-lang.org/tour/generic-classes.html
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
---
```scala
class Stack[A] {
  private var elements: List[A] = Nil
  def push(x: A) { elements = x :: elements }
  def peek: A = elements.head
  def pop(): A = {
    val currentTop = peek
    elements = elements.tail
    currentTop
  }
}
```
---
# Metody polimorficzne
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
