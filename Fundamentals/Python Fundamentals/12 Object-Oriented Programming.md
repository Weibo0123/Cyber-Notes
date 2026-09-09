# Object-Oriented Programming

## Paradigms and the Basic Idea

Python supports multiple paradigms:
- **Procedural** — code executes step by step.
- **Object-oriented (OOP)** — data and related behavior are grouped into **objects**.

Before reaching for a class, note that tuples/lists/dicts can already group related values:
```python
def get_student():
    name = input("Name: ")
    house = input("House: ")
    return {"name": name, "house": house}     # dict is often clearer than a tuple once fields have distinct meaning
```

### Advantages and Disadvantages of OOP

| Advantages | Disadvantages |
|---|---|
| Reusability — many objects from one class | Complexity — large OOP codebases can get hard to follow |
| Readability — data + behavior grouped together | Performance overhead with many objects |
| Testability — objects tested as independent units | Learning curve for OOP-specific concepts |
| Extensibility — classes can be extended |  |

## Classes, Objects & `__init__`

A **class** is a blueprint; an **object/instance** is a value created from it.
```python
class Student:
    def __init__(self, name, house):
        self.name = name
        self.house = house

student = Student("Harry", "Gryffindor")
student.name        # dot notation accesses attributes
```
- `self` refers to the current instance.
- `__init__` runs automatically when an object is created, and initializes its attributes.
- **Attribute** — data on an object; **Method** — a function defined inside a class.

### Validating Data with `raise`
```python
class Student:
    def __init__(self, name, house):
        if not name:
            raise ValueError("Missing name")
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        self.name = name
        self.house = house
```
`raise` explicitly triggers an exception — here, used so a class rejects invalid data at construction time instead of silently accepting it.

### `__str__` and Custom Methods
```python
def __str__(self):
    return f"{self.name} from {self.house}"

print(student)     # automatically calls student.__str__()
```
Custom methods (e.g. `def charm(self): ...`) access the object's own data through `self`, just like `__init__` does.

## Encapsulation

**Encapsulation** groups an object's data and behavior together while restricting direct access to internal state — so outside code interacts with an object through a controlled interface rather than poking at its internals directly.

**Convention — single leading underscore (`_name`):** signals "internal, please don't touch directly," but Python doesn't enforce it.

**Name-mangled — double leading underscore (`__name`):** Python actually rewrites the attribute name internally, making accidental external access harder (though still not truly private):
```python
class Computer:
    def __init__(self):
        self.__maxprice = 900

    def sell(self):
        print("Selling Price: {}".format(self.__maxprice))

    def setMaxPrice(self, price):
        self.__maxprice = price

c = Computer()
c.sell()             # Selling Price: 900
c.__maxprice = 1000    # does NOT touch the real internal attribute (name-mangled)
c.sell()                 # still: Selling Price: 900
c.setMaxPrice(1000)        # the correct way to change it
c.sell()                     # Selling Price: 1000
```

### `@property` — Managed Attribute Access
```python
@property
def house(self):
    return self._house

@house.setter
def house(self, house):
    if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
        raise ValueError("Invalid house")
    self._house = house
```
```python
student.house = "Ravenclaw"     # looks like plain assignment, actually calls the setter
```
This is Python's more idiomatic take on encapsulation: `house` looks like a normal attribute from the outside, but reads/writes are validated/controlled underneath. `_house` is the conventionally-internal backing attribute.

## Python Classes You Already Use

```python
type(50)         # int
type("hello")      # str
type([])             # list
```
Built-in types are classes too — `str.lower()` and `list.append()` are just methods defined on Python's built-in classes.

## Class Methods and Static Methods

```python
class Hat:
    houses = ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]

    @classmethod
    def sort(cls, name):
        print(name, "is in", random.choice(cls.houses))

Hat.sort("Harry")     # called on the CLASS, no instance needed
```
- **Instance method** — receives `self` (a particular object).
- **Class method** (`@classmethod`) — receives `cls` (the class itself); can act as an alternative constructor:
```python
class Student:
    @classmethod
    def get(cls):
        name = input("Name: ")
        house = input("House: ")
        return cls(name, house)

student = Student.get()
```
- **Static method** (`@staticmethod`) — receives neither `self` nor `cls`; it's just a function that happens to live inside the class namespace.

## Inheritance

```python
class Wizard:
    def __init__(self, name):
        if not name:
            raise ValueError("Missing name")
        self.name = name

class Student(Wizard):
    def __init__(self, name, house):
        super().__init__(name)     # calls Wizard.__init__()
        self.house = house
```
`Wizard` is the parent/base class; `Student` is the child/derived class, reusing `Wizard`'s logic via `super()` while adding its own attributes.

### Python's Exceptions Are Also a Hierarchy
```
BaseException
└── Exception
    ├── ArithmeticError → ZeroDivisionError
    ├── AssertionError
    ├── AttributeError
    ├── ImportError → ModuleNotFoundError
    ├── LookupError → KeyError
    ├── NameError
    ├── SyntaxError → IndentationError
    └── ValueError
```
Catching a parent exception type also catches its children — worth knowing when deciding how specific an `except` clause should be.

## Polymorphism

**Polymorphism** lets the same interface (e.g. a method name) behave differently depending on the actual object it's called on:
```python
class Animal:
    def __init__(self, name):
        self.name = name
    def speak(self):
        raise NotImplementedError     # forces subclasses to implement their own version

class Dog(Animal):
    def speak(self):
        return self.name + " says Woof!"

class Cat(Animal):
    def speak(self):
        return self.name + " says Meow!"

fido, isis = Dog("Fido"), Cat("Isis")
print(fido.speak())      # Fido says Woof!
print(isis.speak())        # Isis says Meow!
```
Or without a shared base class at all (duck typing — Python doesn't require it):
```python
class Parrot:
    def fly(self):
        print("Parrot can fly")

class Penguin:
    def fly(self):
        print("Penguin can't fly")

def flying_test(bird):
    bird.fly()      # works for ANY object with a fly() method

flying_test(Parrot())
flying_test(Penguin())
```

## Operator Overloading

Define how operators behave on custom objects via special ("dunder") methods:
```python
class Vault:
    def __init__(self, galleons=0, sickles=0, knuts=0):
        self.galleons, self.sickles, self.knuts = galleons, sickles, knuts

    def __add__(self, other):
        return Vault(
            self.galleons + other.galleons,
            self.sickles + other.sickles,
            self.knuts + other.knuts
        )

total = potter + weasley     # calls potter.__add__(weasley)
```
In `__add__`, `self` is the left-hand operand, `other` is the right-hand one. Common dunders: `__init__` (construction), `__str__` (string form), `__add__` (`+`), and many more per operator.

## Cheat Sheet

```
class Name:
    def __init__(self, ...): self.attr = ...     constructor
    def __str__(self): return "..."                 str(obj) / print(obj)
    def method(self): ...                              instance method

raise ValueError("msg")     validate/reject bad data explicitly

_name    convention: "internal, don't touch"
__name    name-mangled: harder to access accidentally from outside

@property / @x.setter        controlled attribute access (looks like a plain attribute)
@classmethod (cls)              acts on the class; alt-constructor pattern
@staticmethod                     no self/cls; just grouped under the class

class Child(Parent):             inheritance
    super().__init__(...)           call the parent's version

Polymorphism: same method name, different behavior per class (with OR without shared base)
Operator overloading: __add__, __str__, etc. — define what operators mean for your class

OOP pros: reuse/readability/testability/extensibility
OOP cons: complexity/overhead/learning curve
```
