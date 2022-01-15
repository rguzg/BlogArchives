# How Does the `new` Keyword Work in JavaScript?, part 1: The prototype property of a function

Most programmers that have already dipped their toes into Object-Oriented Programming probably already know what the `new` keyword does, you use it to create a new instance of a class but, in the case of JavaScript, what is the `new` keyword actually doing? 

For starters, the `new` keyword is not even creating a new instance of a class, because JavaScript doesn't have classes! Remember that JavaScript uses prototypes to describe the structure of new objects instead of classes. With that in mind, we can say that the `new` keyword is in some way assigning the properties specified in the prototype that's being used to create the new object to that brand new object. But how does JavaScript know which prototype to use? To answer that question we need to explore the innards of functions in JavaScript.

Everything [^1] in JavaScript is an object, even functions, this means that functions have properties that we can explore. The one we're interested in is the `prototype` property. Open your favorite web browser's developer tools and define the following function, make sure to define it as a regular function and not as an arrow function:

```javascript
function foo() {
    console.log("I'm a function");
}
```

Now try printing to the console `foo.prototype`, you'll get something like this:

```javascript
{constructor: ƒ}
    constructor: ƒ foo()
    [[Prototype]]: Object
```

The `prototype` property of a function is where the prototype that will be assigned to a brand new object when using the `new` keyword is stored. Let's examine what's inside this object:
- The `constructor` property stores a reference to the function that is being used as the new object's constructor
- The ``[[Prototype]]`` property stores the prototype that `prototype` is inheriting from. In this case, the new object is inheriting from `Object`, this makes functions like `isPrototypeOf` available to the newly created object.

As a side note: `prototype` and `[[Protoype]]` are two different properties. `prototype` stores the prototype that will be assigned to a brand new object when using the `new` keyword. `[[Prototype]]` stores the prototype that the object inherits from.

Now, what would happen if we wanted to add something to the new object's prototype? Type the following on the browser console:

```javascript
foo.prototype.inheritedProperty = 42;
```

And then print `foo.prototype` to the console:

```javascript
{inheritedProperty: 42, constructor: ƒ}
    inheritedProperty: 42
    constructor: ƒ foo()
    [[Prototype]]: Object
```

There's the `inheritedProperty` we added to `foo.property`! When we create a new `foo` type object, it'll inherit that `inheritedProperty` property. Any already existing objects that are an instance of `foo` will also have that property.

JavaScript knows which prototype to assign to a brand new object by looking for a function's `prototype` property.

As an exercise to the reader: Why can't the constructor function be an arrow function? There's two right answers!

Next time, we'll look at what JavaScript's doing internally when using the `new` keyword.

[^1]:  `null` and `undefined` aren't objects