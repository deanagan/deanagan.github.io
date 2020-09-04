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

In this post, we'll talk about 3 operators we can use in Typescript. These operators are also available in Javascript.


## Sample Problem

We want to extract and add up numbers in a string. If there are no numbers, then return 0.
For example, if we have the string:

*"99 bottles of beer on the wall, take 1 down pass it around"*

We want the function to return 100, where 99 and 1 are added together.

So in a map-reduce approach, this is my solution. (Also, as a first step, we had to split the string using space as a delimiter.).

```javascript
export function sum(input: string): number {
  return input.split(' ')
              .map(word => Number(word) || 0)
              .reduce((total, current) => total + current, 0);
}
```

So to make sure our function is correct, we write a couple of tests:
```javascript
  describe('Test sum of numbers', () => {
    it('should return 100 bottles of beer', () => {
      expect(sum('99 bottles of beer on the wall, take 1 down pass it around'))
        .to.be.equal(100);
    });

    it('should return 0 for no more bottles of beer', () => {
      expect(sum('No more bottles of beer on the wall, no more bottles of beer'))
        .to.be.equal(0);
    });
  });
```
```
  Test sum of numbers
    √ should return 100
    √ should return 0 for no more bottles of beer


  2 passing (9ms)
```


## Unary Plus Operator

The `Number` function was my typical way to go in Typescript. But I discovered that we can also use the shorter unary plus operator.

With that, we modify our code so it is shorter:
```javascript
export function sum(input: string): number {
  return input.split(' ')
              .map(word => +word || 0)
              .reduce((total, current) => total + current, 0);
}
```
Note that we've simply replaced `Number(word)` with just `+word`. It works just the same and still passes all the test.

## Optional Chaining Operator

So what if the requirements change where the input string is now optional? We have to now deal with the input possibly being `undefined`. Let's say, we just return `undefined` if there is no input string.

With that, let's change our function:
```javascript
export function sum(input?: string): number | undefined {
  if (input) {
    return undefined;
  }
  return input.split(' ')
              .map(word => +word || 0)
              .reduce((total, current) => total + current, 0);
}
```

Notice, we also had to add `| undefined` for the return type as we want our return to be either a number or undefined.

Adding a unit test for it, and we still pass:
```javascript
  describe('Test sum of numbers', () => {
    it('should return 100 bottles of beer', () => {
      expect(sum('99 bottles of beer on the wall, take 1 down pass it around'))
        .to.be.equal(100);
    });

    it('should return 0 for no more bottles of beer', () => {
      expect(sum('No more bottles of beer on the wall, no more bottles of beer'))
        .to.be.equal(0);
    });

    it('should return undefined for no argument', () => {
      expect(sum()).to.be.undefined;
    });
  });
```
```
  Test sum of numbers
    √ should return 100 bottles of beer
    √ should return 0 for no more bottles of beer
    √ should return undefined for no argument


  3 passing (9ms)
```

And now comes the optional chaining operator. We can get rid of the `if` clause we added by using the optional chaining operator. This will make it cleaner.
```javascript
export function sum(input?: string): number | undefined {
  return input?.split(' ')
              .map(word => +word || 0)
              .reduce((total, current) => total + current, 0);

}
```

Optional chaining lets us write code that immediately stops running if we encounter `null` or `undefined`.

## Nullish Coalescing

So now, a new requirement came in! Instead of returning `undefined`, we must return zero instead if there is no argument. To accomodate the new requirement, we've now changed our code to:
```javascript
export function sum(input?: string): number {
  if (input) {
    return input.split(' ')
              .map(word => +word || 0)
              .reduce((total, current) => total + current, 0)
  }

  return 0;
```

This is where the nullish coalescing operator comes very handy. The nullish coalescing operator lets us fallback to a default value if the left side of it is `null` or `undefined`.

With this operator, our code will be:
```javascript
export function sum(input?: string): number {

  return input?.split(' ')
              .map(word => +word || 0)
              .reduce((total, current) => total + current, 0)
              ?? 0;
}
```

And we write a few more tests to make sure we are behaving correctly:
```javascript
  describe('Test sum of numbers', () => {
    it('should return 100 bottles of beer', () => {
      expect(sum('99 bottles of beer on the wall, take 1 down pass it around'))
        .to.be.equal(100);
    });

    it('should return 0 for no more bottles of beer', () => {
      expect(sum('No more bottles of beer on the wall, no more bottles of beer'))
        .to.be.equal(0);
    });

    it('should return 0 for no argument', () => {
      expect(sum()).to.be.equal(0);
    });

    it('should return 0 for empty string', () => {
      expect(sum('')).to.be.equal(0);
    });

    it('should return 0 for undefined', () => {
      expect(sum(undefined)).to.be.equal(0);
    });

  });
```
```
  Test sum of numbers
    √ should return 100 bottles of beer
    √ should return 0 for no more bottles of beer
    √ should return 0 for no argument
    √ should return 0 for empty string
    √ should return 0 for undefined


  5 passing (11ms)
```


Cool! All our tests passed!

And that's it for this post. Thanks for reading!
