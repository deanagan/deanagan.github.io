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

Today, I decided to learn Typescript, and here are 6 items I learned. These are not meant to be comprehensive coverage of the language.

## 1. Variables and Functions
Being a transpiler for Javascript, it is expected to be closer to how Javascript does its function.
Typescript functions look like this:
```typescript
function add(addend1: number, addend2:number) : number {
    return addend1 + addend2;
}
```
The function `add` takes 2 numbers and returns their sum. Typescript treats both floats and integers as numbers. So a float works too for this function.
```typescript
console.log(`Sum of numbers ${add(434,344)}`);
console.log(`Sum of floating point numbers ${add(43.4, 23.4)}`);
```
```
>>> Sum of numbers 778
>>> Sum of floating point numbers 66.8
```
The `: number` bit is the return type of the function.

## 2. String Interpolation or Formatted String Literals

Other languages like C#6's string interpolation and Python 3.6's formatted string literals make it quite convenient to create formatted strings.

Check out below on how other languages do it:

**C#**
```
int x = 4;
int y = 5;
Console.WriteLine($"The sum of {x} and {y} is {x+y}");
>>> The sum of 4 and 5 is 9
```
**Python**
```python
x,y = 4,5
print(f"The sum of {x} and {y} is {x+y}")
>>> The sum of 4 and 5 is 9
```

We can do something similar in typescript/javascript, except it is per variable. This is called *"template literals"*.

This works using backticks and wrapping each variable with `${}`.
```
console.log(`The sum of ${a} and ${b} is ${a+b}`);
>>> The sum of 2 and 5 is 7
```

## 3. For Loops

This one caught me by surprise when I tried passing the contents of a number list into a function.

Suppose, I have a function that contains a switch like below:
```typescript
function giveMe(what:number) {
    switch(what) {
        case 1:
            console.log("you gave me 1");
            break;
        case 2:
            console.log("you gave me 2");
            break;
        default:
            console.log("you gave me more!");
    }
}
```
then I supply numbers from a list:
```typescript
let nums = [1,2,3];

for(var n of nums) {
    giveMe(n);
}
```
Note the ***of*** in the loop above. This can be surprising for C# and Python developers who get so used to using the preposition ***in*** in loops like:

**C#**
```
foreach(var number in numbers) {
    GiveMe(number);
}
```

**Python**
```python
for number in numbers:
    give_me(number)
```
Doing something similar in Typescript like below:
```typescript
for(var n in nums) {
    giveMe(n);
}
```

Will give you a type error: `Argument of type 'string' is not assignable to parameter of type 'number'`. This is because ***in*** is meant to iterate over object properties, and thus has a type **string**.

## 4. Parameters

Like C# and Python, Typescript has optional and default parameters.

Typescript has it as such for optional parameters:
```typescript
function increase(num: number, inc?:number) : number {
    if (inc != undefined) {
        return num + inc;
    }

    return num + 1;
}

console.log(increase(10));
console.log(increase(10,2));
>>> 11
>>> 12
```

`inc?: number` is an optional parameter.

For default parameters, it goes like:
```typescript
function increase(num: number, inc:number = 1) : number {
    return num + inc;
}

console.log(increase(10));
console.log(increase(10,2));
>>> 11
>>> 12
```

With C#, there is a `params` keyword that can be useful for taking a variable number of arguments.

**C#**
```
public static int GetTotal(params int[] addends) {
    return addends.Sum();
}

GetTotal(1,2,3,4,5);
>>> 15
```
In Python, its equivalent for taking a variable number of arguments:
**Python**
```python
def get_total(*addends):
	return sum(addends)

get_total(1,2,3,4,5)
>>> 15
```


Similarly, this can be achieved in **Typescript** using the spread operator:
```typescript
function getTotal(...addends:number[]) : number {
    let total = 0;
    for (var n of addends) {
        total += n;
    }
    // Or if you like using Javascript's functional operations
    // n.reduce(function(total, cv) {
    //    return total += cv;
    //}, 0);

    return total;
}

getTotal(1,2,3,4,5);
>>> 15
```
These are called **rest parameters** in Typescript.

## 5. Function Overloading

Typescript also supports function overloading. It is somewhat very unique in my opinion when comparing it to other object-oriented languages.

Python, being a dynamic language does not have a concept of function overloading (we do have multiple dispatch in Python, but let's not go there).

So, let's take as an example, **C#**:
```
public static int Add(int a, int b) {
    return a + b;
}

public static int Add(int a, int b, int c) {
        return a + b + c;
}
```

However, with **typescript**, it works differently. We have to do it by writing a method declaration for each overload first, then implement the method.

So we make this for each overload like:
```typescript
function add(a: number, b: number) : number;
function add(a: number, b: number, c: number) : number;
```

Then we implement the method/function:
```typescript
function add(a: number, b: number, c?: number) : number {
    let total = a + b;

    if (c != undefined) {
        total += c;
    }
    return total;
}

console.log(add(1,2,3));
console.log(add(1,2));
```
In some cases, we may end up checking the type of the argument. As an example, if we had a method that had different types:
```typescript
function identification(id: string) : string;
function identification(id: number): string;

function identification(value: (string | number)): string {
    switch(typeof value) {
        case "string":
            return `My id is ${value}`;
        case "number":
            return `My id is XX-${value}`;
    }
}

console.log(identification("XX-123"));
console.log(identification(123));
// >>> My id is XX-123
// >>> My id is XX-123
```

## 6. Arrow Functions

Arrow functions in typescript are very similar to C#.
```
let greetFn = (name:string) => console.log(`Hello ${name}`);
greetFn("Bob");
```
As a side note, I was quite surprised that there is a subtle difference when using arrow operators in Javascript ES6. Below is legal in ES6:
```typescript
let greetFn = name => console.log(`Hello ${name}`);
greetFn("Bob");
```
So with this, I thought I could do:
```typescript
// Note without parenthesis around name:string
let greetFn = name:string => console.log(`Hello ${name}`);
greetFn("Bob");
```

This, however, leads to an error, so I had to put back the parenthesis.


And that is it! Stay tuned for part 2, where I will share a few things I learned about classes in Typescript.