> 16 - Nov - 2024

## Prototypal Inheritance in Javascript `__proto__` [[Prototype]]

1. Everything in JavaScript - trying to be an object, by the help of `Boxing` concepts.
    * **Boxing**: Converts primitives to objects.

2. When nothing exists in JavaScript, two things are always present...
    * Object Functions --- & to help him have a helper is...
    * a --> `prototype` key, inside Object
        - this prototype key contains default DNA/behavior of an Object...
        - value of the prototype key is also an Object...

#


```js
const newObject = { }

new Object --> prototype --> toString() --> inject it --> newObject
```

```js
const newObject = { }

newObject.[[Prototype]] = { }               // happen internally - create a hidden property...
newObject.[[Prototype]] = Object.prototype  // happen internally - linked with prototype...

* js create this hidden key `[[Prototype]]` & link this with Object.prototype
* js did it internally... for us to access all default Objects behavior...

newObject.__proto__ === Object.prototype 
===> true
You cant see/access your DNA but you can test your DNA... 
as like we cant access this `[[Prototype]]` but we can test this DNA by accessing `__proto__` key inside every object...

so is this the `[[Prototype]]` key, --> where js inject all default DNA/behavior inside that newly created object...

`[[Prototype]]` is like DNA, where js all default behaviors are saved here...
```

* JavaScript is a `Link` based language... 
    - when we create a new object {...}, then its internally linked with that function `prototype key`, that create this object...

    - every object - created by a function... & then this object will be attached with that function prototype key
    - its mean in js every function have a `prototype key`...




#

* As long as the app is running, the variable's value is retained (temporarily) or stored in RAM. This is called `Memorization`.
* If the variable's value is saved in localStorage, then it is considered `Caching`.