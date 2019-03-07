

### Call stack 

* Global Memory (Global Scope or Global Variable Environment or Stack frame): contains global variables and function declarations for later use

* Global execution context

* call stack (message queue): push all the execution, then pop them in FILO

* Event loop: Refers to the loop that checks if there are more messages in the message queue.

* [A good example of event loops](https://gist.github.com/jesstelford/9a35d20a2aa044df8bf241e00d7bc2d0)


  ```javascript
  console.log(1);
  setTimeout(()=>console.log(2), 0);
  console.log(3);

  // 1
  // 3
  // 2


  let startTime = Date.now();
  setTimeout(function() {
    console.log(Date.now() - startTime);
  }, 500);

  while (Date.now() - startTime < 1000) {} // Pause for 1   second.

  // 1001 (or 1000)
  ```

* `local execution context` and `local memory` are for each internal scope. for example functions.

---

### Hoisting 

Using a variable before its declaration.

* JavaScript Declarations are Hoisted

  ```javascript
  console.log(x); // 12
  var x;
  x=12;


  console.log(y); // ƒ y(){return "hi"}
  function y(){return "hi"}; // function declaration
  ``` 

* JavaScript Initializations are Not Hoisted

  ```javascript
  console.log(x); // undefined
  var x=12;

  console.log(y); // undefined
  var y=function(){return "hi"}; // function expression
  ```

---

### Falsy and truthy values

```javascript
false
0
null
undefined
""
NaN
-0
```

* Everything else is truthy.

---

### Non-numeric values in numeric expressions

* Type coercion: when the operands of an operator are different types, one of them will be converted to an "equivalent" value of the other operand's type.

* [More information](https://dev.to/promhize/what-you-need-to-know-about-javascripts-implicit-coercion-e23)

---

### Expressions, Statements and Expression Statements

* Expression
  * produce value. don’t necessarily change state.
  * A function expression is an expression, what you call an anonymous function
  * A named function expression is an expression, like an anonymous function, but it has a name

* Statement: 
  * Basically, statements perform actions, they do things.
  * In javascript, statements can never be used where a value is expected. So they cannot be used as function arguments, right-hand side of assignments, operators operand, return values…
  * A function declaration is a statement

  ```javascript
  //These are all javascript statements:
  if
  if-else
  while
  do-while
  for
  switch
  for-in
  with (deprecated)
  debugger
  variable declaration
  ````

* [More info](https://dev.to/promhize/javascript-in-depth-all-you-need-to-know-about-expressions-statements-and-expression-statements-5k2)

---

### requestAnimationFrame

* Tells the browser that you wish to perform an animation and requests that the browser call a specified function to update an animation before the next repaint.
  
  * Nedds polyfill for IE9, IOS 5 and Android

  ```javascript
  /* css
  div {
    width: 10px;
    height: 10px;
    background: orange;
    float: left;
  }
  */

  var globalID;

  function repeatOften() {
    $("<div />").appendTo("body");
    globalID = requestAnimationFrame(repeatOften);
  }

  globalID = requestAnimationFrame(repeatOften);
  ```

---

### Bitwise operators

Operator | Usage | Description
---------|------------|------------
Bitwise AND |	a & b	| Returns a one in each bit position if bits of both left and right operands are ones.
Bitwise OR |	a \| b	| Returns a one in each bit if bits of either left or right operand is one.
Bitwise XOR |	a ^ b |	Returns a one in a bit position if bits of one but not both left and right operand are one.
Bitwise NOT |	~ a |	Flips the bits of its operand.
Left shift |	a << b |	Shifts a in binary representation b bits to the left, shifting in zeros from the right.
Sign-propagating right shift |	a >> b |	Shifts a in binary representation b bits to the right, discarding bits shifted off.
Zero-fill right shift |	a >>> b |	Shifts a in binary representation b bits to the right, discarding bits shifted off, and shifting in zeros from the left.

---

### Classes, Factory functions and prototype chaining

* A JavaScript class is a type of function.
* Classes in JavaScript are often described as providing "syntactical sugar" over prototypes and inheritance in that they offer a cleaner and more elegant syntax. 

  ```javascript
  function Hero(name, level) {
      this.name = name;
      this.level = level;
  }

  // Adding a method to the constructor
  Hero.prototype.greet = function() {
      return `${this.name} says hello.`;
  }

  // Creating a new constructor from the parent
  function Mage(name, level, spell) {
      // Chain constructor with call
      Hero.call(this, name, level);

      this.spell = spell;
  }

  // Is equivalent to this with classes
  //               ==

  class Hero {
      constructor(name, level) {
          this.name = name;
          this.level = level;
      }

      // Adding a method to the constructor
      greet() {
          return `${this.name} says hello.`;
      }
  }

  // Creating a new class from the parent
  class Mage extends Hero {
      constructor(name, level, spell) {
          // Chain constructor with super
          super(name, level);

          // Add a new property
          this.spell = spell;
      }
  }
  ```

* Factory function

  ```javascript
  // Function factory
  function Car () {
    var self = {
      make: 'Honda',
      model: 'Accord',
      color: '#cc0000',
      paint: function(color){
        self.color = color;
      }
    };
    return self;
  }
  
  var myCar = Car();
  ```

*Inheritance with prototype chaining

  ```javascript
  function doSomething(){}
  doSomething.prototype.foo = "bar"; // add a property  onto the prototype
  var doSomeInstancing = new doSomething();
  doSomeInstancing.prop = "some value"; // add a property   onto the object
  console.log( doSomeInstancing );
  /*
  doSomething {prop: "some value"}
    prop: "some value"
    __proto__:
      foo: "bar"
      constructor: ƒ doSomething()
      __proto__: Object
  */
  ```
---

### call() , apply() and bind()

```javascript
var obj = {name:"Niladri"};

var greeting = function(a,b,c){
    return "welcome "+this.name+" to "+a+" "+b+" in "+c;
};

console.log(greeting.call(obj,"Newtown","KOLKATA","WB"));
// welcome Niladri to Newtown KOLKATA in WB
```

* apply

  ```javascript
  var obj = {name:"Niladri"};

  var greeting = function(a,b,c){
      return "welcome "+this.name+" to "+a+" "+b+" in "+c;
  };

  // array of arguments to the actual function
  var args = ["Newtown","KOLKATA","WB"];  
  console.log(greeting.apply(obj,args));
  // welcome Niladri to Newtown KOLKATA in WB
  ```

* bind

  ```javascript
  var obj = {name:"Niladri"};

  var greeting = function(a,b,c){
      return "welcome "+this.name+" to "+a+" "+b+" in "+c;
  };

  //creates a bound function that has same body and   parameters 
  var bound = greeting.bind(obj); 

  console.dir(bound); ///returns a function
  console.log(bound("Newtown","KOLKATA","WB"));
  //welcome Niladri to Newtown KOLKATA in WB
  ```

---

### Object.create, Object.assign

```javascript
let y={x:12};

let q = Object.create(y);
console.log(q.x);   //12
q.x=5;
console.log(q.x);   //5

let w = Object.create(y)
console.log(w.x);   // 12
```

* Object.create is not performing any copy. Instead, the original object is being used as the prototype of copied.

  ```javascript
  const original = {
    name: 'Fiesta'
  }
  const copied = Object.create(original)
  copied.name //Fiesta

  original.hasOwnProperty('name') //true
  copied.hasOwnProperty('name') //false
  ```

* `Object.assign()` performs a shallow copy of an object, not a deep clone.

  ```javascript
  const original = {
    name: 'Fiesta',
    car: {
      color: 'blue'
    }
  }
  const copied = Object.assign({}, original)

  original.name = 'Focus'
  original.car.color = 'yellow'

  copied.name //Fiesta
  copied.car.color //yellow
  ```

* [How to deep cloning of objects in Javascript](https://flaviocopes.com/how-to-clone-javascript-object/)


---

### Pure functions

* A pure function doesn’t depend on and doesn’t modify the states of variables out of its scope.

 * That means a pure function always returns the same result given same parameters. Its execution doesn’t depend on the state of the system.

 * Pure functions are a pillar of functional programming.


```javascript
var values = { a: 1 }

function impureFunction(items) {
  var b = 1

  items.a = items.a * b + 2

  return items.a
}

var c = impureFunction(values)
// Now `values.a` is 3, the impure function modifies it.
```

```javascript
var values = { a: 1 }

function pureFunction(a) {
  var b = 1

  a = a * b + 2

  return a
}

var c = pureFunction(values.a)
// `values.a` has not been modified, it's still 1
```

---

### Higher-order functions

* Functions that operate on other functions, either by taking them as arguments or by returning them, are called higher-order functions.

* Allow us to abstract over actions, not just values.

  ```javascript
  function noisy(f) {
    return (...args) => {
      console.log("calling with", args);
      let result = f(...args);
      console.log("called with", args, ", returned",  result);
      return result;
    };
  }
  noisy(Math.min)(3, 2, 1);
  // → calling with [3, 2, 1]
  // → called with [3, 2, 1] , returned 1
  ```

  ```javascript
  // Currying
  var add = a => b => a+b;
  add(5)(3);  // 8
  ```

---

### Collections and Generators

* The Map object holds key-value pairs and remembers the original insertion order of the keys. 

```javascript
new Map([iterable])
```

* weakmap

  * WeakMap is not iterable, as opposed to Map – that means there is no iterable protocol, no .entries(), no .keys(), no .values(), no .forEach() and no .clear().

  * Another “limitation” found in WeakMap as opposed to Map is that every key must be an object, and value types are not admitted as keys. 
      ```javascript
      new WeakMap([iterable])
      ```

* Set
  * The Set object lets you store unique values of any type, whether primitive values or object references.
  
      ```javascript
      new Set([iterable]);
      ```


* WeakSet
  * You can’t iterate over WeakSet. Its values must be unique object references. If nothing else is referencing a value found in a WeakSet, it’ll be subject to garbage collection.

  * Much like in WeakMap, you can only .add, .has, and .delete values from a WeakSet. And just like in Set, there’s no .get.

      ```javascript
      new WeakSet([iterable]);
      ```

---

### async/await
  * The word “async” before a function means one simple thing: a function always returns a promise.

      ```javascript
      async function f() {
        return 1;
      }

      f().then(alert); // 1
      ```

  * The keyword await makes JavaScript wait until that promise settles and returns its result.

      ```javascript
      async function f() {
        let promise = new Promise((resolve, reject) => {
          setTimeout(() => resolve("done!"), 1000)
        });

        let result = await promise; // wait till the promise resolves (*)

        alert(result); // "done!"
      }

      f();
      ```
      
  * Can’t use await in regular functions.

      ```javascript
      function f() {
        let promise = Promise.resolve(1);
        let result = await promise; // Syntax error
      }
      ```
### Function composition

  * Function composition is a mechanism of combining multiple simple functions to build a more complicated one. The result of each function is passed to the next one. In mathematics, we often write something like: `f(g(x))`.

      ```javascript
      const users = [
          { name: "Jeff", age: 14 },
          { name: "Jack", age: 18 }, 
          { name: "Milady", age: 22 },
      ];
      const filter = (cb, arr) => arr.filter(cb);
      const map = (cb, arr) => arr.map(cb);

      map(u => u.name, filter(u => u.age >= 18, users)); //["Jack", "Milady"]
      ```

---

### Garbage collection

* is performed automatically. We cannot force or prevent it.
- Reachability: It removes those which are not reachable from root

```javascript
let user = {
  name: "John"
};

let admin = user;

user = null;   //
console.log(admin); // { name: "John" }
// It is still reachable so wont delete
```

---

```javascript
let user = {
  name: "John",
  go: function() { alert(this.name) }
}

(user.go)() // Error! There must be a ; after user definition }
```
