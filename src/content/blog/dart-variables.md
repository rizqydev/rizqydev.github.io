---
title: 'Dart Variables: var, const, final, and late'
description: 'A comprehensive guide to understanding variable declarations in Dart.'
pubDate: '2026-08-31'
heroImage: '/blog-placeholder-2.jpg'
categories: ['dart', 'programming']
language: en
---

Dart provides several ways to declare variables, each serving a specific purpose. Understanding when to use explicit types, `var`, `const`, `final`, and `late` is crucial for writing efficient and maintainable Dart code, especially when working with frameworks like Flutter.

## Explicit Type Declaration

The traditional way to declare a variable in Dart is by explicitly specifying its type before the variable name.

```dart
String name = 'Alice';
int age = 30;
bool isStudent = true;
```

While this is perfectly valid and sometimes necessary for clarity or when the compiler cannot infer the type, Dart's powerful type inference often makes explicit typing unnecessary when a variable is initialized immediately.

## `var`

The `var` keyword is used to declare a variable without explicitly specifying its type. Dart's compiler will infer the type based on the initial value assigned to it. Once the type is inferred, it cannot change.

```dart
var name = 'Alice'; // Inferred as String
var age = 30;       // Inferred as int

// age = 'thirty'; // Error: A value of type 'String' can't be assigned to a variable of type 'int'.
```

Use `var` for local variables where the type is obvious from the right-hand side of the assignment.

## `final`

A `final` variable can be set only once and it is initialized when accessed. It is evaluated at runtime. If you know a variable's value won't change after it's initialized, use `final`.

```dart
final String greeting = 'Hello';
final currentTime = DateTime.now(); // Evaluated at runtime

// greeting = 'Hi'; // Error: The final variable 'greeting' can only be set once.
```

**Important distinction for collections (like Lists or Maps):** While you cannot reassign a `final` variable to a new collection, you *can* modify the contents of the collection it points to.

```dart
final List<String> fruits = ['Apple', 'Banana'];
fruits.add('Orange'); // This is perfectly valid! The list contents can change.
fruits[0] = 'Mango';  // Also valid.
// fruits = ['Grape']; // Error: Can't reassign the 'final' variable 'fruits'.
```

`final` is often used for properties in classes that are initialized in the constructor and shouldn't change thereafter.

## `const`

The `const` keyword is used for variables that are compile-time constants. Their values must be known at compile time. This makes them inherently `final`, but with a stricter requirement: the object itself is deeply immutable.

```dart
const double pi = 3.14159;
const int maxItems = 100;

// const currentTime = DateTime.now(); // Error: Const variables must be initialized with a constant value.
```

Unlike `final`, if you assign a collection to a `const` variable, you cannot modify its contents. The collection becomes completely unchangeable.

```dart
const List<String> constantFruits = ['Apple', 'Banana'];
// constantFruits.add('Orange'); // Runtime Error: Cannot add to an unmodifiable list
// constantFruits[0] = 'Mango';  // Runtime Error: Cannot modify an unmodifiable list
```

Use `const` for values that you know will never change and can be computed before the program runs. This can improve performance by allowing the compiler to optimize the code.

## `late`

Introduced with null safety, the `late` keyword has two main use cases:

1.  **Late Initialization:** It allows you to declare a non-nullable variable that is initialized after its declaration.
2.  **Lazy Initialization:** If a `late` variable is initialized where it's declared, the initialization expression is evaluated only when the variable is first used (lazily).

```dart
late String title;

void setTitle() {
  title = 'Dart Variables';
}

void printTitle() {
  // If printTitle is called before setTitle, a LateInitializationError is thrown.
  print(title);
}

// Lazy initialization
late int expensiveResult = performExpensiveCalculation(); // Only called when expensiveResult is used
```

Use `late` when you can't initialize a variable immediately but you are certain it will be initialized before it's read.

## Summary

*   **`var`**: For variables whose type can be inferred and whose value might change.
*   **`final`**: For variables whose value is set once at runtime and won't change.
*   **`const`**: For compile-time constants.
*   **`late`**: For non-nullable variables initialized later, or for lazy initialization.