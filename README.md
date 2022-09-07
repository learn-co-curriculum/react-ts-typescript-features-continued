# TypeScript Features

## Learning Goals

- Describe the common TypeScript features.
- Use TypeScript features.

## Introduction

In this lesson, we will go over the following TypeScript features that will be
most useful as we use TypeScript to learn Angular and build our first Angular
application.

1. Primitive Types: `number`, `string` and `boolean`
2. Complex Types: objects and arrays
3. Type Inference
4. Union Types
5. Type Aliases
6. Generics
7. Classes
8. Interfaces
9. Decorators

Another advantage of TypeScript is that since it needs to be compiled to
JavaScript anyway, the compiler can also take care of converting modern features
of JavaScript into less modern, but more widely supported, versions of
JavaScript.

## Simple Types

As previously discussed, TypeScript supports assigning a type to a variable,
including the following primitive types you might have seen in other languages:

1. `number`: this specifies that the variable can be any number. TypeScript does
   not make a distinction between integers and decimal numbers or between
   decimal numbers of various precision.
2. `string`: this specifies that the variable will hold text
3. `boolean`: this specifies that the variable will hold a value of `true` or
   `false`

The type of a variable is specified by adding ` : <type>` after the variable
definition. Here are a couple of examples:

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

Not only will the TypeScript compiler give me an error:

```typescript
simple.ts:35:5 - error TS2322: Type 'string' is not assignable to type 'number'.

35 let age: number = "12";
       ~~~


Found 1 error in simple.ts:35
```

But any IDE that supports TypeScript will have the ability to give you an error
message in your editor as soon as it sees code that is not consistent with the
types that you have indicated:

![TypeScript wrong type](https://curriculum-content.s3.amazonaws.com/java-mod-8/ts-wrong-type.png)

## Complex Types

We can also specify that a given variable can have a more complex "shape" than
simply be made of a single primitive type. For example, we could have a `person`
variable that must have inside it all three types we worked with earlier:

```typescript
let person: {
  name: string;
  age: number;
  studentFlag: boolean;
};
```

You can then assign a value to the variable `person` and TypeScript will warn
you if the value doesn't have the required fields or if any of the fields are
not of the right type:

```typescript
person = {
  age: 20,
  name: "Steph",
  studentFlag: false,
};
```

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
simple.ts:40:1 - error TS2741: Property 'studentFlag' is missing in type '{ age: number; name: string; }' but required in type '{ name: string; age: number; studentFlag: boolean; }'.

40 person = {
   ~~~~~~

  simple.ts:37:5
    37     studentFlag: boolean;
           ~~~~~~~~~~~
    'studentFlag' is declared here.


Found 1 error in simple.ts:40
```

Note something important here - the message
`Property 'studentFlag' is missing in type '{ age: number; name: string; }' but required in type '{ name: string; age: number; studentFlag: boolean; }'`
suggests that there might be a way to declare that an element of a complex type
may be acceptable but not required.

This is done by adding a `?` after the name of the variable. We can change the
`person` declaration as follows:

```typescript
let person: {
  name: string;
  age: number;
  studentFlag?: boolean;
};
```

Which now means the code that assigns a value that does not include a
`studentFlag` value is actually valid.

What if I wanted to have another variable with the exact same shape as `person`.
Based on our current setup, I would have to give it a name and then specify its
exact shape using the same code as I did for `person`. For example:

```typescript
let anotherPerson: {
  name: string;
  age: number;
  studentFlag?: boolean;
};
```

This is not great, as this code would have to be copied and pasted everywhere
where I need this type of structure. TypeScript has 2 constructs to help with
this issue: `interface` and `type`. Let's go over those now.

## Interface

Consider our `person` variable from the previous section:

```typescript
let person: {
  name: string;
  age: number;
  studentFlag?: boolean;
};
```

To turn this person into a re-usable structure, we can define its shape using
the `interface` feature of TypeScript:

```typescript
interface Person {
  name: string;
  age: number;
  studentFlag?: boolean;
}
```

I can declare any variable as needing to follow the structure defined in the
`Person` interface:

```typescript
let person: Person;
let anotherPerson: Person;
```

And TypeScript will warn me of any use of either the `person` or the
`anotherPerson` variable that doesn't respect the definition specified in the
`Person` interface.

## Type (aka Type Alias)

A type is similar to an interface, but varies in some key ways, which we will go
over later. For now, let's look at the definition of a type for same "Person"
shape we've been working with:

```typescript
type Person = {
  name: string;
  age: number;
  studentFlag?: boolean;
};
```

By defining a "Type" with the `type` keyword, we now have the ability to re-use
this definition elsewhere, just like we did with our `interface`-based
definition:

```typescript
let person: Person;
let anotherPerson: Person;
```

2 notes to remember about types as we continue to explore the features of
TypeScript:

1. The "shape" of an object can be defined through an `interface` or through a
   `type`. Both mechanisms provide the same ability to re-use the definition of
   that structure in different places. Beyond that, there are difference between
   `interface` and `type` that we will explore a later in this module.
2. When we refer to a "type" in the rest of this module, we are referring to the
   general mechanism of defining the shape of an object, regardless of whether
   that definition was declared using the `interface` keyword, the `type`
   keyword or even the `class` keyword, which we will cover later. For example,
   "union types", which we will study next, can be used with types that are
   defined in any of the ways mentioned above.

## Union Types

With "union" types, TypeScript gives us the ability to specify that a variable
could be of one type _or_ of another type.

To explore why we might want to do that, let's go back to our function that adds
2 numbers:

```typescript
function add(firstNumber: number, secondNumber: number) {
  return firstNumber + secondNumber;
}

console.log(add(10, 20));
```

As a reminder, this code compiles correctly because we're passing the right
types into the `add()` function, and it gives us the expected result of `30`
because the numbers being added are actually numbers instead of strings.

This is not very useful code, however, because it only adds together 2 hardcoded
numbers. Let's make it more useful then, by integrating it with a very simple
HTML form. Create a file named `no-types.html` with the following code:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>JS has no types</title>
    <script src="../dist/add.js" defer></script>
  </head>
  <body>
    <input type="number" id="firstNumber" placeholder="First Number" />
    <input type="number" id="secondNumber" placeholder="Second Number" />
    <button>Add!</button>
  </body>
</html>
```

This sets up a very basic HTML form with 2 input fields and a button. We will
now write TypeScript code that will add the values in the 2 input fields when
the button is clicked:

```typescript
const button = document.querySelector("button") as HTMLButtonElement;
const input1 = document.getElementById("firstNumber") as HTMLInputElement;
const input2 = document.getElementById("secondNumber") as HTMLInputElement;

button!.addEventListener("click", function () {
  console.log(add(input1!.value, input2!.value));
});

function add(firstNumber: number, secondNumber: number) {
  return firstNumber + secondNumber;
}

console.log(add(10, 20));
```

Let's walk through this code:

1. The first 3 lines of codes are to assign variables to the button and the
   input fields. As you can see, we are using the `as ` notation to tell
   TypeScript that we want each element to be of a specific type. This allows
   TypeScript (and VSCode) to understand the type we're expected to deal with
   and help us enforce proper usage in the subsequent code.
2. Next, you will notice that we're using the `!` notation, for example with the
   following expression: `button!.addEventListener(...)` - this is to tell
   TypeScript that we are confident that `button` will never be `null`. If we
   didn't do that, `tsc` would complain that the `button` variable might be null
   because it has no direct awareness of the HTML form we're getting this
   reference from.
3. The `button!.addEventListener()` function call tells the HTML that when this
   button is clicked (the `click`) parameter the `function` passed in as the
   second parameter should be called. That function then takes the values from
   the first and the second `input` fields and adds them together using our
   `add()` function

With that, the code you just created is syntactically correct, but will still
not compile. Instead, `tsc` will give you the following error:

```typescript
src/add.ts:7:25 - error TS2345: Argument of type 'string' is not assignable to parameter of type 'number'.

7         console.log(add(input1!.value, input2!.value));
                          ~~~~~~~~~~~~~


Found 1 error in src/add.ts:7
```

That is because the `.value` property of an `input` field is actually a
`string`, and our `add()` function is currently defined as only taking `number`
parameters.

There are multiple ways to fix this issue. We will address it in a way that
shows how useful union types can be. But first, let's see how we would have to
fix it to make the compiler happy _without_ using union types. We could modify
the `add()` function to take parameters of type `any`:

```typescript
function add(firstNumber: any, secondNumber: any) {
  return firstNumber + secondNumber;
}
```

This removes the compiler warning and lets us run our code. However, it also
renders TypeScript's type functionality basically useless because we now have an
`add()` function that will accept parameters of any types. This defeats the
purpose of using TypeScript in the first place.

Instead of using the `any` type as a workaround, we can be more precise and
actually tell the `add()` function that we want it to accept either `number`
parameters or `string` parameters:

```typescript
function add(firstNumber: number | string, secondNumber: number | string) {
  return +firstNumber + +secondNumber;
}
```

Let's examine this code:

1. We are using the `|` notation to indicate that `firstNumber` can be of type
   `number` _or_ it can be of type `string`. Both types are acceptable for this
   particular variable. We specify the same for `secondNumber`.
2. Since our variables could now be either `number` or `string`, we need to
   convert them before we can add them together. That's what the `+` in front of
   `firstNumber` and `secondNumber` does - it takes the `string` value and
   attempts to convert it to a number.

Your code will now compile with the `tsc` command and you should be able to open
`no-types.html` in your browser window, open your console and see the following
output when you enter numerical values in the input fields and click the "Add!"
button:

![Add HTML Results](https://curriculum-content.s3.amazonaws.com/java-mod-8/ts-add-html.png)

> Data validation in HTML is a complex subject that is beyond the scope of this
> particular section, so this example is incomplete. In reality, we would want
> to make sure that that values the user enters in these input fields are
> actually numbers and we would want to display an error message if the user
> entered incorrect values.

A "Union Type" then is a way to tell TypeScript that a variable can be of more
than one type. This is a great alternative to using the `any` type, which while
flexible is really negating the value of typing in TypeScript.

## Type Inference

TypeScript has the ability to infer the type of a variable based on the value
you assign to it. For example:

```typescript
let x = 3;
```

Based on this code, TypeScript will infer that the type of `x` is `number` and
the following code will compile successfully:

```typescript
function addNumbers(
  firstNumber: number | string,
  secondNumber: number | string
) {
  return +firstNumber + +secondNumber;
}

let x = 3;

console.log(addNumbers(x, 20));
```

> Note that type inference can be tricky because the wrong type could be
> inferred if the first assignment to the variable is of the wrong type, which
> would lead to errors that are difficult to track down. It is better practice
> to be explicit about your types, as that a) is safer for the compiler and b)
> is more explicit of your intent as the programmer, which is always key to
> making your code more maintainable over time.

## Function Type

Just as you can define a type for variables, you can also define a type for the
return value of functions:

```typescript
function addExplicit(a: number, b: number): number {
  // return type is explicit here
  return a + b;
}
```

Generally, the return type of a function is easier and safer to infer because
most functions do not (and should not) have many different return statements and
those return statements are usually quite explicit about the values they are
constructing.

> Multiple return statements in a function make it harder to read and track what
> the function does. They also make it harder to test. If you are writing a
> function that seems to lend itself to having many return points, it might be a
> sign that you're trying to do too much in a single function. Instead, try to
> break the functionality up into multiple steps and have each step implemented
> in their own function.

When a function a) does not explicitly define a return type and b) does not have
a return statement, its return type is inferred to be `void`, which means the
caller of that function will not get a return value.

## Generics

Types are great for all the reasons we've been covering in this module. However,
they do make it difficult in some scenarios to write code that can be re-used in
many different scenarios.

Consider the following use case: we need a function that can take an existing
array and add a value at the beginning of that array. We might write something
along these lines:

```typescript
function insertAtBeginning(array: number[], value: number) {
  const newArray = [value, ...array];
  return newArray;
}

let numbers = [10, 20, 30];
numbers = insertAtBeginning(numbers, 5);
console.log("new array: " + numbers);
```

The `insertAtBeginning()` function works for arrays of numbers, but will not
work for arrays of strings or any other primitive or complex type.

> Note: this function used the "spread" operator `...` - this is a TypeScript
> operator (also available in vanilla JavaScript) that takes the value of an
> existing array and returns all existing elements of that array. In our
> example, this means that `newArray` is initialized to a an array that contains
> first `value` and then every entry in the `array` variable.

One way to change the `insertAtBeginning()` function to make it work for "any"
type, of course, would be to change the type of the array and the variable to
`any`. But as we've seen before, this takes away our ability to use TypeScript's
type validation functionality.

Generics are a better solution for this problem. Instead of specifying a
specific type, we can actually specify a "generic" type, and the caller of the
function can decide what type that will be. And once that type is defined, it
will have to be consistent throughout that usage of the function.

Let's look at a modified version of the `insertAtBeginning()` function that uses
generics:

```typescript
function insertAtBeginning<AGenericType>(
  array: AGenericType[],
  value: AGenericType
) {
  const newArray = [value, ...array];
  return newArray;
}
```

Let's examine this code:

1. The `<>` notation indicates that this function is dealing with generics
2. We can name our generic anything we want. We're using `AGenericType` here to
   drive that point home, but most code will usually use a single letter for the
   generic, so that's what we'll use moving forward to follow that convention.
3. Once the generic definition is added to the function name, it can be used
   anywhere inside the function, including it the parameter names
4. So now our array is an array of `AGenericType` objects and our new value we
   want to add to the beginning of the array is also of type `AGenericType`

We can now use this function with any type we want:

```typescript
function insertAtBeginning<T>(array: T[], value: T) {
  const newArray = [value, ...array];
  return newArray;
}

let numbers = [10, 20, 30];
numbers = insertAtBeginning(numbers, 5);

console.log("new numbers array: " + numbers);
let strings = ["first string", "second string", "third string"];
strings = insertAtBeginning(strings, "new string");
console.log("new strings array: " + strings);
```

But the following code will, correctly, give us a compilation error because
we're trying to combine 2 types when the function signature clearly states that
whatever the type of the array is, it needs to be the same type as the type of
value we're trying to add to it:

![TypeScript - Wrong Generics](https://curriculum-content.s3.amazonaws.com/java-mod-8/ts-wrong-generics.png)

So we can now define functions that are flexible enough to work with any type,
but that retain TypeScript's ability to enforce proper usage of types.

## Classes

We will work with classes more as we dive deep into Angular, but for now let's
look at classes as simply another way to define the shape we want objects to
take, with the added benefit of being able to assign behavior to those shapes:

```typescript
class Student {
  firstName: string;
  lastName: string;
  age: number;
  private courses: string[]; // this is a private property

  constructor(first: string, last: string, age: number, courses: string[]) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
    this.courses = courses;
  }

  enrol(courseName: string) {
    this.course.push(courseName);
  }

  listCourses() {
    return this.courses.slice();
  }
}
```

This `Student` class has several attributes, as we've seen before with
`interfaces`, but it also has:

1. A constructor: this is a special method that gets called when an object of
   this type is created. The constructor has the responsibility to initialize
   the object with its proper values, sometimes based on defaults, sometimes
   based on values passed into it.
2. 2 functions that can be called on any object of this type. These functions
   can the implement functionality that will be available and standard for all
   objects of this type.
3. A private member variable: this member variable, in this case `courses` will
   not be accessible outside the code in this class. This allows us to have
   "internal" state that can be changed without affecting the users of this
   class.

TypeScript gives us a shorthand for defining properties, including private ones,
using the constructor without having to define the member variables separately.
The following code is functionally exactly equivalent to the code above:

```typescript
class Student {
  constructor(
    public first: string,
    public last: string,
    public age: number,
    private courses: string[]
  ) {}

  enrol(courseName: string) {
    this.course.push(courseName);
  }

  listCourses() {
    return this.courses.slice();
  }
}
```
