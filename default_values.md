# How to set default parameter values in JavaScript functions
When creating a function we often want to make sure that the values (called arguments) passed to the function when called
has default values in case the user does not provide one, or provides the wrong one.

There are many ways to set default values, but here are five common ways:

1. If statement
2. Tenary expression
3. Short-circuit evaluation
4. Object.assign
5. ECMAScript 2015 (ES2015) default parameters

When we call a function without passing a value for one of the parameters (the names used when declaring a function) JavaScript will instead pass the value <code>undefined</code> as a default value for this parameter.

The three first methods overrides the JS default value with our own default value by detecting this <code>undefined</code> value inside the function, and instead replacing it with a new value, while the last two sets their own default values initially instead of relying on the JS <code>undefined</code>.

<br>

## If statement (method 1)
The first and perhaps most intuitive way to set default values is with a simple <code>if</code> statement.

The syntax should be familiar.

```js
if (test expression)
  statement  
```

Using this we can set a default value for a variable like so:

```js
function increment(num) {
  if (!num) { num = 0 }
  return ++num
}
```
```js
increment()   // returns 1 since "num" is set to 0 before incrementation
increment(22) // returns 23
```
If no value is passed for the <code>num</code> parameter, <code>num</code> will be set to the number <code>0</code>.

If the value of <code>num</code> is set to <code>undefined</code> if no value is passed for <code>num</code>, then why do we write <code>if(!num)</code> instead of <code>if (num === undefined)</code>? The answer is that they both work, its just that <code>!num</code> is shorter and faster to write, so we prefer it for this reason. This does come at a cost though. <code>num === undefined</code> would _only_ allow the value <code>undefined</code> to enter the <code>if</code>, setting the default value only in the case the value is <code>undefined</code>, while <code>!num</code> allows any value that is seen as false (called falsy value) by JS to enter the <code>if</code> and set the default value. This makes the value <code>undefined</code> enter the if, but also  make values like <code>0</code>, <code>""</code>, <code>NaN</code> enter the if and set default value, since they are all also seen as falsy values. If we do not want these values to be replaced by a default value, we must use <code>num === undefined</code> instead.

<b>Tip:</b>
If you want to know which values are seen as false (read: converted too) by JS, you can do so with either <code>!!value</code> or <code>Boolean(value)</code>.

```js
Boolean(20)         // true  
!!20                // true

Boolean(0)          // false
!!0                 // false
```


A last tip is to make our code a tad bit less verbose by skipping the brackets.

```js
function increment(num) {
  if (!num) num = 0
  return ++num
}
```

This works because brackets are only necessary when we have several statements inside the <code>if</code> statement. Brackets denote a block statement - a statement used to group other statements.


<!-- **Pros**
- If test expression is true can skip re-assigning the passed value
More efficient because we dont need to reassign the argument value to itself if
it is true. We assign ONLY if no values/valid value is passed. not always decide to do it.
-->


<br>

## Tenary expression (method 2)
The second method, a tenary expression, can be used to set default values the same way as an <code>if</code> statement.

The syntax of a tenary expression looks like this:  

```js
test expression ? value expression : value expression  
```

If the test expression evaluates to true, the lefthand side of the semicolon is returned,
else the righthand side value is returned.

The <code>if</code> statement example we used above can be re-written as a tenary expression instead.

```js
function increment(num) {
  num = num ? num : 0
  return ++num
}
```

If we pass a value for <code>num</code> (a value not seen as falsy), <code>num</code> will be assigned itself, else <code>num</code> will be assigned <code>0</code>.

In this way, the left side of <code>:</code> equals the <code>if</code> statement and the right side equals the <code>else</code> statement in an <code>if else</code> statement.

To better exemplify this relationship: this tenary expression and if statement is identical in behaviour.

```js
num = num ? num : 0
```

```js
if (num) {
  num = num
}
else {
  num = 0
}
```

Tenary expressions do not create less code than an <code>if</code> statement. However, when used as a replacement for an <code>if else</code> statement, it creates shorter syntax, and also fits a single line better.

The bottom line is that an <code>if</code> statement can achieve everything a tenary expression can, and the choice between the two mostly comes down to which feels more eloquent
in the given situation and the preference of the programmer. For this reason, you _will_ encounter tenary expressions used, so its necessary to know them even if you don't use them personally.

<br>


## Short-circuit evaluation (method 3)
A third method for assigning default values to parameters is called short-circuit evaluation (or expression).
Short-circuit expressions make use of the <code>||</code> operator
and how it is seen and evaluated by JavaScript.

The syntax for such an expression is simple and looks like this:

```js
value expression || value expression
```

To use <code>||</code> to set default values we can do as in the following code snippet.

```js
function increment(num) {
  num = num || 0
  return ++num
}
```

Just like in the previous examples, this assigns the default value <code>0</code> to <code>num</code> if <code>num</code> is undefined (or any value seen as falsy).

### How it works
First, JS converts the value on the lefthand side to a boolean. If the result of this conversion is the boolean <code>true</code> JS returns the _value_ on the _left_. If, however, the result of this conversion is the Boolean <code>false</code>, it returns the value on the righthand side instead.

From this we gather two important points:
1. The expression does not return a _bool_. It returns the _value_ on either the left or right side. The boolean is only used for evaluation purposes.
2. The result is always just _one_ of the values of either side   

The name short-circuit comes from the second point. If the lefthand side converts to <code>true</code> the expression "short-circuits", meaning it never goes to the right side.

<b>Note:</b>
Using the && operator also works for selectively returning one of the sides. However, it does not work for assigning default values. This is because
the righthand side (the default) value would be applied when the argument is true, not false, meaning everytime the argument is actually passed, not when it's _not_ passed.


### A flaw
It's tempting to use short-circuit evaluation due to its very short and easy syntax. Unfortunately, though, the reason
for this short syntax, is also the cause of a problem. In a short-circuit expression <b>the test and return value is intrinsically
tied together.</b>

By contrast, in a tenary expression, we can see that these are separate, and that the value that the test expression evaluates to, does not affect the value returned.

```js
both test expression and returned value || returned value
```

```js
test expression ? returned value : returned value  
```

The consequence of this tying together is that we cannot change the lefthand side from e.g. <code>arg ||</code> to <code>arg === "number" ||</code>, since this would return a boolean if true instead of the value stored in <code>arg</code>. In other words, the argument test and the returned value needs to be the same.

So in short. Short-circuit expressions are only useful when we want to assign the argument itself or a default value. Not, for instance, when we want to check if the value is a _specific_ value (<code>arg === undefined</code>) or of a specific datatype (<code>typeof arg === "number"</code>), before choosing to assign a default value. In that case we need to use another method, like so: <code>typeof arg === "number" ? arg : 0</code>.

<!-- - What happens is implicit, which makes its hard to understand (especially when using <code>!</code> etc) -->

<br>

<!-- SS can be hard to understand and wrap your head around. Especially when you start mixing in the use of the ! operator. Why? -->


## Object.assign (method 4)
<code>Object.assign</code> is a function that exists in many environments (e.g. chrome, firefox, node.js).

It is called statically like this:
```js
Object.assign(targetObject, sourceObject1, sourceObject2, etc..)
```

It assigns the properties of one or many objects (source objects), to a target object.


### How it works
What rules does the function follow when assigning to the target object?

In short what happens is that:
- If the property exists in the target object and source object, it replaces the value of the target object property.
- If the property exists in the source object only, it creates/adds it, with the value, to the target. (if dont ref dont matter)
- If the property does not exists in source, we dont do anything.

The following code illustrates all of these rules in effect:

```js
  // create the object we want to assigm TO
  var target = {name: "Ben", age: 29}
  // create the object we want to assign FROM
  var source = {name: "Hans", lastname: "Gruber"}
  // assign the properties of source to target
  Object.assign(target, source)

  // This changes the target object from
  // {
  //   name: "Ben",
  //   age: 29
  // }
  //
  // to:   
  // {
  //   name: "Hans",           // same key, but new value
  //   age: 29,                // same key and value
  //   lastname: "Gruber"      // a new property entirely
  // }
```

As you might have noticed, if the user provides an (source/config) object with properties not present in the <code>target</code> object those properties are added to the defualt object as well, however, this does not cause an issue, since any unnecessary properties added is not referenced anywhere in the function, causing them to just be treated like they dont exists.


### Setting default values with Object.assign
A common use-case for <code>assign</code> is when we want to set default configuration. If a function
executes a process and we want to customize how this process is run, we create a default object in
the function which controls this process and then we override any values of the default config object with the user provided object.

We can illustrate the use of Object.assign to set default values by creating a tooltip that is displayed a certain way, but lets the user (function caller) have the final say in how it's displayed.

```js
function showTooltip(user_config) {
  // create values that will be used by default if no values are passed
  var default_config = {
    direction: "topleft",  
    backgroundColor: "#000"
  }

  // override some of the values in the defualt_config object
  // with user preferences (user_config)
  Object.assign(default_config, user_config)

  // pass the final config and display the tooltip
  displayTooltip(default_config)
}

// shows a tooltip, using the default direction ("topleft"),
// but user preferred background color ("#ddd")
showTooltip({backgroundColor: "#ddd"})

```

The difference with this method compared to the others, is that we need to predefine default values from the get go,
and then we _replace_ the values if need be. In the other methods we only applied the new values _if_ the user
did not provide a value, or a wrong value. Using this method we _assume_ values will not be passed, in the others we _assumed_ they will.


### Your own version
Using <code>Object.assign</code> depends on whether it exists in
the environment that you are using (chrome, node etc), and the environments version (10, 11 etc).

If <code>Object.assign</code> is not available you can make your own:

```js
function assign(target, varArgs) {
  var obj = Object(target)

  for (var index = 1; index < arguments.length; index++) {
    var nextSource = arguments[index];

    if (nextSource !== null && nextSource !== undefined) {
      for (var nextKey in nextSource) {
        if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
         obj[nextKey] = nextSource[nextKey];
        }
      }
    }
  }

  return obj
}
```


This is a simplified version of <code>Object.assign</code> that does not emulate its exact behavior, but does the trick most of the time. If you want a more elaborate version that behaves identically to <code>Object.assign</code>, you can copy the function found <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign#polyfill">here</a> into your own code.

<b>Note:</b> To create a function with the exact same behaviour as <code>Object.assign</code> (called a polyfill) it's necessary to use another function called  <code>Object.defineProperty</code>.
This function does not exists in some older browsers (and cannot be polyfilled itself), this means that, even though the linked function is superior to the simplified function in that it has identical behaviour to <code>Object.assign</code>, it works in less browsers than the aforementioned. This is not really a big deal though since the support is still pretty good, and the simplified version does not work in all version either, even though it has better support.

<br>




## ECMAScript 2015 (ES2015) default parameters (method 5)
Since the JavaScript version released in 2015 another method for setting default parameters values have existed simply named "default parameters".

The previous methods set default values _inside_ the function. They let JavaScript pass <code>undefined</code> as the default value to the function, and then we used an if statement, tenary or short-circuit expression to override the <code>undefined</code> value to whatever we wanted instead.

With default parameters, however, we decide what value will be passed instead of <code>undefined</code>, so we dont need to override the value
inside the function.

The syntax for setting default parameter values looks like this:

```js
function fn(param1 = value expression, param2 = value expression, etc...) {
  // ... code
}
```

Using this syntax we can set default values for parameters like this:

```js
function add(a = 0, b = 10 - 10) {
  return a + b
}
```

If nothing is passed for either <code>a</code> or <code>b</code> (or the value passed is <code>undefined</code>), both <code>a</code> and <code>b</code> will be set to the number <code>0</code> (b = 10 - 10 = 0), resulting in <code>return 0 + 0</code>.

Default values can also be stored in variables:

```js
  var age = 20
  function increment(num = age) {
    return ++num
  }
  increment() // returns the number 20
```

Using this more modern method is a very easy way to set default values. However, it isn't that commonly encountered; due to a combination of less browser support, slow adoptation by developers and the fact that much of the JavaScript code you encounter is already transpiled by babel (transformed into an older JavaScript version for better browser support) before you read it.


<br>



## Conclusion
We have explored five ways to set default function values if the function caller does not provide, or provides the wrong, value.

Each method has its own strengths and weaknesses. For instance, using an if statement would offer the best readability, but the short-circuit method would produce the shortest syntax - tempting for when you set default values often, but insufficient if you need to explicitly check what the passed value is before choosing to assign a default value.

In the end though, which one you choose is mostly down to preference, since each option will do the job in most scenarios.
