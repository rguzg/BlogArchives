# How Does the `new` Keyword Work in JavaScript?, part 3: Fixing our buggy `new` implementation

Last time, we [created a custom implementation of the new operator](./HowDoesNewWorkPart2.md), that had a pretty subtle bug. To refresh your memory, if we tried to modify the prototype of our new object, the object wouldn't change:

```javascript
Character.prototype.inheritedProperty = 'Inherited'

console.log(Eren.inheritedProperty);

// The above statement prints:
undefined
```

The problem with our code was this line:

```javascript
Object.setPrototypeOf(newObject, {...objectPrototype, ...constructor.prototype});
```

I'll give you a few seconds to see if you can figure out what's going on...<br>
...<br>
...<br>
...<br>

OK, a few seconds have passed! The problem with our code was that by spreading the contents of the prototype inside a new object, we were setting that new object's prototype to a new object that had the same contents as the prototype and **not to a reference to the prototype property of the constructor**. We weren't getting the new properties added to the prototype because our custom object didn't care about the constructor's prototype after our `CoolNew` function had finished running!

We can fix our implementation by doing this:

```javascript
// There's no need to worry about the `...objectPrototype` part, because the properties it has are already defined inside constructor.prototype.
Object.setPrototypeOf(newObject, constructor.prototype); 
```

But, there's still stuff we can do to make our code better. `Object.setPrototypeOf` is [reeeeally slow](https://mathiasbynens.be/notes/prototypes). Instead of using it, we can use the `Object.create` method:

```javascript
Object.create(constructor.prototype);
```

`Object.create` creates a new object and sets its prototype to the one specified on the function's first argument. 

Our final code would look like this:
```javascript
function CoolerNew(constructor, args){
        if(typeof constructor != 'function' || !constructor.prototype){
                
            // This is actually one of the valid answers to the question from last time! Arrow functions don't have a `prototype` property, so they aren't constructors.
            throw new TypeError(`${constructor.name || constructor} is not a constructor`);
        }

        let newObject = Object.create(constructor.prototype);

        // Set the `this` value of the constructor function to the new object.
        let returnValue = constructor.apply(newObject, args);

        // Implementation of the extra rule.
        return typeof returnValue === 'object' ? returnValue : newObject;
    }
```

`Object.create` doesn't run the constructor function, so keep that in mind when using it.