# React Notes - a random set of information for a React Developer

## 1. Avoid adding useEffects when it can be computed on render

Don't add another state + useEffect for the sake of calculating the outcome of the prop. Do it on the re-rendering phase.

``` typescript
const MyForm: React.FC<FormProps> => (props) {
   const [firstName, setFirstName] = useState<string>('');
   const [lastName, setLastName] = useState<string>('');

   // Don't do this
   const [fullName, setFullName] = useState<string>('');

   useEffect(() => {
      setFullname(firstName + ' ' + lastName);
   }, [firstName, lastName]);

   // Do this instead
   const fullName = firstName + ' ' + lastName;

   // ...
}
```

## 2. When do you need to useMemo or useCallback?

You use them for only 2 reasons:

### 1. Referential Equality

When we want to ensure we only do something if they are truly not the same deep equal-wise.

As background, we know in Javascript that

``` typescript
{} === {} // false
[] === [] // false
{ name: "Foo" } === { name: "Foo" } // false
```

So if the prop we are about to receive is an object, then we want to memoize it because we don't want to trigger actions needlessly.

This is in case where we use it as a dependency like useEffect, useMemo, useCallback or React.memo (note, React.memo needs to be used judiciously)

As an example:

``` typescript
const Form: React.FC<FormProps> = ({ data }) => {
   useEffect(() => {
      // Do what we need to do here
   }, [data]);
}
```

2. Expensive calculations - we want to calculate things once if they are expensive to compute. So on the next re-render, we don't need to recalculate and speed is optimised.
Like they always say, measure first. Take the AHA approach, avoid hasty abstractions.

## 3. What are some Typescript utilities you should know about

### 1. `Partial<T>`

 This utility allows you to make all properties of a type optional. It's useful when you want to create a type with some properties optional.

### 2. `Pick<T, K>`

This utility allows you to create a new type by picking only certain properties from an existing type. It's useful when you want to create a type with a subset of properties.

### 3. `Omit<T, K>`

This utility is the opposite of Pick. It creates a new type by omitting certain properties from an existing type. It's useful when you want to create a type with certain properties removed.

### 4. `Required<T>`

This utility makes all properties of a type required. It's useful when you want to enforce that all properties must be present.

### 5. `Readonly<T>`

This utility makes all properties of a type readonly. It's useful when you want to ensure that an object remains immutable after creation.

### 6. `Record<K, T>`

This utility creates an object type whose keys are of type K and values are of type T. It's useful for creating dictionaries or lookup tables.

### 7. `ReturnType<T>`

This utility extracts the return type of a function type. It's useful when you want to capture the return type of a function for use elsewhere in your code.

### 8. `Parameters<T>`

This utility extracts the parameter types of a function type as a tuple type. It's useful when you want to capture the parameter types of a function for use elsewhere in your code.

### 9. `Exclude<T, U>` 

This utility removes types from T that are assignable to U. It's useful for creating difference types.

### 10. `Extract<T, U>` 

This utility extracts types from T that are assignable to U. It's useful for creating intersection types.

### 11. `NonNullable<T>`

This utility removes null and undefined from the type T. It's useful for ensuring that a value is not null or undefined.
