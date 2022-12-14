# TypeScript Features, Continued

## Learning Goals

- Describe the common TypeScript features
- Use TypeScript features

## Introduction

In the last lesson, we learned about the first five TypeScript features in our
list:

1. ✅ Primitive Types: `number`, `string` and `boolean`
2. ✅ Object Types
3. ✅ Array Types
4. ✅ Interface
5. ✅ Type Aliases
6. Union Types
7. Type Inference
8. Function Types
9. Generics
10. Classes

As we continue through the list in this lesson, be sure to continue coding along
with the given examples.

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
HTML form. Create a file named `add.html` with the following code:

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
the button is clicked. Create a new file called `add.ts` in your `src` folder
and write the following:

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

The first 3 lines of codes are to assign variables to the button and the input
fields. As you may notice, instead of using type annotation (`:`) to declare the
type, we're using the keyword `as`. This is called **type assertion**. Assertion
is used when we have more information than TypeScript has available about a
type.

In this particular case, when using methods such as `document.getElementById` or
`document.querySelector`, TypeScript can only assume that the element returned
will be of type `HTMLElement`. As the developers of this mini application,
though, we know what exact HTML elements we're trying to select. So, we tell
TypeScript to expect these elements by using type assertion.

Next in our code, you will notice that we're using the `!` notation, for example
with the following expression: `button!.addEventListener(...)`. This is to tell
TypeScript that we are confident that `button` will never be `null`. If we
didn't do that, `tsc` would complain that the `button` variable might be null
because it has no direct awareness of the HTML form we're getting this reference
from.

Lastly, the `button!.addEventListener()` function call tells the HTML that when
this button is clicked, the `function` passed in as the second parameter should
be called. That function then takes the values from the first and the second
`input` fields and adds them together using our `add()` function.

With that, the code we just created is syntactically correct, but will still not
compile. Instead, `tsc` will give us the following error:

```typescript
error TS2345: Argument of type 'string' is not assignable to parameter of type 'number'.

7         console.log(add(input1!.value, input2!.value));
                          ~~~~~~~~~~~~~
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

Instead of using the `any` type as a workaround, we can be more precise with
union types and actually tell the `add()` function that we want it to accept
either `number` parameters or `string` parameters:

```typescript
function add(firstNumber: number | string, secondNumber: number | string) {
  return +firstNumber + +secondNumber;
}
```

Let's examine this code:

1. We are using the union type `|` notation to indicate that `firstNumber` can
   be of type `number` _or_ it can be of type `string`. Both types are
   acceptable for this particular variable. We specify the same for
   `secondNumber`.
2. Since our variables could now be either `number` or `string`, we need to
   convert them before we can add them together. That's what the `+` in front of
   `firstNumber` and `secondNumber` does. If the value is a `string`, it
   attempts to convert it to a number.

Your code will now compile with the `tsc` command and you should be able to open
`add.html` in your browser window, open your console and see the following
output when you enter numerical values in the input fields and click the "Add!"
button:

![Add HTML Results](https://curriculum-content.s3.amazonaws.com/react-ts/react-ts-typescript-features/ts-add-html.png)

A "Union Type" then is a way to tell TypeScript that a variable can be of more
than one type. This is a great alternative to using the `any` type, which, while
flexible, negates the value of typing in TypeScript.

> **Note**: Data validation in HTML is a complex subject that is beyond the
> scope of this particular section, so this example is incomplete. In reality,
> we would want to make sure that that values the user enters in these input
> fields are actually numbers and we would want to display an error message if
> the user entered incorrect values.

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

> **Note**: Type inference can be tricky because the wrong type could be
> inferred if the first assignment to the variable is of the wrong type, which
> would lead to errors that are difficult to track down. It is better practice
> to be explicit about your types, as that is safer for the compiler and is more
> explicit of your intent as the programmer, which is always key to making your
> code more maintainable over time.

## Function Types

One case in which it is generally better not to explicitly specify type,
however, is with functions.

When typing functions, what we're actually typing is its return value. The
syntax is `function fnName(): type {}`. For example:

```typescript
function addExplicit(a: number, b: number): number {
  // return type is explicitly defined as number 
  return a + b;
}
```

Generally, the return type of a function is easier and safer to infer with type
inference because most functions do not (and should not) have many different
return statements. Functions that have returns statements are usually quite
explicit about the values they are constructing. Thus, you can usually get away
with not typing your functions and letting TypeScript infer the return type
itself.

> **Note**: When a function does not explicitly define a return type and does
> not have a return statement, its return type is inferred to be `void`, which
> means the caller of that function will not get a return value.

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

> **Note**: This function uses the "spread" operator `...` - this is an operator
> (available in vanilla JavaScript) that takes the value of an existing array
> and returns all existing elements of that array. In our example, this means
> that `newArray` is initialized to an array that contains first `value` and
> then every entry in the `array` variable.

One way to change the `insertAtBeginning()` function is to change the type of
the array and the variable to `any`. But as we've seen before, this takes away
our ability to use TypeScript's type validation functionality.

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

1. The `<>` notation indicates that this function is dealing with generics.
2. We can name our generic anything we want. We're using `AGenericType` here to
   drive that point home, but most code will usually use a single letter for the
   generic, so that's what we'll use moving forward to follow that convention.
3. Once the generic definition is added to the function name, it can be used
   anywhere inside the function, including in the parameter names.
4. So now our array is an array of `AGenericType` objects and our new value we
   want to add to the beginning of the array is also of type `AGenericType`.

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

Classes, as you may know from vanilla JavaScript, are simply another way to
define a reusable shape we want objects to take. Unlike interfaces and type
aliases, classes have the added benefit of being able to assign default values
and behavior to those shapes:

```typescript
class Student {
  firstName: string;
  lastName: string;
  age: number;
  role: string;
  private courses: string[]; // this is a private property

  constructor(firstName: string, lastName: string, age: number, courses: string[]) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
    this.courses = courses;
    this.role = 'student';
  }

  enroll(courseName: string) {
    this.courses.push(courseName);
  }

  listCourses() {
    return this.courses.slice();
  }
}
```

This `Student` class has several attributes, as we've seen before with
`interfaces`, but it also has:

1. A `constructor`: this is a special method that gets called when an object of
   this type is created. The constructor has the responsibility to initialize
   the object with its proper values, sometimes based on defaults like `role` in
   our example, and sometimes based on values passed into it.
2. Two functions that can be called on any object of this type. These functions
   can then implement functionality that will be available and standard for all
   objects instantiated from this class.
3. A `private` member variable. Private member variables, in this case
   `courses`, are not accessible outside the code in this class. This allows us
   to have "internal" state that can be changed without affecting the users of
   this class.

To use a class as a blueprint, we declare a new variable and set it to a new
instance of the class. We must also provide any required parameters in the order
they were defined.

```typescript
const alayna = new Student("Alayna", "Cooke", 20, ["Intro to Computer Science"]);
```

If we were to pass in a parameter with the wrong type, for example providing
`"20"` as a string instead of `20` as a number, TypeScript would throw an error:

> **Note**: In this example, TypeScript would infer that the variable `alayna`
> is of type `Student`.

```bash
error TS2345: Argument of type 'string' is not assignable to parameter of type 'number'.

     const Alayna = new Student("Alayna", "Cooke", "20", ["Intro to Computer Science"]); 
```

While everything we've learned about classes is already handy, you may have
noticed that our example seems to have a lot of redundant code. First, we define
all the properties we expect our class to have and their types. Then, we define
those same properties and types in the parameters of the `constructor`. Finally,
we define the actual properties themselves based off the parameters and
defaults.

Thankfully, TypeScript gives us a shorthand for defining properties and their
types, including private members, by just using the constructor. The following
code is functionally equivalent to the code above:

```typescript
class Student {
  role: string

  constructor(
    public firstName: string,
    public lastName: string,
    public age: number,
    private courses: string[],
  ) {
    this.role = 'student'
  }

  enroll(courseName: string) {
    this.courses.push(courseName);
  }

  listCourses() {
    return this.courses.slice();
  }
}
```

We still had to define the `role` property's type outside of the constructor as
we want it to have a set, default value. This is also why we still define its
value within the `constructor` method. The other properties don't have to be
defined within the `constructor` as the shorthand will use the names given to
them in the parameters as their property name.

Properties marked as `public` are the opposite of `private`, meaning they are
accessible outside of the class itself. It is the default membership for class
properties and normally does not have to be explicitly defined. However, when
using this shorthand you _must_ provide the `public` membership for it to work
correctly.

> **Note**: There are other modifiers aside from `public` and `private` that can
> be attached to class properties. Take a look at the Member Visibility and
> readonly documentation linked in the Resources section below if you're
> interested in learning about them.

## Conclusion

We learned a lot in the last two lessons, and even then we have only scratched
the surface of what TypeScript has to offer. While all this information may be
overwhelming, it's important to remember no one is expected to be an expert in
anything after just one lesson. We simply want you to know these features exist,
and we will practice them more as we move into learning about React.

TypeScript adds a lot of functionality that JavaScript simply lacks. Though it
mitigates the loose nature of JavaScript that some may find easier or quicker to
work in, it certainly makes it more maintainable and robust.

## Resources

- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [Member Visiblity](https://www.typescriptlang.org/docs/handbook/2/classes.html#member-visibility)
- [readonly Modifier](https://www.typescriptlang.org/docs/handbook/2/classes.html#readonly)
