# Topic: PROGRAMMING PARADIGM

## OOP. What is OOP? Why should we use OOP?

OOP stands for object-oriented programming. Like its name, OOP means focusing on objects and building things upon them. In real world, almost everything is an entity with some data and some behaviors, which OOP can be used to articulated quite exactly, compared to other programming paradigms like procedural programming or functional programming.

## What are the 4 principles of OOP?

The four principles of OOP are encapsulation, abstraction, inheritance, and polymorphism.

- Encapsulation is a feature of OOP that wraps things into a unity, like wraps methods and fields into a class, wrapping classes into a package.
- Abstraction means when we use a class, we only care about its public things, like public fields, methods, and don't care about its private stuff, about how it is implemented under the surface.
- Inheritance allows a class to inherit the properties and characteristics of other classes.
- Polymorphism allows using the same type and the same function call for same name but different action of related entities.

```python
class Bird:
    def make_sound(self):
        pass

class Sparrow(Bird):
    def make_sound(self):
        return "Chirp!"

class Parrot(Bird):
    def make_sound(self):
        return "Squawk!"
```

## What does private, protected, public means and what are the difference?

These keywords indicate the access scope of things like methods, fields in an OOP language.

- Public: Members declared as `public` can be accessed by everthing (every class, package).
- Protected: Members declared as `protected` can be accessed any thing in the same package and outside things that has inheritance relationship.
- Default: Members declared as `default` can be accessed by anything in the same package.
- Private: Members declared as `private` can be accessed by things in the same class.

## Interface vs abstract class

The key difference between abstract class and interface is the relationship between it and the classes that extend or implement it.

When class A extends the abstract class B, class A is a B. For example, a cat is an animal. But when class A implements an interface C, it only means A has C characteristics, like the Cat class can implement Climbing Tree interface.

## SOLID Principles

- `Single Responsibility Principle`
  - **Definition**: A class should have only one reason to change, meaning that it should have only one responsibility.
  - **Implication**: Each class or module should have a single responsibility, and if there is more than one reason to change it, the class should be divided into multiple classes.
- `Open/Closed Principle`
  - **Definition**: Software entities (classes, modules, functions, etc.) should be open for extension and closed for modification.
  - **Implication**: You should be able to extend the behavior of a module without modifying its source code.
- `Liskov Substitution Principle`
  - **Definition**: If a class is a subclass of another class, it should be able to be used wherever its base class is used without affecting the correctness of the program.
- `Interface Segregation Principle`
  - **Definition**: A class should not be forced to implement interfaces that it does not use.
  - **Implication**: It's better to have several small, specific interfaces rather than a large, general-purpose one.
- `Dependency Inversion Principle`
  - **Definition**: Entities should depend on abstractions (interfaces or abstract classes) rather than correct implementations.

## What is composition? Compare composition vs inheritance

Composition is a has-a relationship. Like a house has rooms, a car has wheels. In OOP, the composition relationship is represented as fields in a class.

Inheritance is an is-a relationship. Like a dog is an animal, a rose is a flower. In OOP, the inheritance relationship is represented as a class inheriting another class.

## Explain class constructor. Why does the constructor not return any value?

A class constructor is a special member function that automatically gets called whenever you create a new object that class.

------------------------------

## Functional programming. What is FP? Why should we use FP?

Functional programming is a programming paradigm that focuses on building programs using functions as the primary building blocks. FP focuses on the transformation of data through pure functions.

## FP characteristics

Some highlight characteristics of functional programming are:

- Immutability: Variable should be set once and not changed.
- Pure function: The return value should depend only on the input value, and create no side effects (do not modify anything outside their scope).
- First-class functions: Functions can be treated like any other value. We can create a function, assign a function to a variable, pass it as a parameter and return it as a value.
- Higher-order functions: Functions can operate on other functions. This allows for powerful abstractions and concise code (i.e. you can define a function that take other functions as parameters).

## What is immutability and why is it important?

To be defined.

## What are pure functions and why are they important to functional programming?

To be defined.

## What does it mean when a function is a first-class citizen?

To be defined.

## Lazy evaluation

To be defined.

## What is higher-order functions?

To be defined.

## What are currying and partial application?

To be defined.

## What are pure and impure functions?

To be defined.

## Difference between flatMap() and map()

To be defined.

## Explain functor, applicative, monoid, monad

These are all concepts in functional programming that deal with data structure and how they transform under functions.

- Functor is a type class that wraps value in a context and defines a `fmap`. A `fmap` is the function that takes another function and a wrapped value, then return a new wrapped value.

- Applicative is a type class that wraps value in a context and defines a function that can take a value in a wrapped context and a function in a wrapped context, apply the function to value, then return the result wrapped in the context.

- Monoid: to be defined.

- Monad is a type class that wraps value in a context and defines a function that can take a value in a wrapped context and a function in a wrapped context, apply the function to value, then return the result wrapped in the context. But the different thing between applicative and monad is the return type of the function. In applicative, the function returns the value directly, while in monad, the function returns the value wrapped in a context.
