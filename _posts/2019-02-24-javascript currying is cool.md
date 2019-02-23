---
layout: post
title:  "JavaScript currying is cool"
date:   2019-02-23 17:06:34 +0800
categories: javascript code
---

Today I come across a question from Stackoverflow which caught my attention. It is described like the following:

_write a function that output_:

```javascript
currying('1') //cool
currying()('1') //coool
currying()()('1') //cooool
currying()()()('1') //cooool
```

That is when call the function `currying` with a parameter `'1'`, it outputs `cool`, if we have a precedent call without paramter it adds one letter of `o`, if we have two precedent call without paramter it adds two letters of `o`. 

Before we approaching this question, we need understand what's curry in JavaScript.
Curry in JavaScript means appling the one paramter at a time to a function, that means when one function accept multiple paramters, instead of take all paramter at onece by pass all paramters to the function, we call the function multiple times, each time we pass one paramter to the function. It is illustrated as the following:

```javascript
function add(a, b) {
    return a + b;
}
add(1, 2) //3

function add(a) {
    function bar(b) {
        return a + b;
    }
    return bar;
}
add(1)(2) //3
```

the first `add` function is a normal function accept two paramter, the second `add` function we converted it to be curry style. You may ask why the bother to convert to curry style. See the following example for one secenario where we will realize the benefit of currying

```javascript
function add(a) {
    function bar(b) {
        return a + b;
    }
    return bar;
}
var adder8 = add(8);
adder8(10); //18
adder8(15); //23
adder8(20); //28
```
As you could see we resued the left operand 8 multiple times to calculate the sum. this save us multiple times of passing the first paramter to the function.

The example above can support only two consecutive funciton calls, let say what if we would like to call it as many times as we want. How are we supposed to implemnt this curry function `add`.

support 3 times call
```javascript
function add(a) {
    function bar(b) {
        function foo(c) {
            return a + b + c;
        }
        return foo;
    }
    return bar;
}
```
support 4 times call
```javascript
function add(a) {
    function bar(b) {
        function foo(c) {
            function pony(d) {
                return a + b + c + d;
            }
            return pony;
        }
        return foo;
    }
    return bar;
}
```

support 5 times call
Wait, we can not continue embed on function inside another function. this is not easy to handle says 100 times curry call in which case you have to nestly embed 100 functions. Are there other ways to achieve that?
```javascript
function add(a) {
    function adder(b) {
        a += b;
        return adder;
    }
    return adder;
}
add(1)(2)(3)(4)
```
This implementation sounds good, but look closely, we will find that it does not return the final summation. because we always return adder function after each call. So we need a way to terminate the currying call. Let's refactor the code
```javascript
function add(a) {
    function adder(b) {
        if(b === "end") {
            return a;
        } else {
            a += b;
        return adder;
        }
    }
    return adder;
}
add(1)(2)(3)(4)("end");
```
That is what we want. With the last call sending "end" as parameter we end the currying and return the result of summation

Now let's look back the question we are given at the begining. We could apply the similar technique to accomplish the currying
```javascript
function currying(c) {
    var a = "coo";
    function cool(b) {
        if(b === "1") {
            return a + "l";
        } else {
            a += "o";
            return cool;
        }
    }
    if(c === '1') {
      return a + "l"
    }
    a += "o";
    return cool;
}
console.log(currying()()("1"));//cooool
```


