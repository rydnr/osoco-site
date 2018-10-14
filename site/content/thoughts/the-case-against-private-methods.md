+++
title = "The case against private methods"
subtitle = "Why I don't use them"
date = "2018-10-14T21:21:15+01:00"
months = [ "2018-10" ]
authors = [ "josé-san-leandro" ]
authorPhoto = "josé-san-leandro.jpg"
draft = "false"
tags = [ "osoco", "programming", "oop", "covariance", "contravariance", "solid", "open-closed principle", "abstraction", "encapsulation", "liskov", "java", "groovy" ]
summary = "Using private methods is a questionable practice"
background = "padlock.jpg"
backgroundSummary = "private.jpg"
+++

# Introduction

When writing object-oriented code, most programming languages allow you to control how your code is seen from the outside. Usually, they define three mechanisms:

  - **public**: the code can be accessed by anyone;
  - **protected**: only descendants can access;
  - **private**: no one outside the class can see it.

It's a common practice to favor <em>private</em> methods above all. For example, <i>Test-driven Development</i> [[1]](#1) consists of a three-step coding loop: write a failing test, make it pass, and refactor.
When refactoring means creating a new method, it's usually defined privately, since it's part of the internal structure of the class.

## Abstraction

The bottom line [[2]](#2) here is to understand that the implicit API of a class comprises all its public methods. When designing a class, one must take care of how the class is seen from the outside.
Public methods must make sense from an external point of view. We should pay attention to hide unnecessary details.

But not only that: such implicit API constrains how the class is allowed to evolve. External logic using a public method means the class cannot remove it or rename it without breaking that other code.

These constraints on public methods are also a liability for descendant classes as well. The smaller the API, the easier to understand and maintain.

## Encapsulation

*Encapsulation* [[3]](#3) refers to bundling together data and methods dealing with that data. It's common practice to hide data from the outside, so we declare both it and related methods as private.

## Analysis in Java/Groovy

For the sake of concreteness, let's consider the Java or Groovy programming languages. They define also a **package protected** level, but it is unimportant for the discussion here.

What is important for our discussion is acknowledging that a private method in Java does not mean it's only accessible by the instance. It's accessible by any code inside the class.
This means:

   - you can call private methods on other instances, not only your own.
   
```Java
public class A {

    private void myStuff() { ... }

    public void godMode() {
        new A().myStuff();
    }
}
```
   
   - you can call private methods from code outside the class.

```Java
public class A {

    private void myStuff() { ... }

    static class B extends ExternalService {
        public void godMode() {
            new A().myStuff();
        }
    }
}
```
   
On the one hand, this means Java doesn't really encourage *Encapsulation*. It doesn't protect data and logic on that data to be private to the instance they belong.

On the other hand, it fails to implement proper *Abstraction*, since it allows external classes to see and use private data and methods.

# The case against private methods

I don't write private methods, but my code is consistent with the principles of *Abstraction* and *Encapsulation*. I think private methods fail to respect two SOLID [[4]](#4) principles:

## Open/closed principle

This principle [[5]](#5) states that <em>software entities should be open for extension, but closed for modification</em>. It takes into account how to write code so that it can be customized or extended in the future.
It takes the *Abstraction* and *Encapsulation* concepts to point out the role of the class towards its descendants. Apply *Abstraction* when defining the public API, but don't let *Encapsulation* fail you when extending the class.

I like to think of this as if there were two APIs: the public one towards the outside world, and an internal one, towards me and my descendants.

If I use private methods, I'm acknowledging I'm restraining their ability to extend the code.

## Liskov substitution principle

In this context, the common argument advocating private methods consists of protecting the behavior of the class. Actually, there're some static analysis tools that complain if you use non-private methods in constructors.
This is all well and good, but what they'd need to prevent is constructors with logic that can be replaced, and thus make the class misbehave. In other words, they should prevent using non-final methods in constructors.

This all has to do with the *Liskov substitution principle* [[6]](#6), which addresses what kind of parent-children relationships are correct. Put simply, if a class has some properties (as seen from the outside), then any subtype of that class must respect the same properties.

This principle has some implications. It requires descendants to implement <i>contravariance</i> [[7]](#7) in the arguments of extended methods and <i>covariance</i> [[7]](#7) in what the extended methods return. They can't throw new exceptions either.

Declaring a method as private to avoid dealing with the Liskov principle is doing a disservice to anyone trying to extend the class, and thus fails to respect the <em>open/closed principle</em>.

## Code reuse

This practice [[8]](#8) needs little explanation. The most basic guideline is pretty simple: a private method cannot be reused any further. Even worse, private methods which came into existence due to a refactoring won't be reused at all.

## Lazyness

I've noticed some people use private methods to respect <em>abstraction</em> and <em>encapsulation</em> without thinking further and avoid losing focus on the task at hand.

# Conclusions

The rule of thumb I follow is to make anything not related to the public API as **protected**.

Common conventions include using accessor methods for data, avoiding accessing data directly, so protected methods won't affect *Encapsulation*.

For each of those methods, I consider if they are affecting any property observable from the outside. If needed, I make them **final**, particularly if they're used in constructors.

Again, the bottom line is to think not only on the API to the outside world, but also on the API to descendants.

If you are a TDD practitioner and you are used to write private methods when refactoring, declare them as <em>protected final</em> instead.
You'll be sacrifying the extensibility of your code, but at least you'll allow potential reuse of it.

Finally, remember not to be fooled by private methods: they can be called from external classes, and different instances (as long as they are defined in the source code of the class). Do your homework if you don't believe it.

## References

- [1] <a name="1" href="https://en.wikipedia.org/wiki/Test-driven_development" target="_blank">wikipedia: Test-driven development</a>
- [2] <a name="2" href="https://en.wikipedia.org/wiki/Abstraction_(computer_science)" target="_blank">wikipedia: Abstraction</a>
- [3] <a name="3" href="https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)" target="_blank">wikipedia: Encapsulation</a>
- [4] <a name="4" href="https://en.wikipedia.org/wiki/SOLID" target="_blank">wikipedia: SOLID</a>
- [5] <a name="5" href="https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle" target="_blank">wikipedia: Open/closed principle</a>
- [6] <a name="6" href="https://en.wikipedia.org/wiki/Liskov_substitution_principle" target="_blank">wikipedia: Liskov substitution principle</a>
- [7] <a name="7" href="https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science)" target="_blank">wikipedia: Covariance and contravariance</a>
- [8] <a name="8" href="https://en.wikipedia.org/wiki/Code_reuse" target="_blank">wikipedia: Code reuse</a>

## Credits

- **Images**:
  - <a href="https://pixabay.com/en/private-privacy-green-secret-1647769/" target="_blank_">Private</a>, from Pixabay, licensed under <a href="https://creativecommons.org/publicdomain/zero/1.0/deed.en">CC0 Creative Commons</a>.
  - <a href="https://pixabay.com/en/padlock-shed-locked-lock-secure-690286/" target="_blank">Padlock</a>, from Pixabay, licensed under <a href="https://creativecommons.org/publicdomain/zero/1.0/deed.en">CC0 Creative Commons</a>.







