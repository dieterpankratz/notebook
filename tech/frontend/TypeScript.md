# Generics

## Higher-order functions that operate on dictionaries

```
interface Dict<T> {
  [k: string]: T
}
```

### Filter

```
function filterDict<T>(
  input: Dict<T>,
  filterCb: (arg: T) => boolean
): Dict<T> {
  const toReturn: Dict<T> = {};
  
  for (let key in input) {
    const thisVal = input[key];
    if (filterCb(thisVal)) {
      toReturn[key] = thisVal;
    } 
  }
  
  return toReturn;
}
```

### Map

```
function mapDict<T, U>(
  input: Dict<T>,
  mapCb: (arg: T) => U
): Dict<U> {
  const toReturn: Dict<U> = {};
  
  for (let key in input) {
    const thisVal = input[key];
    toReturn[key] = mapCb(thisVal);
  }
  
  return toReturn;
}
```

### Reduce

```
function reduceDict<T, V>(
  input: Dict<T>,
  reducer: (currentValue: V, item: T) => V,
  initialValue: V
 ): V {
  let valToReturn = initialValue;
  
  for (let key in input) {
    const thisVal = input[key];
    valToReturn = reducer(valToReturn, thisVal);
  }
  
  return valToReturn;
 }
```

## Best Practices

### Use each type parameter at least twice

When doing a code review you might be scanning through to see if someone has written `as` but the following example
is type casting, too:

```
function returnAsSomething<T>(arg: any): T {
  return arg;
}
```

A single use of a type param will have the same effect as type casting! It's just a harder problem to catch in a code review.

### Define type parameters as simply ossible

You want to define your type parameters as simply as possible and in a way that allows you to take advantage of all of the static
analysis and the inference that TS has to offer. So push your type parameters down to the lowest leve (the simplest way of describing them)
as you can and then in your argument list you can be more explicit. And there you can see the type checking will do its job more easily
and more successful. 

### Only use type parameters when you have a real need for them

They introduce complexity, and you shouldn't be adding complexity to your code unless it is worth it!










