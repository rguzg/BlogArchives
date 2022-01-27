# How Does the `new` Keyword Work in JavaScript?, part 2: The inner workings of the `new` operator

Last time, we looked at [How JavaScript knows which prototype to assign to a brand new object](./HowDoesNewWorkPart1.md). Today we'll look at what JavaScript's doing internally when we use the `new` keyword. For that we'll create our own method that replicates `new`'s functionality.

Open your favorite code editor, create a new file and define the following function:

```javascript
function Character(name, series){
    this.name = name;
    this.series = series;
}

Character.prototype.toString = function(){
    return `Hi! I'm ${this.name} from ${this.series}`;
}
```

This will be the constructor function we'll use to try out our implementation of the `new` keyword. Let's start writing our custom `new` implementation!

The pseudocode of what we want to do is the following:
1. Create a new, empty object
1. Assign the contents of the constructor's `prototype` property to the new object
1. Call the constructor function with the new object as its `this` value. If you're not familiar with how `this` works in JavaScript, ~~god save your soul~~, what we're doing here is basically allowing the constructor to modify the new object.
1. An extra rule for the `new` keyword is that if the constructor returns an object, we return that object instead of our newly created object. This extra rule can allow you to hide properties from the end user!

Turning that pseudocode into JavaScript:

```javascript
    function CoolerNew(constructor, args){
        if(typeof constructor != 'function' || !constructor.prototype){
                
            // This is actually one of the valid answers to the question from last time! Arrow functions don't have a `prototype` property, so they aren't constructors.
            throw new TypeError(`${constructor.name || constructor} is not a constructor`);
        }

        let newObject = {};

        // The name of the property that stores an object's prototype is implementation specific, so we use this function to get a reference to the object's prototype.       
        let objectPrototype = Object.getPrototypeOf(newObject);

        // This is wrong on purpose!
        Object.setPrototypeOf(newObject, {...objectPrototype, ...constructor.prototype});

        // Set the `this` value of the constructor function to the new object.
        let returnValue = constructor.apply(newObject, args);

        // Implementation of the extra rule.
        return typeof returnValue === 'object' ? returnValue : newObject;
    }
```

Now, let's try it out and compare the results with the `new` keyword:

```javascript
// CoolerNew Implementation
let Eren = CoolerNew(Character, ['Eren', 'Attack on Titan']);

console.log(Eren);

// The above statement prints:
{name: 'Eren', series: 'Attack on Titan'}
    name: "Eren"
    series: "Attack on Titan"
    [[Prototype]]: Object
        toString: ƒ ()
        [[Prototype]]: Object

console.log(Eren.toString());

//The above statement prints:
"Hi! I'm Eren from Attack on Titan"

// Using the new keyword
let Mikasa = new Character('Mikasa', 'Attack on Titan');

console.log(Mikasa);

// The above statement prints:
Character {name: 'Mikasa', series: 'Attack on Titan'}
    name: "Mikasa"
    series: "Attack on Titan"
    [[Prototype]]: Object
        toString: ƒ ()
        constructor: ƒ Character(name, series)
        [[Prototype]]: Object        

console.log(Mikasa.toString());

// The above statement prints:
"Hi! I'm Mikasa from Attack on Titan"
```

As you may see, the objects are pretty much the same. The `Eren` object didn't inherit the `constructor` property because that property is hidden, so the spread syntax ignores it.

There's a subtle bug in our implementation of `new`. What would happen if we wanted to add a property to the Character prototype?

```javascript
Character.prototype.inheritedProperty = 'Inherited'

console.log(Mikasa.inheritedProperty);

// The above statement prints:
'Inherited'

console.log(Eren.inheritedProperty);

// The above statement prints:
undefined
```

Our custom object is not inheriting things properly! We'll see why this is happening and how to fix it [next time.](./HowDoesNewWorkPart3.md)