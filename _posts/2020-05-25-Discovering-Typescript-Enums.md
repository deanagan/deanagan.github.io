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

Coming from an OOP, C-based language (C++, C#), enums are essentials when it comes to making magical specific numbers into human-readable names. With Typescript, enums have some slightly added functionality than the other languages.

## 1. Enums Under The Hood

As we already know, Typescript code is compiled into Javascript. An enum declaration is no different.

If we had a Typescript enum below:

```
enum Fruit
{
    Apple,
    Mango,
    Banana,
    Total
}
```

it gets compiled to:

```
var Fruit;
(function (Fruit) {
    Fruit[Fruit["Apple"] = 0] = "Apple";
    Fruit[Fruit["Mango"] = 1] = "Mango";
    Fruit[Fruit["Banana"] = 2] = "Banana";
    Fruit[Fruit["Total"] = 3] = "Total";
})(Fruit || (Fruit = {}));
```

That Javascript code looks very interesting. The compiled Javascript is a mapping of strings to numbers and numbers to strings.

In terms of Javascript, the map `Fruit`, used like:
```
Fruit["Apple"] = 0;
```
above will mean, that `Fruit.Apple` or `Fruit["Apple"]` will return 0.

What is interesting in this code, is the reverse mapping part.

The `Fruit["Apple"] = 0` bit in:
```
Fruit[Fruit["Apple"] = 0] = "Apple";
```
is equivalent to `Fruit[0] = "Apple";`, thus, we can access the enum as `Fruit[0]`, and this returns a string if we use numerical indices to access the enum.

So, if we print out `Apple` in `Fruit`:
```
console.log(`An apple's numeric value is ${Fruit.Apple}`);
console.log(`An apple's numeric value is ${Fruit["Apple"]}`);
console.log(`An apple's string value is ${Fruit[0]}`);
```

Our output would be:
```
An apple's numeric value is 0
An apple's numeric value is 0
An apple's string value is Apple
```

## 2. Const Enums

Typescript also has const enums, which differs in how it compiles into Javascript. If we had the const enum below:
```
const enum ConstFruit
{
    Apple,
    Mango,
    Banana
}

console.log(`An apple's numeric value is ${ConstFruit.Apple}`);
console.log(`An mango's numeric value is ${ConstFruit["Mango"]}`);
console.log(`An banana's numeric value is ${ConstFruit["Banana"]}`);
```

It gets compiled to:
```
console.log("An apple's numeric value is " + 0 /* Apple */);
console.log("A mango's numeric value is " + 1 /* "Mango" */);
console.log("A banana's numeric value is " + 2 /* "Banana" */);
```
Notice the values are just inlined with comments beside them.
There is no lookup table generated.

So if we do `ConstFruit[0]`, we get an error:
```
"A const enum member can only be accessed using a string literal."
```

As expected, because the lookup table doesn't exist, we are getting an error.


## 3. Ambient Enums

Ambient enums are useful for when the enum type already exists elsewhere. This could be another library, perhaps an old Javascript one. An ambient enum is declared like:

```
declare enum AmbientFruit
{
    Apple,
    Mango,
    Banana
}
```
Its compiled Javascript looks like:
```
console.log("An apple's numeric value is " + AmbientFruit.Apple);
```

Ambient enums don't emit a lookup object. When I looked at the value of AmbientFruit.Apple as Javascript code, it is like telling the compiler that I have this type somewhere, and you can go ahead and compile it. If the type doesn't really exist, then we get a runtime error.

## 4. Preserve Const Enum Flag

As discussed in item 2, const enums don't emit a lookup object. However, when compiling with `--preserveConstEnums`, the compiler will emit a lookup object. However, the values are still inlined.

So when we have:
```
const enum ConstFruit
{
    Apple,
    Mango,
    Banana
}

console.log(`An apple's numeric value is ${ConstFruit.Apple}`);
console.log(`A mango's numeric value is ${ConstFruit["Mango"]}`);
```
the compiled Javascript is:
```
var ConstFruit;
(function (ConstFruit) {
    ConstFruit[ConstFruit["Apple"] = 0] = "Apple";
    ConstFruit[ConstFruit["Mango"] = 1] = "Mango";
    ConstFruit[ConstFruit["Banana"] = 2] = "Banana";
})(ConstFruit || (ConstFruit = {}));
console.log("An apple's numeric value is " + 0 /* Apple */);
console.log("A mango's numeric value is " + 1 /* "Mango" */);
```

Note that this flag doesn't affect ambient enums.

## 5. String Enums

Lastly, in [Typescript 2.4](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-4.html), string enums were introduced.


We declare string enums:
```
enum FruitStrings
{
    APPLE = "Apple",
    MANGO = "Mango",
    BANANA = "Banana"
}
console.log(`An apple's ${typeof(FruitStrings.APPLE)} value is "${FruitStrings.APPLE}"`);
```
The compiled Javascript will look like:
```
var FruitStrings;
(function (FruitStrings) {
    FruitStrings["APPLE"] = "Apple";
    FruitStrings["MANGO"] = "Mango";
    FruitStrings["BANANA"] = "Banana";
})(FruitStrings || (FruitStrings = {}));
console.log("An apple's " + typeof (FruitStrings.APPLE) + " value is \"" + FruitStrings.APPLE + "\"");
```

Notice that the numerical value is gone. It also doesn't have a reverse lookup. So `let t = FruitStrings["Apple"];` will produce an error:
 `Property 'Apple' does not exist on type 'typeof FruitStrings'. Did you mean 'APPLE'?`


And that's it! I liked the idea of a reverse lookup for enums. It makes it easier to loop through enum values if we ever have to.
