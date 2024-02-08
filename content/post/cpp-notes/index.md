---
title: "C++ Learning Notes"
description: "Personal C++ learning notes."
date: 2024-02-05T17:17:25+08:00
hidden: false
comments: true
draft: false
categories:
- Skills
tags:
- C++
---

# C++ Learning Notes

###### `const` modifier after function declaration

```cpp
class A {
public:
    void foo() const;
    void foo();
    // note: the const modifier is part of the function signature, that means, the two foo functions are different and overloadable.
};

int main() {
    A a;
    a.foo(); // OK, if the class does not have the ordinary foo function, the const foo function will be called. it's OK to call a const function on a non-const object.
    const A b;
    b.foo(); // OK
    b.foo(); // Error: no matching function for call to 'A::foo() const'
}
```

The `const` keyword after a member function declaration means that the function is a "const member function ". This has two main implications:

1. The function can be called on `const` objects of the class, as well as on `pointer/reference to const` objects. Non-const member functions can only be called on non-const objects.
2. Inside the function, the `this` pointer is treated as a `const` pointer, meaning you can't modify the object's data members (unless they are declared as `mutable`).

So, you would typically make a member function `const` if it doesn't need to modify the object, and you want to be able to call it on `const` objects. This is often the case for "getter" functions that just return the value of a data member, for example.

###### macro misc

```cpp
#define STRINGIFY(x) #x
#define TOSTRING(x) STRINGIFY(x)
#define LINE_NUMBER TOSTRING(__LINE__)
```

The reason for using two macros (`STRINGIFY` and `TOSTRING`) instead of one is due to the order of macro expansion in C++.

When a macro is expanded, its arguments are not immediately expanded. They are first substituted as is into the macro body. Only after this substitution are they expanded.

So, if you directly use `STRINGIFY(__LINE__)`, `__LINE__` is substituted as is into the macro body, and you end up with the string `"__LINE__"` instead of the string representation of the current line number.

By using two macros (`TOSTRING(x)` and `STRINGIFY(x)`), you ensure that `__LINE__` is fully expanded to the line number before it's converted to a string. `TOSTRING(__LINE__)` first substitutes `__LINE__` with the line number, and then `STRINGIFY` converts this number to a string.
