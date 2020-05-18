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
1. [Constructors and Implicit Properties](#Constructors-and-Implicit-Properties)
2. [Object Literals](#Object-Literals)
3. [Class Access](#Class-Access)
4. [Implicit Properties](#Implicit-Properties)
5. [Inheritance](#Inheritance)
6. [Interfaces](#Interfaces)
7. [Modules](#Modules)


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


## 2. Object Literals

## 3. Class Access

 In typescript, classes have public access by default. Coming from a C# background, default access in classes are always internal.

## 4. Implicit Properties

## 5. Inheritance

## 6. Interfaces


## 7. Modules
