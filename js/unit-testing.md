# Unit Testing (Mocha & Chai)

When you test your codebase, you take a piece of code — typically a function — and verify it behaves correctly in a specific situation. Unit testing is a structured and automated way of doing this. 

As a result, the more tests you write, the bigger the benefit you receive. You will also have a greater level of confidence in your codebase as you continue to develop it.

```
npm install mocha chai --save-dev
```

NB: always use save-dev, no need for the stuff in production.

Include it in the project

```javascript
var chai = require('chai');
```

Every test follows the same basic pattern. First you have the `describe` block.

```javascript
describe('Array', function() {
  // Code goes here
});
```

Describe is used to group the tests. Fx. A single Components test.

Secondly, inside the describe, we’ll have `it` blocks:

```javascript
describe('Array', function() {
  it('should start empty', function() {
    // Test implementation goes here
  });

  // We can have more its here
});
```

`It` is the actual test. The item were testing. Here we define that the `Array` in describe part should start empty. And we then provide a readable description to it.

## The test

Since we are testing that an array should start empty, we need to create an array and then ensure it’s empty. 

```javascript
var assert = chai.assert;

describe('Array', function() {
  it('should start empty', function() {
    var arr = [];

    assert.equal(arr.length, 0);
  });
});
```

`assert` is the Chai instance. In this case, it's simply a shorthand for chai.assert.

In the `it` function, we create an array and check its length.

```javascript
assert.equal(arr.length, 0);
```

Here's basic cheatsheet.

https://gist.github.com/mikejakobsen/2fa2a792fd7627fedcc2a80975f6d187#file-mocha-js
