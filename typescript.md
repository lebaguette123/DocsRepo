# TypeScript

Hello guys. Since we are going to be working on a project that contains TypeScript, I wanted to make this document that explains what TypeScript is, it's history, some basics, and some advanced stuff that we are going to encounter in our wonderful journey. Anyways, let's start.

## What is TypeScript?
TypeScript is what is known as a superset —specifically a strict superset— meaning it is a language based on another language but with more features. Think of it kind of like what C++ was originally intended to be for C, before it evolved into its own distinct thing. In TypeScript's case (which we will call TS), it is a superset of JavaScript (JS). What makes it a strict superset is that it has everything JavaScript has and more, meaning they can never be exactly identical. JavaScript is famously known as the language of the internet—the only language that web browsers can natively understand. Because of this, TS has a unique constraint: all TS code must be transformed into JS to actually run. At its core, TS is just JavaScript with extra functionality. While modern tools like Bun make it feel like you are executing TS files directly, underneath the hood they are just providing a runtime that transpiles and runs it as JS. TS cannot run natively as TS; it always runs as JS. However, before your code turns into JS, it goes through the TypeScript compiler. This compilation step is what unlocks features like static types, generics, interfaces, and enums—tools that JS either lacks entirely or only poorly mimics. Because of this, all valid JS code is automatically valid TS code. And actually, the compiler isn't technically a compiler, but what is known as a transpiler. It checks everything in your code, makes sure there are no issues, and then just turns it into JS, erasing all of the TS unique parts of it, or transforming at least partially shared ones. This is known as the "Erase" principle. 

The type safety and features are only there in the editor and the build process. JS and TS runs at the exact same speed. There is an issue however in this case. It is vital to remember that interfaces and types are development-only constructs. When TS compiles to JS, they are completely deleted. This means TypeScript cannot validate real-world data at runtime. If an API promises to send you a number but actually sends a string, TypeScript's interfaces cannot stop it because those interfaces don't exist in production. Interfaces & Types are erased entirely, while classes & enums are kept and converted into real JavaScript objects. To be able to actually assure this, you would need some sort of runtime checker, or to use some JS keywords like `typeof` and `===`.

### History
TypeScript was made by Microsoft, released on October 2012 as open-source, and designed by Anders Hejlsberg (the creator of C#), to address issues with scaling in large JavaScript codebases, since with JavaScript's lack of strict types and the ability to just change types at will made it have a lack of type checker or compiler, which **SUCKS**. Because of the incredible increase to developer productivity and ease of use, it is now supported by basically every major frontend framework, and they either default to it or are written in it. And, since TypeScript 7.0, has a Go-powered compiler, so it is much faster in compilation and checking. And because of modern runtimes like Bun and Deno, and even Node.js (since v22.6), now allow you to execute TS files directly without needing an external transpiler. 

---

### Differences in execution
How TypeScript Actually Runs: `tsc` vs `tsx` vs Bun

To understand how TypeScript runs, you have to separate two completely different tasks: Type Checking and Type Stripping.

### Type Checking vs. Type Stripping

When you write TypeScript, your types (like `: string`, `interface User`, or `type Status`) act like invisible safety rails. JavaScript engines (like Node or V8) cannot read them.

    Type Checking: Reading every line of code to make sure your logic and types match up (e.g., ensuring you aren't passing a string to a function expecting a number). This requires scanning the whole project and takes processing time.

    Type Stripping: Erasing all the TypeScript syntax like white-out in milliseconds, leaving behind plain JavaScript.

### The 3 Tools Explained
**`tsc` (The Inspector & Builder)**

    What it does: Runs full type checking on your project. If your types are valid, it converts your code and writes actual .js files to your disk (usually inside a dist/ or build/ folder).

    Does it check types? YES. If you have a type error, tsc will catch it and report it.

    Does it execute code? NO. It only builds .js files for production.

**`tsx` (The Node Fast-Runner)**

    What it does: Uses a fast build tool (esbuild) to instantly strip types away in memory and hand the plain JavaScript to Node.js to execute immediately.

    Does it check types? NO. It completely ignores type errors so your code can start instantly.

    Does it execute code? YES.

**`Bun` (The Fast Runtime)**

    What it does: Works like tsx, but built natively into the Bun runtime engine. It strips types on the fly in memory using its own internal engine and runs the JavaScript immediately.

    Does it check types? NO. Just like tsx, Bun skips type checking for sub-millisecond execution speeds.

    Does it execute code? YES.

Now we aren't going to use Bun, `tsx`, or maybe even `tsc`, but it's important that you see the different ways that people use TypeScript, and the different ways that you can use it, and the way that the system works underneath so you understand that it isn't a completely different language, and that it's relationship to JavaScript determines it's functionality.

---

## Language Syntax and Features

Now, because TS is just a superset of JS (I sure hope you know exactly what I'm talking about and that you understood *everything*), it's syntax and features are, wow, practically identical! Let's start with what i think are the basic things that people should learn syntax and idiom wise. (spoiler alert it won't be that basic for long). Normally I'd say learn the basics of syntax for a language for a couple of categories, and then idioms and specific features. But I'm gonna give them all here.

---

### Comments
There are three types of comments:
```ts
//This is a single line comment.

/*
This is a multi line comment.
Wow so many lines
*/

/** 
 * This one is special. It's called TSDoc, and are used for actual documentation.
 * IDE's parse them and generate a rich hover view with stuff like IntelliSense.
 * 
 * @param userId - The unique identifier of the customer
 * @param amount - Total cost in USD
 * @returns A string that is the confirmation code that it was recieved
*/
function processPayment(userId: string, amount: number): string{
    return "tx12345"; //semicolons are optional.
}
```

### Variables

Variables in TypeScript are declared in a couple of ways:

```ts
//Type and value explicitly defined
let username: string = "Juan"; //The syntax for using types is : type.

//Type defined without value
let total: number;
total = 42;

//value defined without type
let isActive = true; //it infers it as a boolean

//no type, no value
let data; //will most likely infer it as any. It could infer it as something else depending on how it's used, but for the time being its any.
```

Now there are a couple of keywords that you can use to declare them as variables. Here I use let, but there are three more. One you should not use:

```ts
const //It is block scoped ({} <- in between these), and it CANNOT be reassigned.

let //Also block scoped, but it can be reassigned. Use it only when you know that the value will change.

var //This is function scoped. Don't use it at all, as it can cause a lot of bugs with scoping.
```

### Types
Now this is part of what makes TS so good and powerful. Now there are the primitive types:  
- `number` (no separate decimal or integer or anything) 
- `string` 
- `boolean` 
- `bigint` (arbitrary-precision integers, used when integer values need to exceed the precision available from `number`) 
- `undefined` 
- `null` 
- `symbol` (this one is interesting. It is used as a unique and inmutable identifier `Symbol("id")`).  

These are JavaScript's primitives. But TypeScript adds some more primitives: 

- **Arrays and tuples:** `number[]` or `Array<number>` (these are variable length), `[string, number]` (these are fixed length, fixed size, this doesn't mean inmutable)  
- `unknown` and `any`: Don't use `any`, use `unknown`. It's safer since it accepts any value, but you have to perform a type check on it. I'll explain how later.
- `void` and `never`: void indicates that a function will not return a useful value (duh), and never is code paths that can never occur (functions that always throw errors, or run infinite loops, as an example).

Now there are object shapes: `type` and `interface`. They are largely very similar, but differ in some small ways and functionallity.

- `interface`: Designed for object shapes and API contracts. It supports extension via `extends` and declaration merging.
- `type`: Can represent object shapes, but can also represent unions, primitives, tuples, and function signatures.
- Modifiers: You can make them optional `id?: number`, and inmutable `readonly id: number`

Example: 
```ts
//interface approach
interface User{
    readonly id: number;
    name: string;
    email?: string;
}

//type alias (another way to structure a block of data)
type UserType = {
    readonly id: number;
    name: string;
    email?: string;
}
```

Also, you can do this thing called type merging:
```ts
interface User{
    name: string;
}

interface User{
    age: number
}

//turns into
interface User{
    name: string;
    age: number;
}
```

I will talk about type inferencing quickly. You will see this later, but a lot of times, you don't actually have to write the type out unless it's custom made or ambiguous. You can use this to write less. For example, as you will see later, for arrow functions, you don't have to specify the type:

```ts
const numbers = [1,2,3];

numbers.map(n => n * 2)

//you don't need to do 
numbers.map((n: number) => n*2)
//because it already can infer it. This will be very useful for idiomatic TS in the future
```

### Functions
Functions are very similar to other languages. A basic function looks like this:
```ts
function add(a: number, b: number): number{
    return a+b
}

//optional and default parameters
function greet(name: string = "Guest", age?: number)

//arrow function (used a lot)
const multiply = (x: number, y: number): number => x*y;
```

Now we have to go more in-depth into arrow functions, because, like I said, they are used a lot.
```ts
//Inline callbacks.
const prices = [10,20,30];

const formattedPrices = prices.map((price: number): string => `$${price.toFixed(2)}`) //use `$` for formatted strings

//you can separate the function's type signature from the implementation
type MathOperation = (x: number, y: number) => number;

const subtract: MathOperation = (x, y) => x-y;

//you can also just call them normally
const result = subtract(1,2);

```

The biggest feature is what's called ***"lexical*** `this` ***binding"***.  
In standard functions, the value of the `this` keyword changes depending on *how* or *where* the function is called. In arrow functions, `this` is inherited from the surrounding scope where the function was created and never changes.

```ts
/*
The cause of this bug is because every `function` gets its own `this` slot every time it runs. It fills it based on
who calls it. greetLater() get `user`, but in `setTimeout`, you pass a normal standard function callback. When the timer finishes, it invokes the function as a standalone function, with no this slot.

Arrow functions differ, as they don't have a this. It handles the usage of this using lexical scoping (looking up the parent chain). So since the arrow function doesn't have a this, it looks for a this on the outer function, greetLater(). It does have a this, which is user, so it uses that.

Basically, it goes looking for anything that uses lexical scoping, including variables, by going "outward" ONLY, never "inward". It goes out scopes looking for the declaration of say, a variable name, and it goes all the way up the blocks ({}) until it reaches the global scope. If it isnt there, it doesnt exist.
*/
const user = {
  name: "Alex",
  greetLater() {
    setTimeout(function() {
      // 'this' is no longer 'user'! setTimeout called this function.
      console.log(`Hello, ${this.name}`); 
    }, 1000);
  }
};

user.greetLater();
// Output after 1 sec: "Hello, undefined"

const user = {
  name: "Alex",
  greetLater() {
    setTimeout(() => {
      //Arrow function remembers 'this' belongs to 'user'
      console.log(`Hello, ${this.name}`);
    }, 1000);
  }
};

user.greetLater();
// Output after 1 sec: "Hello, Alex"
```

<u>**What are they primarily used for?**</u>

- **Inline Callbacks & Higher-Order Array Methods:** They keep array operations clean, readable, and concise.

```ts
const numbers = [1, 2, 3, 4, 5];
const doubledEvens = numbers
    .filter((n) => n % 2 === 0)
    .map((n) => n * 2);
```

- **Event Listeners & Async Chains:** Perfect inside .then(), .catch(), or event handler callbacks where you want to keep the outer scope's this context intact.

- **Short Utility Functions:** Creating small, single-purpose transformations or helper formulas without cluttering the file with function keywords and return statements.

**Rules of use:**
- Use arrow functions when you want lexical `this` or when a concise function expression fits the situation. 
- Use normal functions when you need a dynamically determined `this`, or when a function declaration better communicates the structure of the code.

Ok, that was a lot. Time to go to:
### Unions, Intersections, & Literal Types

Types in TS can be composed in many different ways. you have already seen some, but here's more.

```ts
//Literal union type
type Role = "admin" | "user" | "guest";

//Union with primatives
type ID = string | number;

//intersecting object types. kind of like adding more, as an extension
type Employee = User & { salary: number };
```

### Enums
If you don't know what an enum is... tough cookies. Basically, it represents something that can only be one of that version at a time. Or, an enum is a set of named constants.

```ts
//By default, enums have numeric mapping. These automatically generate a reverse mapping, so you can look them up by number
enum Status{
    Pending, //0 
    Active, //1
    Denied   //2
}

console.log(Status.Active); //1

console.log(Status[1]) // "Active" 

//String enums DON'T have reverse mapping, and every member has to be initialized with a string literal. It's easier to inspect their values when debugging raw JS or API responses
enum Role{
    Admin = "ADMIN",
    User = "USER",
    Guest = "GUEST"
}

console.log(Role.Admin) // "ADMIN"

//const enums are cool because, normally, the TS compiler turns them into JS objects. If you dont wanna have that, you can get rid of it by prefixing it with const. It will replace all usages with raw values (inlining)

const enum Direction{
    Up,
    Down,
}

const move = Direction.Up

//This turns into this in JS: var move = 0; /*Up*/
```
Now you can mix types in the same enum, but it should be avoided.
Now, since this does generate extra JS code at runtime, many devs use `as const` instead:

```ts
//basically, without as const, it infers that you could mutate it. So it infers them as primitives.
//Pending would be infered as string. So this means that you could reassign it.

//Status.Pending = "AnyRandomString"

//but as const does three things:
//properties become readonly
//string values remain as literal types (not the string, but literally "PENDING")
//nested values recieve the same treatment recursively

const Status = {
    Pending: "PENDING", // Key = "Pending"  |  Value = "PENDING"
    Active: "ACTIVE" // Key = "Active"   |  Value = "ACTIVE"
} as const;

//this creates a union type "PENDING" | "ACTIVE
// 1. typeof Status            --> { readonly Pending: "PENDING", readonly Active: "ACTIVE" }
// 2. keyof typeof Status      --> "Pending" | "Active"
// 3. typeof Status[keyof ...] --> "PENDING" | "ACTIVE"
type StatusType = typeof Status[keyof typeof Status];
```

### Type narrowing

This is actually pretty useful. The TS compiler lets you use type guards and if statements to narrow union types down to specific branches. And to eliminate null or undefined.  
For example:
```ts
function printId(id: string | number){
    if (typeof id === "string"){
        //TS knows that it can use that method because what is in here is a string
        console.log(id.toUpperCase());
    } else {
        //TS know that its a number here
        console.log(id.toFixed(2));
    }
}
```

### Modern operators
- **Optional Chaining** (`?.`): To safely access properties on objects that might be `null` or `undefined`. Returns `undefined` if it is indeed those. No error throwing here. Also applies to calling functions, it doesn't execute them if it short-circuits.
- **Nullish Coalesing** (`??`): Fallback operator for `null` or `undefined`(JS has 6 falsy values: `false`, `0`, `""` (empty string), `null`, `undefined`, and `NaN`. You might think to use `||` instead of this, but it falls back if it's **ANY** falsy value. `??` doesn't). Think of it like being able to add a default if it isn't there.
- **Non-null assertion** (`!`): Tells the compiler "trust me bro,  I swear this isn't, you guessed it, `null` or `undefined` (don't use it too much)
- **Type Assertion** (`as`): You can use this to say that this value shows up as this "I know more about this type than you. Treat it as this". For example: `const value: unknown = "Hello"; -> const text = value as string`. It now treats it as a string, but it does not convert it to the value 
- **Satisfies** (`satisifies`): A lesser-known cousin of `as`. Where `as` says "trust me bro", and can actually even make TS forget what it knew (since it's an assertion), `satisfies` says "check this against the type, but dont change what you inferred". You get both validation *and* you keep the specific, literal type. Remember that this is only in the type-checker's head.

An example:
```ts
type Colors = "red" | "green" | "blue";

// with `as`: pure assertion, TS won't complain even if you're wrong
const badge = "purple" as Colors; // no error here?? bad.

// with satisfies: TS actually checks it
const goodBadge = "red" satisfies Colors; // fine, "red" IS a Color

const config = {
    color: "red",
    size: 12
} satisfies { color: Colors; size: number };

// config.color stays inferred as the literal "red", not widened to plain `string`
// but TS DID verify "red" fits inside Colors when you wrote it
```

This pairs nicely with `as const`.

### Splitting Code Across Files

Up until now we've talked about all our code like it lives in one giant file forever, but real projects obviously don't work that way. You break things up, and then those files need a way to talk to each other. That's what `import` and `export` do.

A file that exports something is letting other files borrow it. A file that imports something is doing the borrowing. And remember from a few sections back — the second a file has an `import` or `export` in it, TS treats it as a module, not a plain script. That's the whole reason top-level `await` worked the way it does (this will come in the future).

```ts
// utils.ts
export function double(n: number): number {
    return n * 2;
}

export const PI = 3.14159;

// you can also export a "default" — only one allowed per file
export default function greet(name: string) {
    return `Hello, ${name}`;
}
ts
// main.ts
import greet, { double, PI } from "./utils";
//     ^ default import        ^ named imports — names have to match exactly, unless you rename them

console.log(greet("Sam"));
console.log(double(21));

// renaming an import, if it clashes with something or you just feel like it
import { double as timesTwo } from "./utils";
```

**Small opinion time**: lean on named exports over default exports when you can. Named exports keep their actual name wherever you import them, your editor can auto-import and rename them reliably, and you can have as many per file as you want. Default exports are fine, but since you can rename them on import, nothing stops five different files from calling the same thing five different names, which is a great way to confuse yourself in three weeks.

**One TS-only thing**: sometimes you're importing something purely as a type, not an actual value (an `interface` or `type`, say). You can say so directly:

```ts
import type { User } from "./types";
```

This tells the compiler "this vanishes completely once compiled, it was never a real JS value", which makes sense, since types get erased anyway. Being explicit here can even speed up builds on bigger projects.

Now, once your project's a pile of files importing from each other, something has to tell TS how it's supposed to check and build all of it. That's `tsconfig.json`. It's a settings file for the compiler, sitting at your project root. You usually won't write one from scratch (starter templates generate it), but you should know what the important bits mean:

```jsonc
{
  "compilerOptions": {
    "strict": true,        // turns on ALL the strict checks. leave this on, always
    "target": "ES2022",    // what JS version your code compiles DOWN to
    "module": "ESNext",    // what module syntax gets output
    "outDir": "./dist",    // where compiled JS lands
    "moduleResolution": "bundler" // how TS goes looking for the stuff you import
  },
  "include": ["src"]       // which folders TS actually pays attention to
}
```

`strict` matters the most day-to-day. It's what stops a variable from secretly being `null` or `undefined` when you never said it could be. Without it, TS is a lot more forgiving, and a lot less useful.

You might also run into old code using `require()` and `module.exports` instead, and that's CommonJS, the way Node did modules before ES Modules were standard. You probably won't write it yourself here, but don't be surprised finding it in an older dependency.

---

## Advanced Concepts
Now we're getting into the juicy stuff. These are more advanced concepts that are a part of the TypeScript language that, for some cases, we will see and use, unavoidably. Let's get started.

### Generics
Now these are present everywhere. So you should probably know how to use them. Or not you typeless, featureless Python slave.

Anyways, generics are a type system feature that allows code or types to work with type parameters/different types

Here's an example:
```ts
// Without Generics (using 'any' loses type information):
function getFirstAny(arr: any[]): any {
  return arr[0]; // Returned item is typed as 'any'
}

// Simple generic function
function wrapInArray<T>(value: T): T[] {
  return [value];
}

const numArr = wrapInArray(10);     // inferred as number[]
const strArr = wrapInArray("hello"); // inferred as string[]

//you can also add constraints
// T MUST be an object that has at least a string 'id'
function logId<T extends { id: string }>(item: T): void {
  console.log(item.id);
}

logId({ id: "123", name: "Alice" }); //Allowed
logId({ name: "Bob" }); //TS Error: Property 'id' is missing

//It's also good for generic interfaces
interface ApiResponse<TData> {
  status: number;
  message: string;
  data: TData; // The shape of data varies depending on the endpoint
}

type UserProfile = { id: string; name: string };
type Product = { id: number; price: number };

// Reuse the response structure with different payload shapes:
type UserResponse = ApiResponse<UserProfile>;
type ProductResponse = ApiResponse<Product>;
```

Generics can also show up in other places
```ts
type Success<T> = {
    value: T;
};

type Failure = {
    error: string;
};

type Result<T> = Success<T> | Failure;

//Pretty self explanatory, There's two types, and we have a union type with a generic parameter, and success can take that in.
```

### Discriminated Unions
These are a union where every member has a shared property whose value identifies which member it is.

Time to explain it.

Instead of this:
```ts
type Success<T> = {
    value: T;
};

type Failure = {
    error: string;
};

type Result<T> = Success<T> | Failure;
```
We can do:
```ts
type Success<T> = {
    kind: "success";
    value: T;
};

type Failure = {
    kind: "failure";
    error: string;
};

type Result<T> = Success<T> | Failure;
```

Now they both have a `kind`, but the value is different. That property is the discriminant.

We can use this with type narrowing like this:
```ts
function handleResult(result: Result<string>){
    if(result.kind === "success"){
        console.log(result.value);
    } else {
        console.log(result.error);
    }
}
```

Since TS sees that if statement, it can look and see that the only member of the `Result` union that has `kind: "success"` is `Success<string>`, so in that branch, it knows that `result.value` exists, and vice versa in the else. 

This can be really useful for more complex types that can now hold several different shapes, and you can use that discriminant to tell the difference and use type assertion so the compiler can too.

### Asynchronous code (Async)
This, **THIS** is the big boy.

Ok. Even though threading doesn't really have to do with it, since other languages have async that doesn't require multithreading, in JavaScript, and by extension TypeScript's case, it's pertinent.

JavaScript runs on a single thread, a thread being a unit of execution. Think of it as a conveyor belt. It can only execute one line of code at a time. If a function that gets data from a server or that reads a large file takes 3 seconds, a synchronous program stops dead. Nothing else can be done until that completes. That freezes UI, interactions, and other code, making everything completely stand still.  

Asynchronous execution solves this by allowing long-running tasks to run in the background, instead of freezing the entire program while it waits. Remember that JavaScript runs on a runtime or a browser engine. They can do other things. JS starts the task, and immediately goes to run the rest of the code, and when the operation finishes, it goes back to handle the result.

To illustrate it a bit more simply:
- **Synchronous** (blocking): Step 1 runs &rarr; App waits for Step 1 to complete &rarr; Step 2 runs.
- **Asynchronous** (Non-blocking): Step 1 starts in the background &rarr; Step 2 runs immediately &rarr; Step 1 finishes when ready.

I now also need to explain the syntax and parts that make it work, but here is an example with none of those.

```ts
console.log("1. Order food");

//This is to simulate a heavy, 3 second task that freezes the main thread
const startTime = Date.now();
while (Date.now() - startTime < 3000){
    //The app is frozen now
}

console.log("2. Food is cooked")
console.log("3. Talk to friend")

//It outputs like this:
//1. Order food
//(3 sec freeze)
//2. Food is cooked
//3. Talk to friend
```
On the other hand:
```ts
console.log("1. Order food");

setTimeout(() => {
    console.log("2. Food is cooked");
}, 3000);

console.log("3. Talk to friend")

//Output order:
//1. Order food
//3. Talk to friend
//(3 sec pass in the background while the code keeps running)
//2. Food is cooked
```

Now, I hope these very simple examples showed you the general idea. Now let's get into syntax!

---

Now, there are these things called `Promise<T>`. These are, in simple words, containers where a future value will be placed once it is obtained. The program, after stopping a function for execution, but wants to continue, still needs something for the function to have returned. For this, we need that container, a `Promise` that something will be there soon. Once that value is obtained, the value is placed there and can be unwrapped.

Think of it as a ticket for your food order. The food isnt there, you have a ticket that says that that food will be ready at some point. If its pending, you are waiting. If its fulfilled, it's ready, and you get your food (data). If it's rejected, something went wrong, and you don't get your food (error).

Now, to have a function be asynchronous, you need to add `async` to its declaration. This says that you can use the `await` keyword (I will explain it soon), and that it will return a Pending `Promise` right away once it reaches the first `await`, since the function runs synchronously until then.

`await` temporarily pauses execution of that function until the background task you are asking to do finishes. Once it does, `await` "unwraps" the result so you can use it as normal data. You can only use `await` in an `async` function except for some exceptions, which I will mention.

Now, for an async function, you can have a `return`. The return statement resolves the outer, empty `Promise` that gets created when it's called. That value gets passed into the container. The `return` in an async function also always returns a `Promise`, no matter what. You do need an `await` or `.then()` to get that value out through.

***Quick context on Modules***  
In modern JS/Ts, any file that contains an `import` or `export` statement is treated as an **ES Module** (an isolated code file). In module files, TS allows you to use `await` directly at the top level of the file without wrapping it in an `async` function. If a file doesn't have imports/exports, it's treated as a global "script", where top level `await` isnt allowed.

**There are a few ways to get that `Promise` value out if not in an `async` function:**
- Using `.then()`: This method is built into all Promises. It lets you get the value out using a callback function without needing `await`. It works everywhere
    
```ts
getAge().then((age) => {
    console.log(age);
})
```
- Top level `await`: This is the more modern standard. You can use `await` at the root level of any module file without wrapping it in an `async` function.
```ts
const age = await getAge();
console.log(age);
```
- Async `main()` function: If you dont have moduled scripts, you can wrap the entry point in an async function
```ts
async function main(){
    const age = await getAge();
    console.log(age);
}

main();
```

Now, this is an example of a normal, common asynchronous function:
```ts
async function getUsername(): Promise<string> {
  // `await` pauses inside this function until fetch completes & unwraps the Response
  const response = await fetch("https://api.example.com/user");
  const data = await response.json();

  return data.username; 
}

// How the caller receives it:
const userPromise = getUsername(); // `userPromise` is a Pending Promise<string>

userPromise.then((name) => {
  console.log(name); // Runs once the explicit `return` inside getUsername completes!
});

//Error handling for async functions can be done as so:
//Use this version when the function itself knows how to handle the failure, so that it's callers don't have to handle it.
async function getUsername(): Promise<string | null> {
    try { //Do the work you want to check for errors here
        const response = await fetch("https://api.example.com/user");
        const data = await response.json();
        return data.username;
    } catch (error) { //If there is an error, or exception, handle it here
        console.error("Failed to get username:", error);
        return null; // swallow it, hand the caller a safe fallback instead
    }
}

//Use this version when the function just does the job, and each caller can handle the error differently.
async function getUsername(): Promise<string> {
    const response = await fetch("https://api.example.com/user");
    const data = await response.json();
    return data.username; // if fetch fails, THIS function's promise rejects
}

// the caller decides what "failure" actually means here
try {
    const name = await getUsername();
    console.log(name);
} catch (error) {
    console.error("Couldn't load username:", error);
}

//General rule: wrap the `await` that can fail, not just the whole function body
```

### Classes
Now, you know what they are (I hope, if not jeez), but these are present in JS, so they survive erasure. Let's show you and talk about the specific implementation.

```ts
class Person {
    name: string;
    age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    greet(): string {
        return `Hi, I'm ${this.name}`;
    }
}

const alex = new Person("Alex", 25);
console.log(alex.greet()); // "Hi, I'm Alex"
```

The `constructor` runs when you make an object with `new`. Visually, it looks oddly similar to Java, especially in how you write constructors. But, you can get rid of the `this.name = name` stuff and just use **parameter properties**. You just need to put visibility modifiers on the constructor parameters, and it'll declare the field and assign it for you.

```ts
class Person{
    constructor(
        public name: string,
        private age: number
    ){}
}

//Does the same thing as the version above, but its shorted
```

Now, you can only use these visibility modifiers on classes, and not interfaces or types. They have strict rules on where they can be used, and to change that you have to import/export.

- `public`: Accesible from anywhere
- `private`: Only accessible from inside the class. Not even a subclass can access it.
- `protected`: `private` but a subclass can access it. Wow.
- `readonly`: can be used with any of them. Can only be set once (normally in the constructor) and never again.

Remember, these are ***compile-time only*** restrictions. These all disappear when compiled into JS (JS didn't even have actual private fields until recently).

**Implementing an interface**  
Remember the `User` interface from the Types section? A class can promise to match a shape like that using `implements`:

```ts
interface User {
    readonly id: number;
    name: string;
    email?: string;
}

class Customer implements User {
    readonly id: number;
    name: string;
    email?: string;

    constructor(id: number, name: string) {
        this.id = id;
        this.name = name;
    }
}
```

`implements` doesn't copy anything over for you, it just makes TS yell at you if `Customer` doesn't actually fulfill every field and method `User` demands. Good way to make sure a class stays honest to a contract.

**Inheritance**  
This was mentioned in AP CS A, but anywho. Classes can extend other classes with `extends`, inheriting their fields and methods.
```ts
class Animal {
    constructor(public name: string) {}

    makeSound(): string {
        return "...";
    }
}

class Dog extends Animal {
    makeSound(): string {
        return "Woof!";
    }
}

const rex = new Dog("Rex");
console.log(rex.name); // "Rex", inherited from Animal
console.log(rex.makeSound()); // "Woof!", overridden in Dog
```
`super()` is how a subclass calls it's parent's constructor, and it has to be the first thing you do in a subclass's constructor if you write one:
```ts
class Dog extends Animal {
    constructor(name: string, public breed: string) {
        super(name); // has to happen before anything else
    }
}
```

### Function overloading
You've also already seen this concept, but here's one cool way you can use it.
```ts
function makeId(value: number): string;
function makeId(value: string): string;
function makeId(value: number | string): string {
    if (typeof value === "number") {
        return `id-${value}`;
    }
    return value;
}

makeId(5);      // "id-5"
makeId("abc");  // "abc"
```

The firs two lines aren't functions, they're signatures, that define the parameter and return implementation for different types. The actual implementation below has to be able to handle every case you promised existed. A little niche, but cool.

---

### TODO: Utility functions and types

---

# End
Well, I hope you guys enjoyed my tutorial on TypeScript. Any questions, and you can probably go ask AI or Google for a better answer than I could provide. Thank you for reading, and happy coding.



*Written by: Juan González*  
*On 2026-9-23*  
*Last updated: 2026-9-24*