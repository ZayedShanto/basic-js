# basic-js
Read different article of js and combine them in this repo
#

# Contents
- [Primitive](#primitive-in-js)
- [Prototype](#prototype)
- [Polyfilling](#polyfilling)
- [Transpiling](#transpiling)
- [Non-JavaScript](#non\-javascript)
- [Cheating Lexical](#cheating-lexical)
- [Eval](#eval)
- [document.all](#documentall)

#
#### Primitive in JS
A primitive is data that is not an object and has no methods. It cannot be changed.

#### Prototype
Almost every thing in JavaScript is an object, and every JavaScript object has a prototype. Therefore, the prototype itself is also an object.

#### Polyfilling
The word "polyfill" is used to refer to taking the definition of a newer feature and producing a piece of code that's equivalent to the behavior, but is able to run in older JS environments.

```js
if (!Number.isNaN) {
  Number.isNaN = function isNaN(x) {
    return x !== x;
  };
}
```

#### Transpiling
There's no way to polyfill new syntax that has been added to the language. The new syntax would throw an error in the old JS engine as unrecognized/invalid.

So the better option is to use a tool that converts our newer code into older code equivalents. This process is commonly called "transpiling," a term for transforming + compiling.

Essentially, our source code is authored in the new syntax form, but what we deploy to the browser is the transpiled code in old syntax form. We typically insert the transpiler into our build process, similar to our code linter or our minifier.

**Example:** 
- Bable
- Traceur

Here's a quick example of transpiling. ES6 adds a feature called "default parameter values." It looks like this:
```js
function foo(a = 2) {
  console.log( a );
}

foo();		// 2
foo( 42 );	// 42
```

It's new syntax that's invalid in pre-ES6 engines. Here is the transpiler to make it run in older environments

```js
function foo() {
  var a = arguments[0] !== (void 0) ? arguments[0] : 2;
  console.log( a );
}
```

#### Non-JavaScript
So far, the only things we've covered are in the JS language itself. The reality is that most JS is written to run in and interact with environments like browsers. A good chunk of the stuff that we write in your code is, strictly speaking, not directly controlled by JavaScript. That probably sounds a little strange.

The most common non-JavaScript JavaScript we'll encounter is the DOM API. For example:
```js
var el = document.getElementById( "foo" );
```
The document variable exists as a global variable when our code is running in a browser. It's not provided by the JS engine, nor is it particularly controlled by the JavaScript specification. It takes the form of something that looks an awful lot like a normal JS object, but it's not really exactly that. It's a special object, often called a "host object."

#### Cheating Lexical
JavaScript has two such mechanisms ([eval](#eval), [with](#cheating-lexical)). The former can modify existing lexical scope (at runtime) by evaluating a string of "code" which has one or more declarations in it. The latter essentially creates a whole new lexical scope (again, at runtime) by treating an object reference as a "scope" and that object's properties as scoped identifiers. 

The downside to these mechanisms is that it defeats the Engine's ability to perform compile-time optimizations regarding scope look-up, because the Engine has to assume pessimistically that such optimizations will be invalid. Code will run slower as a result of using either feature.

Both of them are equally frowned-upon in the wider community as bad practices to use in your code. But the typical arguments against them are often missing the most important point: **cheating lexical scope leads to poorer performance.**

#### Eval
The eval(..) function in JavaScript takes a string as an argument, and treats the contents of the string as if it had actually been authored code at that point in the program. In other words, we can programmatically generate code inside of our authored code, and run the generated code as if it had been there at author time.
```js
function foo(str, a) {
  eval( str ); // cheating!
  console.log( a, b );
}

var b = 2;

foo( "var b = 3;", 1 ); // 1 3
```
In fact, as mentioned above, this code actually creates variable b inside of foo(..) that shadows the b that was declared in the outer (global) scope.

**Note:** eval(..) when used in a strict-mode program operates in its own lexical scope, which means declarations made inside of the eval() do not actually modify the enclosing scope.

#### document.all

In DOM, we can see there is an `HTMLCollection` called `document.all`. Though it looks like array but it is not array. `typeof document.all` is `undefined`. But still we cant iterate it as it has a function `Symbol.iterator` in it's `__proto__`. But here main question is why it is falsy value though it has value. Because to detect `Old Browser`. `document.all` was used in old browser. But in modern browsers, the same purpose is served using `document.getElementByID`. Though modern browsers use `document.getElementById`, there is still support for `document.all` in many modern browsers. That's why if we cannot detect old browser by checking that property. That's why it is made `falsy` value so that it can be easily understood the browsers version. Example in code to solve this in different way:

```
if (document.all) {
  // code that uses `document.all`, for ancient browsers
} else if (document.getElementById) {
  // code that uses `document.getElementById`, for “modern” browsers
}
```
Basically, for a long time `document.all` was used in this way to detect old browsers. Because `document.all` is tested first though, more modern browsers that offer both properties, would still end up in the `document.all` code path. In modern browsers, we’d prefer to use `document.getElementById`, of course, but since most browsers still have `document.all` (for other backwards compatibility reasons) the else would never be accessed if `document.all` was truthy. Had the code been written differently, this wouldn’t be a problem:

```
if (document.getElementById) {
  // code that uses `document.getElementById`, for “modern” browsers
} else if (document.all) {
  // code that uses `document.all`, for ancient browsers
}
```

But sadly, a lot of existing code does it the other way around.

The simplest fix for this problem is to simply make `document.all` be falsy in browsers that still mimic it.
