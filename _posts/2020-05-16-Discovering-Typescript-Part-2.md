---
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      read_time: true
      comments: true
      share: true
      related: true
---

This is part 2 of 2 about my Typescript discoveries. If you haven't read part 1, you can read it [here](https://deanagan.github.io/Discovering-Typescript-Part-1/).

So this entry will be about classes. Object oriented languages have a lot of similarities and Typescript is no different.

## Here's a list of what I'll cover in this blog post:
1. [Constructors and Implicit Properties](#constructors-and-implicit-properties)
2. [Class and Method Access](#class-and-method-access)
3. [Object Literals](#object-literals)
4. [Prototypal Inheritance](#prototypal-inheritance)
5. [Interfaces](#interfaces)



## Constructors and Implicit Properties

Coming from C# or other C-based OOP languages, we get so used to having constructors having the same name as the class name. In Typescript, it's different.

Our constructor would look like this:

```
class Book {
    public salesRank: number;
    public title: string;
    private isbn: string;

    constructor(salesRank: number, title: string, isbn: string) {
        this.salesRank = salesRank;
        this.title = title;
        this.isbn = isbn;
    }

    description() {
        return `${this.title} ISBN: ${this.isbn} Rank# ${this.salesRank}`
    }
}
```

Alternatively, this would look cleaner if we use ***implicit properties***. In that way, we observe the [DRY principle](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). However, this is really a matter of personal preference.

```
class Book {
    constructor(
        public salesRank: number,
        public title: string,
        private isbn: string
    )
    {}

    description() {
        return `${this.title} ISBN: ${this.isbn} Rank# ${this.salesRank}`
    }
}
```
## Class And Method Access

 Coming from a C# background, default(unspecified) access in classes that are not nested within other classes/structs are always internal. In Typescript, classes have public access by default.
```
class Patient {

    constructor(
        public firstName: string,
        public lastName: string,
        public email: string,
        private age: number
    )
    {}


    greet() {
        console.log(`Hello ${this.firstName}`);
        console.log(`Year Born: ${this.yearBorn()}`);
    }

    private yearBorn() : number {
        let date: Date = new Date();
        return date.getFullYear() - this.age;
    }
}
```

The `greet` function above is public, and accessible:
```
var patientA = new Patient("Joe", "Black", "joe_black@awesomeemail.com", 24);
patientA.greet();
>>> Hello Joe
Year Born: 1996
```

If we try to access `yearBorn()` outside its scope,  we get an error:
```
patientA.yearBorn();
>>> error TS2341: Property 'yearBorn' is private and only accessible within class 'Patient'.
```

## Object Literals
There are cases in Javascript where we just want something short and simple. Essentially, an object without any intent of creating any prototypal inheritance.

Object literals to the rescue! Actually, object literals are already in Javascript, but nice to know we have it here in Typescript.
```
const rectangle = {
    height: 20,
    width : 40,
    area: function() { return this.width * this.height; }
}
```

## Prototypal Inheritance
Being an object oriented language, inheritance also exists in Typescript.

Here's how:
```
class OutPatient extends Patient {
    constructor(
        fn: string, ln: string, email: string, age: number
    ) {
        super(fn, ln, email, age);
    }

    greet() {
        super.greet();
        console.log(`Welcome to outpatient services, ${this.firstName}. Please pick a number.`);
    }
}
```

To derive from a base class, we simply use the `extends` keyword. If the base class has a constructor, we can construct the base class by using the `super` keyword, and we're all set.

Overriding methods are straightforward. If we need to call a method from the parent class, we just use the `super` keyword.

## Interfaces
 Any object oriented language wouldn't be complete without interfaces. As OOP coders, we always have that principle of writing to abstractions rather than concrete implementations (or dependency inversion if you prefer the 'D' in SOLID principles).

 So with that, here's what our Typescript interface looks like:
 ```
 interface Product {
    name: string;
    sku: string;
    id: number;
    discount(): number;
}
```

So the above code tells us that name, sku and id are properties and that discount is a method.

Writing against this interface, this would be:
```
class Chair implements Product {
    constructor(
        public name: string,
        public sku: string,
        public id: number
    ) {

    }

    discount() : number {
        return 0.25;
    }
}
```
