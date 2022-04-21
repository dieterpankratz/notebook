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










