# basic-js
Read different article of js and combine them in this repo
#

# Contents
- [Primitive](#primitive-in-js)
- [Prototype](#prototype)
- [Polyfilling](#polyfilling)
- [Transpiling](#transpiling)
- [Non-JavaScript](#non\-javascript)

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
