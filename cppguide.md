# Leka C++ Style Guide

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

[TOC]

## References

A lot of content in this style guide has been happily copied (and modified) from several sources. It's always good to get a bigger picture and see how others are doing it. We highly encourage you to take a look at those:

- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#c-core-guidelines) -  Licensed under an [MIT-style license](https://isocpp.github.io/CppCoreGuidelines/LICENSE)
- [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) - Licensed under a [Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/)

## Goals of the Style Guide

> **Why do we have this document?**

There are a few core goals that we believe this guide should serve. These are the fundamental **why**s that underlie all of the individual rules. By bringing these ideas to the fore, we hope to ground discussions and make it clearer to our broader community why the rules are in place and why particular decisions have been made. If you understand what goals each rule is serving, it should be clearer to everyone when a rule may be waived (some can be), and what sort of argument or alternative would be necessary to change a rule in the guide.

The goals of the style guide as we currently see them are as follows:

> Style rules should pull their weight

The benefit of a style rule must be large enough to justify asking all of our engineers to remember it. The benefit is measured relative to the codebase we would get without the rule, so a rule against a very harmful practice may still have a small benefit if people are unlikely to do it anyway. This principle mostly explains the rules we don’t have, rather than the rules we do: for example, goto contravenes many of the following principles, but is already vanishingly rare, so the Style Guide doesn’t discuss it.

> Optimize for the reader, not the writer

Our codebase (and most individual components submitted to it) is expected to continue for quite some time. As a result, more time will be spent reading most of our code than writing it. We explicitly choose to optimize for the experience of our average software engineer reading, maintaining, and debugging code in our codebase rather than ease when writing said code. "*Leave a trace for the reader*" is a particularly common sub-point of this principle: When something surprising or unusual is happening in a snippet of code (for example, transfer of pointer ownership), leaving textual hints for the reader at the point of use is valuable (e.g., `std::unique_ptr` demonstrates the ownership transfer unambiguously at the call site).

> Be consistent with existing code

Using one style consistently through our codebase lets us focus on other (more important) issues. Consistency also allows for automation: tools that format your code or adjust your `#includes` only work properly when your code is consistent with the expectations of the tooling. In many cases, rules that are attributed to "Be Consistent" boil down to "Just pick one and stop worrying about it"; the potential value of allowing flexibility on these points is outweighed by the cost of having people argue over them.

> Be consistent with the broader C++ community when appropriate

Consistency with the way other organizations use C++ has value for the same reasons as consistency within our code base. If a feature in the C++ standard solves a problem, or if some idiom is widely known and accepted, that's an argument for using it. However, sometimes standard features and idioms are flawed, or were just designed without our codebase's needs in mind. In those cases (as described below) it's appropriate to constrain or ban standard features. In some cases we prefer a homegrown or third-party library over a library defined in the C++ Standard, either out of perceived superiority or insufficient value to transition the codebase to the standard interface.

> Avoid surprising or dangerous constructs

C++ has features that are more surprising or dangerous than one might think at a glance. Some style guide restrictions are in place to prevent falling into these pitfalls. There is a high bar for style guide waivers on such restrictions, because waiving such rules often directly risks compromising program correctness.

> Avoid constructs that our average C++ programmer would find tricky or hard to maintain

C++ has features that may not be generally appropriate because of the complexity they introduce to the code. In widely used code, it may be more acceptable to use trickier language constructs, because any benefits of more complex implementation are multiplied widely by usage, and the cost in understanding the complexity does not need to be paid again when working with new portions of the codebase. When in doubt, waivers to rules of this type can be sought by asking your project leads. This is specifically important for our codebase because code ownership and team membership changes over time: even if everyone that works with some piece of code currently understands it, such understanding is not guaranteed to hold a few years from now.

> Concede to optimization when necessary

Performance optimizations can sometimes be necessary and appropriate, even when they conflict with the other principles of this document.

The intent of this document is to provide maximal guidance with reasonable restriction. As always, common sense and good taste should prevail. By this we specifically refer to the established conventions of the entire C++ community, not just your personal preferences or those of your team. Be skeptical about and reluctant to use clever or unusual constructs: the absence of a prohibition is not the same as a license to proceed. Use your judgment, and if you are unsure, please don't hesitate to ask your project leads to get additional input.

## C++ Version

Code should be in par with [Mbed OS](https://github.com/armmbed/mbed-os) minimum supported versions:

- `-std=gnu11`
- `-std=gnu++17`

The C++ version targeted by this guide will advance (aggressively) over time. C++17 features can be tested if they provide a real advantage demonstrated by careful benchmark.

## Clang Format

// TODO:

## ISO C++ Rules

- T.43: Prefer `using` over `typedef` for defining aliases - [[link](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#t43-prefer-using-over-typedef-for-defining-aliases)]

## Header Files

In general, every `.cpp` file should have an associated `.h` file. There are some common exceptions, such as unit tests and small `.cpp` files containing just a `main()` function.

Correct use of header files can make a huge difference to the readability, size and performance of your code.

The following rules will guide you through the various pitfalls of using header files.

### Self-contained Headers

Header files should be [self-contained](https://stackoverflow.com/a/1892054/2205264) (compile on their own) and end in `.h`.

All header files should be self-contained. Users and refactoring tools should not have to adhere to special conditions to include the header. Specifically, a header should have [header guards](#the-define-guard) and include all other headers it needs.

Prefer placing the definitions for template and inline functions in the same file as their declarations. The definitions of these constructs must be included into every `.cpp` file that uses them, or the program may fail to link in some build configurations. If declarations and definitions are in different files, including the former should transitively include the latter.

As an exception, a template that is explicitly instantiated for all relevant sets of template arguments, or that is a private implementation detail of a class, is allowed to be defined in the one and only `.cpp` file that instantiates the template.

### The `#define` Guard

All header files should have #define guards to prevent multiple inclusion. The format of the symbol name should be `<PROJECT>_<PATH>_<FILE>_H_`.

To guarantee uniqueness, they should be based on the full path in a project's source tree. For example, the file `LekaOS/lib/bar/baz.h` in project foo should have the following guard:

```cpp
#ifndef LEKA_OS_BAR_BAZ_H_
#define LEKA_OS_BAR_BAZ_H_

// ...

#endif  // LEKA_OS_BAR_BAZ_H_
```

### Inline Functions

Define functions inline only when they are small, say, 10 lines or fewer.

**Definition:**

You can declare functions in a way that allows the compiler to expand them inline rather than calling them through the usual function call mechanism.

**Pros:**

Inlining a function can generate more efficient object code, as long as the inlined function is small. Feel free to inline accessors and mutators, and other short, performance-critical functions.

**Cons:**

Overuse of inlining can actually make programs slower. Depending on a function's size, inlining it can cause the code size to increase or decrease. Inlining a very small accessor function will usually decrease code size while inlining a very large function can dramatically increase code size. On modern processors smaller code usually runs faster due to better use of the instruction cache.

**Decision:**

A decent rule of thumb is to not inline a function if it is more than 10 lines long. Beware of destructors, which are often longer than they appear because of implicit member- and base-destructor calls!

Another useful rule of thumb: it's typically not cost effective to inline functions with loops or switch statements (unless, in the common case, the loop or switch statement is never executed).

It is important to know that functions are not always inlined even if they are declared as such; for example, virtual and recursive functions are not normally inlined. Usually recursive functions should not be inline. The main reason for making a virtual function inline is to place its definition in the class, either for convenience or to document its behavior, e.g., for accessors and mutators.

## Scoping

### Unnamed Namespaces and Static Variables

When definitions in a `.cpp` file do not need to be referenced outside that file, place them in an unnamed namespace or declare them `static`. *Do not* use either of these constructs in `.h` files.

**Definition:**

All declarations can be given internal linkage by placing them in unnamed namespaces. Functions and variables can also be given internal linkage by declaring them `static`. This means that anything you're declaring can't be accessed from another file. If a different file declares something with the same name, then the two entities are completely independent.

**Decision:**

Use of internal linkage in *.cpp* files is encouraged for all code that does not need to be referenced elsewhere. *Do not* use internal linkage in `.h` files.

Format unnamed namespaces like named namespaces. In the terminating comment, leave the namespace name empty:

```cpp
namespace {
...
}  // namespace
```

### Nonmember, Static Member, and Global Functions

Prefer placing nonmember functions in a namespace; use completely global functions rarely. Do not use a class simply to group static functions. Static methods of a class should generally be closely related to instances of the class or the class's static data.

**Pros:**

Nonmember and static member functions can be useful in some situations. Putting nonmember functions in a namespace avoids polluting the global namespace.

**Cons:**

Nonmember and static member functions may make more sense as members of a new class, especially if they access external resources or have significant dependencies.

**Decision:**

Sometimes it is useful to define a function not bound to a class instance. Such a function can be either a static member or a nonmember function. Nonmember functions should not depend on external variables, and should nearly always exist in a namespace. Do not create classes only to group static member functions; this is no different than just giving the function names a common prefix, and such grouping is usually unnecessary anyway.

If you define a nonmember function and it is only needed in its `.cpp` file, use [internal linkage](#unamed-namespaces-and-static-variables) to limit its scope.

### Local Variables

Place a function's variables in the narrowest scope possible, and initialize variables in the declaration.

C++ allows you to declare variables anywhere in a function. We encourage you to declare them in as local a scope as possible, and as close to the first use as possible. This makes it easier for the reader to find the declaration and see what type the variable is and what it was initialized to. In particular, initialization should be used instead of declaration and assignment, e.g.:

```cpp
int i;
i = f();      // Bad -- initialization separate from declaration.
int j = g();  // Good -- declaration has initialization.
```

```cpp
std::vector<int> v;
v.push_back(1);               // Bad -- Prefer initializing using brace initialization.
v.push_back(2);
std::vector<int> w = {1, 2};  // Good -- w starts initialized.
```

Variables needed for if, while and for statements should normally be declared within those statements, so that such variables are confined to those scopes. E.g.:

```cpp
while (const char* p = strchr(str, '/')) {
    str = p + 1;
}
```

There is one caveat: if the variable is an object, its constructor is invoked every time it enters scope and is created, and its destructor is invoked every time it goes out of scope.

```cpp
// Inefficient implementation:
for (int i = 0; i < 1000000; ++i) {
  Foo f;  // My ctor and dtor get called 1000000 times each.
  f.doSomething(i);
}
```

It may be more efficient to declare such a variable used in a loop outside that loop:

```cpp
Foo f;  // My ctor and dtor get called once each.
for (int i = 0; i < 1000000; ++i) {
  f.doSomething(i);
}
```

## Classes

Classes are the fundamental unit of code in C++. Naturally, we use them extensively. This section lists the main dos and don'ts you should follow when writing a class.

### Doing Work in Constructors

Avoid virtual method calls in constructors, and avoid initialization that can fail if you can't signal an error.

**Definition:**

It is possible to perform arbitrary initialization in the body of the constructor.

**Pros:**

- No need to worry about whether the class has been initialized or not.
- Objects that are fully initialized by constructor call can be const and may also be easier to use with standard containers or algorithms.

**Cons:**

- If the work calls virtual functions, these calls will not get dispatched to the subclass implementations. Future modification to your class can quietly introduce this problem even if your class is not currently subclassed, causing much confusion.
- There is no easy way for constructors to signal errors, short of crashing the program (not always appropriate) or using exceptions (which are [forbidden](#exceptions)).
- If the work fails, we now have an object whose initialization code failed, so it may be an unusual state requiring a `bool isValid()` state checking mechanism (or similar) which is easy to forget to call.
- You cannot take the address of a constructor, so whatever work is done in the constructor cannot easily be handed off to, for example, another thread.

**Decision:**

Constructors should never call virtual functions. If appropriate for your code, terminating the program may be an appropriate error handling response. Otherwise, consider a `factory function` or `init()` method. Avoid `init()` methods on objects with no other states that affect which public methods may be called (semi-constructed objects of this form are particularly hard to work with correctly).

### Structs vs. Classes

Use a `struct` only for passive objects that carry data; everything else is a `class`.

The `struct` and `class` keywords behave almost identically in C++. We add our own semantic meanings to each keyword, so you should use the appropriate keyword for the data-type you are defining.

`structs` should be used for passive objects that carry data, and may have associated constants, but lack any functionality other than access/setting the data members. All fields must be public, and accessed directly rather than through getter/setter methods. The `struct` must not have invariants that imply relationships between different fields, since direct user access to those fields may break those invariants. Methods should not provide behavior but should only be used to set up the data members, e.g., constructor, destructor, `initialize()`, `reset()`.

If more functionality or invariants are required, a `class` is more appropriate. If in doubt, make it a `class`.

Note that member variables in structs and classes have [different naming rules](#naming-rules).

### Structs vs. Pairs and Tuples

Prefer to use a `struct` instead of a pair or a tuple whenever the elements can have meaningful names.

While using pairs and tuples can avoid the need to define a custom type, potentially saving work when *writing* code, a meaningful field name will almost always be much clearer when *reading* code than `.first`, `.second`, or `std::get<X>`. While C++14's introduction of `std::get<Type>` to access a tuple element by type rather than index (when the type is unique) can sometimes partially mitigate this, a field name is usually substantially clearer and more informative than a type.

Pairs and tuples may be appropriate in generic code where there are not specific meanings for the elements of the pair or tuple. Their use may also be required in order to interoperate with existing code or APIs.

### Inheritance

Composition is often more appropriate than inheritance. When using inheritance, make it `public`.

**Definition:**

When a sub-class inherits from a base class, it includes the definitions of all the data and operations that the base class defines. "Interface inheritance" is inheritance from a pure abstract base class (one with no state or defined methods); all other inheritance is "implementation inheritance".

**Pros:**

Implementation inheritance reduces code size by re-using the base class code as it specializes an existing type. Because inheritance is a compile-time declaration, you and the compiler can understand the operation and detect errors. Interface inheritance can be used to programmatically enforce that a class expose a particular API. Again, the compiler can detect errors, in this case, when a class does not define a necessary method of the API.

**Cons:**

For implementation inheritance, because the code implementing a sub-class is spread between the base and the sub-class, it can be more difficult to understand an implementation. The sub-class cannot override functions that are not virtual, so the sub-class cannot change implementation.

Multiple inheritance is especially problematic, because it often imposes a higher performance overhead (in fact, the performance drop from single inheritance to multiple inheritance can often be greater than the performance drop from ordinary to virtual dispatch), and because it risks leading to "diamond" inheritance patterns, which are prone to ambiguity, confusion, and outright bugs.

**Decision:**

All inheritance should be `public`. If you want to do private inheritance, you should be including an instance of the base class as a member instead.

Do not overuse implementation inheritance. *Composition is often more appropriate*. Try to restrict use of inheritance to the "is-a" case: `Bar` subclasses `Foo` if it can reasonably be said that `Bar` "is a kind of" `Foo`.

Limit the use of `protected` to those member functions that might need to be accessed from subclasses. Note that [data members should be private](#access-control).

Explicitly annotate overrides of virtual functions or virtual destructors with exactly one of an `override` or (less frequently) `final` specifier. Do not use `virtual` when declaring an override. Rationale: A function or destructor marked override or final that is not an override of a base class virtual function will not compile, and this helps catch common errors. The specifiers serve as documentation; if no specifier is present, the reader has to check all ancestors of the class in question to determine if the function or destructor is virtual or not.

Multiple inheritance is permitted, but multiple *implementation* inheritance is strongly discouraged

### Access Control

Make classes' data members `private`, unless they are constants. This simplifies reasoning about invariants, at the cost of some easy boilerplate in the form of accessors (usually `const`) if necessary.

### Declaration Order

Group similar declarations together, placing public parts earlier.

A class definition should usually start with a `public:` section, followed by `protected:`, then `private:`. Omit sections that would be empty.

Within each section, generally prefer grouping similar kinds of declarations together, and generally prefer the following order: types (including `typedef`, `using`, and nested structs and classes), constants, factory functions, constructors, assignment operators, destructor, all other methods, data members.

Do not put large method definitions inline in the class definition. Usually, only trivial or performance-critical, and very short, methods may be defined inline. See [Inline Functions](#inline-functions) for more details.

## Functions

### Output Parameters

The output of a C++ function is naturally provided via a return value and sometimes via output parameters.

Prefer using return values over output parameters: they improve readability, and often provide the same or better performance. If output-only parameters are used, they should appear after input parameters.

Parameters are either input to the function, output from the function, or both. Input parameters are usually values or `const` references, while output and input/output parameters will be pointers to non-`const`.

When ordering function parameters, put all input-only parameters before any output parameters. In particular, do not add new parameters to the end of the function just because they are new; place new input-only parameters before the output parameters.

This is not a hard-and-fast rule. Parameters that are both input and output (often classes/structs) muddy the waters, and, as always, consistency with related functions may require you to bend the rule.

### Write Short Functions

Prefer small and focused functions.

We recognize that long functions are sometimes appropriate, so no hard limit is placed on functions length. If a function exceeds about 40 lines, think about whether it can be broken up without harming the structure of the program.

Even if your long function works perfectly now, someone modifying it in a few months may add new behavior. This could result in bugs that are hard to find. Keeping your functions short and simple makes it easier for other people to read and modify your code. Small functions are also easier to test.

You could find long and complicated functions when working with some code. Do not be intimidated by modifying existing code: if working with such a function proves to be difficult, you find that errors are hard to debug, or you want to use a piece of it in several different contexts, consider breaking up the function into smaller and more manageable pieces.

### Reference Arguments

All parameters passed by lvalue reference must be labeled `const`.

**Definition:**

In C, if a function needs to modify a variable, the parameter must use a pointer, e.g., `int foo(int *pval)`. In C++, the function can alternatively declare a reference parameter: `int foo(int &val)`.

**Pros:**

- Defining a parameter as reference avoids ugly code like `(*pval)++`.
- Necessary for some applications like copy constructors.
- Makes it clear, unlike with pointers, that a null pointer is not a possible value.

**Cons:**

References can be confusing, as they have value syntax but pointer semantics.

**Decision:**

Within function parameter lists all references must be const:

```cpp
void Foo(const std::string &in, std::string *out);
```
In fact it is a very strong convention in Google code that input arguments are values or `const` references while output arguments are pointers. Input parameters may be `const` pointers, but we never allow non-`const` reference parameters except when required by convention, e.g., `swap()`.

However, there are some instances where using `const T*` is preferable to `const T&` for input parameters. For example:

- You want to pass in a null pointer.
- The function saves a pointer or reference to the input.

Remember that most of the time input parameters are going to be specified as `const T&`. Using `const T*` instead communicates to the reader that the input is somehow treated differently. So if you choose `const T*` rather than `const T&`, do so for a concrete reason; otherwise it will likely confuse readers by making them look for an explanation that doesn't exist.

### Function Overloading

Use overloaded functions (including constructors) only if a reader looking at a call site can get a good idea of what is happening without having to first figure out exactly which overload is being called.

**Definition:**

You may write a function that takes a const `std::string&` and overload it with another that takes `const char*`. However, in this case consider `std::string_view` instead.

```cpp
class MyClass {
 public:
  void Analyze(const std::string &text);
  void Analyze(const char *text, size_t textlen);
};
```

**Pros:**

Overloading can make code more intuitive by allowing an identically-named function to take different arguments. It may be necessary for templatized code, and it can be convenient for Visitors.

Overloading based on const or ref qualification may make utility code more usable, more efficient, or both. (See [TotW 148](https://abseil.io/tips/148), [CCG C.162](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#c162-overload-operations-that-are-roughly-equivalent) and [CCG C.163](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#c162-overload-operations-that-are-roughly-equivalent) for more.)

**Cons:**

If a function is overloaded by the argument types alone, a reader may have to understand C++'s complex matching rules in order to tell what's going on. Also many people are confused by the semantics of inheritance if a derived class overrides only some of the variants of a function.

**Decision:**

You may overload a function when there are no semantic differences between variants. These overloads may vary in types, qualifiers, or argument count. However, a reader of such a call must not need to know which member of the overload set is chosen, only that something from the set is being called. If you can document all entries in the overload set with a single comment in the header, that is a good sign that it is a well-designed overload set.

### Default Arguments

Default arguments are allowed on non-virtual functions when the default is guaranteed to always have the same value. Follow the same restrictions as for [function overloading](#function-overloading), and prefer overloaded functions if the readability gained with default arguments doesn't outweigh the downsides below.

**Pros:**

Often you have a function that uses default values, but occasionally you want to override the defaults. Default parameters allow an easy way to do this without having to define many functions for the rare exceptions. Compared to overloading the function, default arguments have a cleaner syntax, with less boilerplate and a clearer distinction between 'required' and 'optional' arguments.

**Cons:**

Defaulted arguments are another way to achieve the semantics of overloaded functions, so all the [reasons not to overload functions](#function-overloading) apply.

The defaults for arguments in a virtual function call are determined by the static type of the target object, and there's no guarantee that all overrides of a given function declare the same defaults.

Default parameters are re-evaluated at each call site, which can bloat the generated code. Readers may also expect the default's value to be fixed at the declaration instead of varying at each call.

Function pointers are confusing in the presence of default arguments, since the function signature often doesn't match the call signature. Adding function overloads avoids these problems.

**Decision:**

Default arguments are banned on virtual functions, where they don't work properly, and in cases where the specified default might not evaluate to the same value depending on when it was evaluated. (For example, don't write `void f(int n = counter++);`.)

Default arguments simply provide alternative interfaces to a single implementation. There is no guarantee that a set of overloaded functions all implement the same semantics. The use of default arguments can avoid code replication. (See [CCG F.51](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#f51-where-there-is-a-choice-prefer-default-arguments-over-overloading) for more.)

## Naming

The most important consistency rules are those that govern naming. The style of a name immediately informs us what sort of thing the named entity is: a type, a variable, a function, a constant, a macro, etc., without requiring us to search for the declaration of that entity. The pattern-matching engine in our brains relies a great deal on these naming rules.

Naming rules are pretty arbitrary, but we feel that consistency is more important than individual preferences in this area, so regardless of whether you find them sensible or not, *the rules are the rules*.

### General Naming Rules

Optimize for readability using names that would be clear even to people on a different team.

Use names that describe the purpose or intent of the object. Do not worry about saving horizontal space as it is far more important to make your code immediately understandable by a new reader. Minimize the use of abbreviations that would likely be unknown to someone outside your project (especially acronyms and initialisms). Do not abbreviate by deleting letters within a word. As a rule of thumb, an abbreviation is probably OK if it's listed in Wikipedia. Generally speaking, descriptiveness should be proportional to the name's scope of visibility. For example, n may be a fine name within a 5-line function, but within the scope of a class, it's likely too vague.

Exemple, bad: 👎

```cpp
class MyClass {
 public:
  int countFooErrors(const std::vector<Foo>& foos) {
    int total_number_of_foo_errors = 0;  // Overly verbose given limited scope and context
    for (int foo_index = 0; foo_index < foos.size(); ++foo_index) {  // Use idiomatic `i`
      ...
      ++total_number_of_foo_errors;
    }
    return total_number_of_foo_errors;
  }
  void doSomethingImportant() {
    int cstmr_id = ...;  // Deletes internal letters
  }
 private:
  const int kNum = ...;  // Unclear meaning within broad scope
};
```
Exemple, good: ✅

```cpp
class MyClass {
 public:
  int countFooErrors(const std::vector<Foo>& foos) {
    int n = 0;  // Clear meaning given limited scope and context
    for (const auto& foo : foos) {
      ...
      ++n;
    }
    return n;
  }
  void doSomethingImportant() {
    std::string fqdn = ...;  // Well-known abbreviation for Fully Qualified Domain Name
  }
 private:
  const int kMaxAllowedConnections = ...;  // Clear meaning within context
};
```

Note that certain universally-known abbreviations are OK, such as `i` for an iteration variable and `T` for a template parameter.

For the purposes of the naming rules below, a "word" is anything that you would write in English without internal spaces. This includes abbreviations and acronyms; e.g., for "camel case" or "Pascal case," in which the first letter of each word is capitalized, use a name like `startRpc()`, not `startRPC()`.

Template parameters should follow the naming style for their category: type template parameters should follow the rules for type names, and non-type template parameters should follow the rules for variable names.

### File Names

Filenames should written using [Pascal Case](https://wiki.c2.com/?PascalCase). Follow the convention that your project uses.

Examples of acceptable file names:

```
UseFullClass.cpp
LSM6DSOXDriver.cpp
Motors.cpp
Motors_test.cpp
```

C++ files should end in `.cpp` and header files should end in `.h`.

Do not use filenames that already exist in `/usr/include`, such as `db.h`.

In general, make your filenames very specific. For example, use `HttpServerLogs.h` rather than `Logs.h`.

A very common case is to have a pair of files called, e.g., `FooBar.h` and `FooBar.cpp`, defining a class called `FooBar`.

### Type Names

Type names are written using Pascal Case. They start with a capital letter and have a capital letter for each new word, with no underscores: `MyExcitingClass`, `MyExcitingEnum`.

The names of all types — classes, structs, type aliases, enums, and type template parameters — have the same naming convention. Type names should start with a capital letter and have a capital letter for each new word. No underscores. For example:

```cpp
// classes and structs
class UrlTable { ...
class UrlTableTester { ...
struct UrlTableProperties { ...

// typedefs
typedef hash_map<UrlTableProperties *, std::string> PropertiesMap;

// using aliases
using PropertiesMap = hash_map<UrlTableProperties *, std::string>;

// enums
enum UrlTableErrors { ...
```

### Variable Names

The names of variables (including function parameters) and data members are all lowercase, with underscores between words. Data members of classes (but not structs) additionally have leading underscores. For instance: `a_local_variable`, `a_struct_data_member`, `_a_class_data_member`.

#### Common Variable Names

For example:

```cpp
std::string table_name;  // OK - lowercase with underscore.
std::string tableName;   // Bad - mixed case.
```

A generic variable must have the same name as its type. Reducing the number of terms used reduces complexity. It also helps deduce the type of the variable from its name. Most of the time if this rule does not fit well, it's because the name of the type was not well chosen to begin with.

```cpp
void setTopic(Topic * topic);
void connect(Database * database);

Point start_point, center_point;
Name login_name;
```

#### Class Data Members

Data members of classes, both static and non-static, are named like ordinary nonmember variables, but with a leading underscore.

```cpp
class TableInfo {
  ...
 private:
  std::string _table_name;        // OK - leading underscore.
  static Pool<TableInfo>* _pool;  // OK.
};
```

#### Struct Data Members

Data members of structs, both static and non-static, are named like ordinary nonmember variables. They do not have the leading underscores that data members in classes have.

```cpp
struct UrlTableProperties {
  std::string name;
  int num_entries;
  static Pool<UrlTableProperties>* pool;
};
```

See [Structs vs. Classes](#struct-vs-classes) for a discussion of when to use a `struct` versus a `class`.

### Constant Names

Variables declared `constexpr` or `const`, and whose value is fixed for the duration of the program, are named with a leading `k` followed by mixed case. Underscores can be used as separators in the rare cases where capitalization cannot be used for separation. For example:

```cpp
const int kDaysInAWeek = 7;
const int kLekaOS1_0_0 = 24;  // LekaOS 1.0.0
```

All such variables with static storage duration (i.e. statics and globals, see [Storage Duration](https://en.cppreference.com/w/cpp/language/storage_duration#Storage_duration) for details) should be named this way. This convention is optional for variables of other storage classes, e.g. automatic variables, otherwise the usual variable naming rules apply.

### Function Names

Regular functions are written using [camel Case](https://en.wikipedia.org/wiki/Camel_case); accessors and mutators may be named like variables.

Ordinarily, functions should start with a lowercase letter and have a capital letter for each new word.

```cpp
addTableEntry()
deleteUrl()
openFileOrDie()
```

(The same naming rule applies to class- and namespace-scope constants that are exposed as part of an API and that are intended to look like functions, because the fact that they're objects rather than functions is an unimportant implementation detail.)

Accessors and mutators (get and set functions) may be named like variables if it helps with readability. These often correspond to actual member variables, but this is not required. For example, `int count()`, `int get_count()` and `void set_count(int count)`.

### Enumerator Names

Enumerators (for both scoped and unscoped enums) should be named either like [constants](#constant-names) or like [macros](#macros-names): either `kEnumName` or `ENUM_NAME`.

Preferably, the individual enumerators should be named like constants. However, it is also acceptable to name them like macros. The enumeration name, `UrlTableErrors` (and `AlternateUrlTableErrors`), is a type, and therefore mixed case.

Note, `enum class` is preferred over `enum`. See [Bjarne's *enum class -- scoped and strongly typed enums*](http://www.stroustrup.com/C++11FAQ.html#enum) for why.

```cpp
enum class UrlTableErrors {
  kOk = 0,
  kOutOfMemory,
  kMalformedInput,
};

enum class AlternateUrlTableErrors {
  OK = 0,
  OUT_OF_MEMORY = 1,
  MALFORMED_INPUT = 2,
};
```

### Macro Names

You're not really going to define a macro, are you? If you do, they're like this: `MY_MACRO_THAT_SCARES_SMALL_CHILDREN_AND_ADULTS_ALIKE`.

Please see the description of macros; in general macros should not be used. However, if they are absolutely needed, then they should be named with all capitals and underscores.

```cpp
#define ROUND(x) ...
#define PI_ROUNDED 3.0
```

### Exceptions to Naming Rules

If you are naming something that is analogous to an existing C or C++ entity then you can follow the existing naming convention scheme.

- `bigopen()` - function name, follows form of `open()`
- `uint` - `typedef`
- `bigpos` - `struct` or `class`, follows form of `pos`
- `sparse_hash_map` - STL-like entity; follows STL naming conventions
- `LONGLONG_MAX` - a constant, as in `INT_MAX`
