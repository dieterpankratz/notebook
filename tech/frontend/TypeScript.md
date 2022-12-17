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

# Conditional Types

## Type Queries

Type queries allow us to obtain type information from a value. We do this by using the two keywords `keyof` and `typeof`.
It is important to note that in JS we can have properties that are strings, numbers or rarely symbols. 
The following code shows how how to tell TS that we only want to see keys that are strings:

```
type DatePropertyNames = keyof Date;
type DateStringPropertyNames = DatePropertyNames & string;
```

The `typeof` type query is a much more direct type query. By using it we are saying: "I have a value and I wish to get the type that describes
this value. A use case might be if you are consuming a library that exposes some values to you and they don't give you the types directly. 

## Conditional Types

Conditional types help describe the relation between the types of inputs and outputs. Conditional types take a form that looks a little like conditional expressions (condition ? trueExpression : falseExpression) in JavaScript.

Important note: The `extends` keyword is the only tool we have for expressing conditions! You can think of it as >= operator or "at least". You can ask yourself: "Does everything on the left of `extends` fit within the set on the right: 

```
64 extends number // true
number extends 64 // false
never extends any // true
any extends any // true
```

## Extract and Exclude

Utility types which are included as part of TS and used to obtain some sub-part of a type. Either through specifying what you are looking for or 
what you are not looking for. Behind the scenes they simply use conditional types. 
With `Extract` you search for something that is more general and the matches will be at that level of specificity or even more specific.
We are extracting the subset of type A that is assignable to type b.

Here is how it works:

```
type Exclude<T, U> = T extends U ? never : T;
type Extract<T, U> = T extends U ? T : never;
```

## The `infer` keyword

It is a special tool for conditional types. It is used to extract some sub part of one type from another type.
It can only be used within the condition expression of a conditional type! 

Let's look at an example:

```
class shoppingCart
  constructor(items: string[]) {}
}
```

We want to design some sort of type that takes `shoppingCart` as a type parameter and it emmits `string[]`
And here is the solution:

```
type ConstructorArg<C> = C extends {
  new (arg: infer A, ...args: any[]): any
}
  ? A
  : never
```

It looks complicated, but here is what it means:
We are defining a type alias ConstructorArg, which takes a single type parameter C.
Next we want to create a conditional type where we are analyzing the static side of a class which is:

```
{ new (...args: any[]): any }
```

So we check if C is in all sets that are 'newable':

```
type ConstructorArg<C> = C extends {
  new (...args: any[]): any
}
```

Next, we want to "collect" the first constuctor argument:

```
type ConstructorArg<C> = C extends {
  new (arg: infer A, ...args: any[]): any
}
```
So we change from accepting any arbitrary list of arguments to accepting a first argument which must be there. So you can think of this almost like 
We're going to suck up whatever is here and call it `A`. And after `A` we have zero or more other arguments.

And finally we have to say what we emmit (what type do we evaluate out to) if the condition is met. And what if it is false:

```
type ConstructorArg<C> = C extends {
  new (arg: infer A, ...args: any[]): any
}
  ? A
  : never
```

# Mapped types

This is the most powerful tool for transforming types. Taking some type and making it another type in a very organized way.
A great way to remember mapped types is thinking of Array.map, in that it feels sort of like looping behavior where we iterating 
over all the keys of something and we are producing a type for a value.

Let's recall index signatures:

```
type Person = {
  name: string
  profession: string
  age: number
}

type Dict<T> = { [k: string]: T }

const employees: Dict<Person> = {}
employees.dieter // <- dieter is of type Person
```

The problem is that the only thing that an index signature allows us to do is "for an arbitrary key, here is the type you can expect to receive."
But what if we have something more specific than just any string. So what if we have like a list of properties and we wish to use that. 
And this mapped type allow us to do that:

```
type Person = {
  name: string
  profession: string
  age: number
}

//mapped type
type myRecord = { [PersonKey in "dieter" | "toni"]: Person }

function printEmployees(employees: MyRecord) {
  employees.dieter // works!
  employees.toni // works!
  employees.chris // "Property 'chris' does not exist on type 'MyRecord'.
}
```

Here we have the `in` keyword and we can think of it as a looping behavior. We are looping over the union type. And think of PersonKey as your `i`.
But remember that it is still just type information so there is no code that's beeing run and looping or so. 
However MyRecord is very specific and can be more generalized with type params:

```
type MyRecord<KeyType, ValueType> = { [Key in KeyType]: ValueType }
```

Btw this is a good practice: Implement the non-abstract thing and then start pulling things out to make it parameterized.

```
type MyRecord<KeyType extends string, ValueType> = {
  [Key in KeyType]: ValueType
}
```

And this is already built-in into TS under the name `Record`:

```
// Construct a type with a set of properties K of type T:
type Record<K extends keyof any, T> = {
  [P in K]: T
}
```

From T, pick a set of properties whose keys are in the union K:

```
type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}
```

Mapping modifiers: As we loop over in our map type, we have the option to determine wether the value type should be readonly or optional.
They are two independant things.

```
// Make all properties in T optional
type Partial<T> = {
  [P in keyof T]?: T[P]
 }
 
 // Make all properties in T required
type Required<T> = {
  [P in keyof T]-?: T[P]
}

// Make all properties in T readonly
type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}
```

## Filtering out properties

One thing that is pretty cool is that we can use template literal type (almost like a query) to say:
"Show me those keys of `Document` that begin with the word `query`:

```
type DocKeys = Extract<keyof Document, `query${string}`>
```

With that we can produce a filtered version of this Document type:

```
 type KeyFilteredDoc = {
  [K in DocKeys]: Document[K]
 }
```

But what if we want to filter based on the value instead of the key?
What we need to do to achieve this:
First we filter out our keys and then we use those keys to build a mapped type:

The approach we want to take here is that we want to select those keys whose value type is what we are looking for.
So we want to go and find `querySelector` and identify that as what we want because `document.querySelector` is matching the
condition that we are filtering for. We are not just picking it because of its name, but because it has a value that is what we 
are looking for:

```
  type ElementFunction = (...args: any[]) => (Element | Element[])
```

Let's look at filtered keys:

```
  type FilteredKeys<T, U> = { [P in keyof T]: T[P] extends U ? P : never }[keyof T] & T
```


```
  type FilteredKeys<ToFilter, Condition> = { P in keyof ToFilter]: ToFilter[P] extends Condition ? P : never }[keyof[ToFilter] & ToFilter
```

We are taking in a type (T) which is the thing we are iterating oveer (-> keyof T). 
We loop over all of the keys. `P` is our little `i` in our for loop. 
With `ToFilter[P]` we are going to grap out the value type out of whatever we're filtering from. And we are going
to check it against the condition. Is the value type assignable to whatever we passed in. If so, we're sending the 
parameter `P` through, which is the key (not the value!). Otherwise: `never`.

```
type RelevantDocumentKeys = FilteredKeys<Document, ElementFunction>
```

## Unions and Indexing

### Discriminated Union
Discriminated unions have common properties which are used to
differentiate between members of the union: 

```
 */
type A =
  | {
      type: "a";
      a: string;
    }
  | {
      type: "b";
      b: string;
    }
  | {
      type: "c";
      c: string;
    };
```

In this case, type is the discriminator.

### How to extract/exclude a type from a discriminated Union?

```
type Engineer =
  | {
      level: "junior";
      name: string;
    }
  | {
      level: "mid";
      name: string;
    }
  | {
      level: "senior";
      name: string;
};

type JuniorEngineer = Extract<Engineer, { level: "junior" }>;
type NonJuniorEngineer = Exclude<Engineer, { level: "junior" }>;
```

The utility type `Extract` will check if any of the members will extend `{ level: "junior" }` and if yes, it will extract the entire member.

### as const

```
const responseEnumMap = {
  SUCCESS: 'success',
  FAILURE: 'failure'
};
```

The issue is that `type Success = typeof responseEnumMap['SUCCESS']` will be just `string`.
To resolve them as their literal values instead we need to add the `as const` annotation to the object:

```
const responseEnumMap = {
  SUCCESS: 'success',
  FAILURE: 'failure'
} as const;
```

It does two things, it makes sure that the values are inferred as their literals and it also adds the `READONLY` annotation to them.
Before we added `as const` the object was mutable, so we were able to do something like responseEnumMap.SUCCESS = 'somethingElse'. That's why TS inferred it correctly just as `string`.
