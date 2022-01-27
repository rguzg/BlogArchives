Now, what would happen if we wanted to extend the RandomClass class?

### TypeScript Classes
```typescript
class SuperClass {
    a: Number;

    constructor() {
        this.a = 1;
    }

    getA(){
        return this.a;
    }
}

class RandomClass extends SuperClass {
    x: Number;

    constructor() {
        super();

        this.x = Math.random();
    }

    getX() {
        return this.x;
    }
}

let random = new RandomClass();
```

### Classes compiled to JavaScript ES3
```javascript
var __extends = (this && this.__extends) || (function () {
    var extendStatics = function (d, b) {
        extendStatics = Object.setPrototypeOf /* For simplicity sake, some code was skipped here. The skipped code is just  */
        return extendStatics(d, b);
    };
    return function (d, b) {
        if (typeof b !== "function" && b !== null)
            throw new TypeError("Class extends value " + String(b) + " is not a constructor or null");
        extendStatics(d, b);
        function __() { this.constructor = d; }
        d.prototype = b === null ? Object.create(b) : (__.prototype = b.prototype, new __());
    };
})();
var SuperClass = /** @class */ (function () {
    /* Doing the same procedure for turning a class into a function */
}());
var RandomClass = /** @class */ (function (_super) {
    __extends(RandomClass, _super);
    function RandomClass() {
        var _this = _super.call(this) || this;
        _this.x = Math.random();
        return _this;
    }
    RandomClass.prototype.getX = function () {
        return this.x;
    };
    return RandomClass;
}(SuperClass));
var random = new RandomClass();

```
   