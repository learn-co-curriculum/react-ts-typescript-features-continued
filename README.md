# TypeScript Features

## Learning Goals

- Describe the common TypeScript features
- Use TypeScript features

## Introduction

In this lesson and the next, we will go over the following TypeScript features
that will be most useful as we use TypeScript to learn React and build our first
React application.

1. Primitive Types: `number`, `string` and `boolean`
2. Object Types
3. Array Types
4. Interface
5. Type Aliases
6. Union Types
7. Type Inference
8. Function Types
9. Generics
10. Classes

As we go through this lesson, code along with the given examples in the
`react-ts-typescript-lab-2` directory we configured in the previous lab. Create
a new file in the `src` folder called `practice.ts` and code along in there.
TypeScript syntax can take some time getting used to when you're already
familiar with vanilla JavaScript. Writing things out as you learn them can help
solidify the concepts.

## Primitive Types

As previously discussed, TypeScript supports assigning a type to a variable,
including the following primitive types you might have seen in other languages:

1. `number`: this specifies that the variable can be any number. TypeScript does
   not make a distinction between integers and decimal numbers or between
   decimal numbers of various precision.
2. `string`: this specifies that the variable will hold text
3. `boolean`: this specifies that the variable will hold a value of `true` or
   `false`

The type of a variable is specified by adding `: <type>` after the variable
declaration. The typed variables can then be given values, as long as they match
the declared type. Here are a couple of examples:

```typescript
let name: string 
let age: number 
let studentFlag: boolean 

name = "example"
age = 12
studentFlag = true
```

Typing a variable and defining the variable's value separately, as in the
example above, is occasionally desired. Other times, it may feel like a lot of
redundant code.

In such cases, we can type a variable _and_ define it at the same time:

```typescript
let name: string = "example";
let age: number = 12;
let studentFlag: boolean = true;
```

If I try to assign a value of the wrong type to either of these variables:

```typescript
let personName: string = "Jamie";
let age: number = "12";
let studentFlag: boolean = true;
```

The TypeScript compiler will give me an error:

```typescript
error TS2322: Type 'string' is not assignable to type 'number'.

35 let age: number = "12";
       ~~~
```

Additionally, any IDE that supports TypeScript may give you an error message in
the editor as soon as it sees code that is not consistent with an assigned type:

![TypeScript wrong type](https://curriculum-content.s3.amazonaws.com/java-mod-8/ts-wrong-type.png)

## Object Types

While simple typing is easy enough for variables of a single primitive type,
what about more complex values, such as objects or arrays?

We can specify that a given variable can have a more complex "shape" with
TypeScript as well. For example, we could have a `person` object that must have
inside it all three types we worked with earlier. We would use a similar syntax
of `<variable>: <type>`, but instead of a singular type, we would shape out the
object and type its individual properties, like so:

```typescript
let person: {
  name: string;
  age: number;
  studentFlag: boolean;
};
```

We can then assign a value to the variable `person`:

```typescript
person = {
  name: "Steph",
  age: 20,
  studentFlag: false,
};
```

As we did earlier with simple typing, we can also type and set the value of
objects at the same time. The syntax would look like:

```typescript
let person: {
  name: string;
  age: number;
  studentFlag: boolean;
} = {
  name: "Steph",
  age: 20,
  studentFlag: false,
};
```

TypeScript will not only warn you if any of the fields are not of the right type
as we saw earlier, it will also warn you if you're missing a required field.

We already know the type of message we would get if we tried to set a value to
the wrong type. Let's see what happens if we omit a required field:

```typescript
person = {
  age: 20,
  name: "Steph",
};
```

In this case, TypeScript will give you this type of message:

```typescript
error TS2741: Property 'studentFlag' is missing in type '{ age: number; name: string; }' but required in type '{ name: string; age: number; studentFlag: boolean; }'.

40 person = {
   ~~~~~~

  practice.ts:37:5
    37     studentFlag: boolean;
           ~~~~~~~~~~~
    'studentFlag' is declared here.
```

The error message `Property 'studentFlag' is missing in type '{ age: number;
name: string; }' but required in type '{ name: string; age: number; studentFlag:
boolean; }'` suggests that there might be a way to declare that an element of a
complex type may be acceptable but not required.

This is done by adding a `?` after the name of the variable. We can change the
`person` declaration as follows:

```typescript
let person: {
  name: string;
  age: number;
  studentFlag?: boolean;
};
```

Now the `person` object we defined without the `studentFlag` property is
actually valid.

> **Bonus**: What happens you try to define the object with a property that is
> not included in the type declaration? Try it out and see what happens!

## Array Types

What if we want to give our `person` object an optional property named
`favoriteThings` that holds an array of strings? How would we type that?

Typing arrays follows the syntax `type[]` where `type` should be the type of the
elements in the array and `[]` indicates that it's an array.

In our case, we want an array of strings. We can update our `person` object type
declaration like so:

```typescript
let person: {
  name: string;
  age: number;
  studentFlag?: boolean;
  favoriteThings?: string[];
};
```

Now our person can have an array of their favorite things:

```typescript
person = {
  age: 20,
  name: "Steph",
  favoriteThings: ["Raindrops on roses", "Whiskers on kittens"]
};
```

Like other variables, array types aren't limited to the simple, primitive types.
For example, if we wanted an array of objects that are all shaped the same, we
could type the properties of the object as we did earlier and use it as the type
in the `type[]` syntax:

```typescript
let ratings: { stars: number, blurb: string }[]
```

This would allow an array like the following:

```js
ratings = [
  { stars: 5, blurb: "Talented, brilliant, incredible" },
  { stars: 5, blurb: "Amazing, shop stopping, spectacular" },
  { stars: 5, blurb: "Never the same, totally unique, completely not done before" },
  { stars: 4, blurb: "Unafraid to reference or not reference" }
]
```

> **Note**: In cases where an array holds multiple types, we can use union types
> or the `any` type. We will learn about both later in this lesson.

## Interface

Going back to our `person` object, what if we wanted to have another variable
with the exact same shape as `person`? Based on our current setup, we would have
to give it a new name and then specify its exact shape using the same code as we
did for `person`. For example:

```typescript
let anotherPerson: {
  name: string;
  age: number;
  studentFlag?: boolean;
  favoriteThings?: string[];
};
```

This is not great, as this code would have to be copied and pasted every time I
need this type of structure. TypeScript has 2 constructs to help with this
issue: `interface` and `type`. Let's go over those now, starting with
`interface`.

To turn our `person` type declaration into a re-usable structure, we can define
its shape using the `interface` feature of TypeScript:

```typescript
interface Person {
  name: string;
  age: number;
  studentFlag?: boolean;
  favoriteThings?: string[];
}
```

When defining an `interface` it's important to note that the name of the
interface, in this case `Person` is **not** a JavaScript variable. It is simply
the name of the shape declared on it, and we can now use the name as a type.

We can use it as many times as we want to declare the type of any variable that
needs to follow the structure defined in the interface:

```typescript
let person: Person;
let anotherPerson: Person;
let aThirdPerson: Person;
```

As with explicitly typing a variable, TypeScript will tell us if any variables
typed with an interface don't respect the definition specified in that
interface.

## Type Alias

A type alias is similar to an interface, but varies in some key ways, which we
will go over later. For now, let's look at the definition of a type alias for
the same "Person" shape we've been working with:

```typescript
type Person = {
  name: string;
  age: number;
  studentFlag?: boolean;
  favoriteThings?: string[];
};
```

By defining a "Type" with the `type` keyword, we now have the ability to re-use
this definition elsewhere, just like we did with our `interface` definition:

```typescript
let person: Person;
let anotherPerson: Person;
let aThirdPerson: Person;
```

Two notes to remember about types as we continue to explore the features of
TypeScript:

1. The "shape" of an object can be defined through an `interface` or through a
   `type`. Both mechanisms provide the same ability to re-use the definition of
   that structure in different places. Beyond that, there are difference between
   `interface` and `type` that we will explore later.
2. When we refer to a "type" in the rest of this module, we are referring to the
   general mechanism of defining the shape of an object, regardless of whether
   that definition was declared using the `interface` keyword, the `type`
   keyword or even the `class` keyword, which we will cover later. For example,
   "union types", which we will study next, can be used with types that are
   defined in any of the ways mentioned above.

## Conclusion

In this lesson, we learned about primitive types (`number`, `string` and
`boolean`), object types and array types. We also learned that we can define the
"shape" of an object using either an `interface` or a `type alias`, which helps
us avoid redundant code and reduce errors.

In the next lesson, we will continue with our discussion of TypeScript
features that will be useful for us as we build our React apps.

## Resources

- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [Differences Between Type Aliases and Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)
