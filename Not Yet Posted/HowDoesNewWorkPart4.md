# How Does the `new` Keyword Work in JavaScript?, part 4: What about classes?

While reading the previous parts of this series you may have been screaming at your screen **"But JavaScript does have classes!"**, and well, you're right, JavaScript does have classes. Some places on the internet say that classes are just syntactic sugar for functions and the prototypal inheritance pattern, and at surface-level they are, but if we dig deeper we can see some differences between JavaScript functions and JavaScript classes.

To get started, let's look at a simple class, and its equivalent as a function. For that let's create a new class and then use the TypeScript compiler to convert it to a function. Classes were introduced in ES6, so set the TypeScript compiler's target to ES3.

### TypeScript Class
```typescript
class RandomClass {
    x: Number;

    constructor() {
        this.x = Math.random();
    }

    getX() {
        return this.x;
    }
}

let random = new RandomClass();
```

### Class compiled to JavaScript ES3
```javascript
var RandomClass = /** @class */ (function () {
    function RandomClass() {
        this.x = Math.random();
    }
    RandomClass.prototype.getX = function () {
        return this.x;
    };
    return RandomClass;
}());
var random = new RandomClass();

```

As you can see, the way TypeScript converts classes to functions is by:
- Creating a function that:
    - Creates a constructor function equivalent to the constructor declared inside the original class 
    - Adds any class methods to the function's prototype (so that they're available to all instances of RandomClass)
    - Returns the newly created constructor function
- Running that function and storing its return value (the function constructor described earlier) inside a variable

The TypeScript compiler is pretty much adapting the things that a class does to JavaScript's more functional programming style.

Now, what are some of the differences between classes and functions?

## All code ran inside a class is in strict mode

Open your browser's developer console and try running the following code:

```JavaScript
class RandomClass {
    x;

    constructor() {
        this.x = Math.random();

        delete Object.prototype;
    }

    getX() {
        return this.x;
    }
}

new RandomClass();
```

The console will throw the following error:

```
Uncaught TypeError: Cannot delete property 'prototype' of function Object() { [native code] }
```

That's because strict mode turns common mistakes into errors (in this case the mistake is trying to delete an undeletable property). Typing `delete Object.prototype` outside a class declaration and with strict mode disabled would just silently fail.

## Class declarations are not initialized when hoisted

We could honestly make an entire blog post related to hoisting, but for now, just keep in mind that hoisting is the process of moving declarations to the top of the current scope. Class declarations are not initialized when hoisted, so the following code will throw an error:

```JavaScript
new RandomClass(); // Throws ReferenceError: Cannot access 'RandomClass' before initialization

class RandomClass {
    x;

    constructor() {
        this.x = Math.random();
    }

    getX() {
        return this.x;
    }
}
```

Functions are initialized when hoisted:

```javascript
let random = new RandomClass();
console.log(random.getX()); // Prints a random number

function RandomClass() {
    this.x = Math.random();

     RandomClass.prototype.getX = function() {
        return this.x;
    }
}
```

Why aren't classes initialized? As classes can extend other classes, it's important to make sure that the parent class is the correct value when the child class is initialized. If classes were initialized when hoisted, what would be the following class's parent?

```javascript
var AnotherClass = 5;

class AnotherClass {}

class RandomClass extends AnotherClass{
    x;

    constructor() {
        this.x = Math.random();
    }

    getX() {
        return this.x;
    }
}
```

Speaking of extending classes, next time we'll look at how class inheritance is translated to JavaScript's prototypal inheritance pattern.