quick_check.js
==============

quick_check.js is an implementation of QuickCheck in JavaScript. (Actually the
implementation is written in CoffeeScript, but that doesn't matter much).

Currently it exists as a Jasmine plugin. Expectations are written like this:

~~~javascript
// implementation
function odd(n) {
  return n % 2 === 1;
}
// tests
it('#odd returns true for odd numbers', function() {
  expect(function(i) {
    return odd(2 * i + 1);
  }).forAll(qc.int)
});
~~~

Notice the `forAll(qc.int)`. This indicates that our function (called property)
should return true for all integers passed to it. This code in fact contains a bug
which quick_check.js will helpfully find for you:

~~~
PhantomJS 1.9.7 (Mac OS X) #odd returns true for odd numbers FAILED
  Falsified after 3 attempts. Counter-example: -4
~~~

This means that quick_check.js generated 3 random integers and one of them failed
the test (in this case -4). (Why? Because modulus operator in JavaScript is botched).

QuickCheck will stop after 100 generated test cases and assume that your code works.

For more information [check out my talk](https://vimeo.com/98737599) or read the
[annotated source code](http://code.gampleman.eu/quick_check.js/).

# Generators

quick_check.js comes with batteries included. There are plenty of generators included
plus it is very easy to write your own. I recommend checking out the source, but here is a quick rundown:

### basic
qc.bool will randomly return `true` or `false`.

qc.byte will return a random integer from 0 to 255.

qc.constructor(constructorFn, generators...) will generate random objects by calling the constructor randomly.

qc.fromFunction(fn, generators...) will generate random values by calling a function with random args,

### numbers
All numbers listed here have a large variant that generates larger numbers (eg. qc.int -> qc.int.large).

qc.ureal return a random positive real.

qc.real return a random real.

qc.uint return a random positive integer.

qc.int will return a random integer.

qc.int.between(min, max) generates a random number between min and max.

### strings

qc.char will return a random string with a single chararcter.

qc.string will generate a string of random charachters.

qc.string.ascii will generate a string of random ascii charachters.

qc.string.concat(generators) will concat random strings generated by the array of generators passed in.

qc.string.matching(regexp) will generate a string that matches the regular expression regexp. Negative lookaheads and control sequences (e.g. `\c08`) aren't currently supported, but otherwise this is a very powerful way how to generate random strings.

### arrays

qc.array will generate a random array of any type.

qc.arrayOf(generator) will generate an array of type from that generator.

### object

qc.object generates an object containing random types.

qc.objectOf(type) generates an object containing the passed type.

qc.objectLike(templateObject) accepts a template of an object with random generators as values, and returns a generator of that form of object.

~~~javascript
qc.objectLike({
  hello: "world",
  name: qc.string.matching(/^m(r|s)\. [A-Z][a-z]{3,9}$/)
}) // generates:

{
  hello: "world",
  name: "mr. Dasde"
}
~~~

### combinators

These generators combine the function of other generators to make it easy to create powerful generators:

qc.pick(a, b, c, ...) will return a function that randomly chooses one of its arguments.

qc.choose(a, b, c, ...) will randomly choose one of its arguments. If only one argument is passed,
it is assumed that that argument is an array of possibilities.

qc.oneOf(a, b, c, ...) combines generators into one generator by picking a random one between them.

qc.except(generator, values...) generates a value but not any of the passed values. This is quite a naive implementation as it will simply try again if the generator does generate one of the values. If the probability of generating one of these values is high, this can really kill performace, so for those cases a custom implementation might be better (e.g. the string generator does this).

### functions

qc.function(argGenerator..., returnValueGenerator) will generate a random pure function that accepts the argGenerator types and returns a value of returnValueGenerator type.

### various

qc.any generates a random value of any of the basic types (except functions).

qc.date generates a random date.

# License

The MIT License (MIT)

Copyright (c) 2014 RightScale

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
