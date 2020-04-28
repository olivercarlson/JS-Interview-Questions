# JS-Interview-Questions
A Repository that goes beyond explanations & solutions to common JS Interview Questions by explaining WHY they are asking the question.

Questions will be broken down by:
1. The Question 

2. Stop and Think Sections

3. Understanding what the question is intending to test and why they are asking this

4. A Solution

5. Further Reading  

# 1. Closures

A typical example of this question is something along the lines of "what is the output of this code?" followed by,
"how do you fix it?" 

```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, 1000);                 
}
```

### Questions to ask yourself first:
Consider the output of these two similar blocks of code:

```
for (var i = 0; i < 5; i++) {
    console.log(i);
}
```

```
for (var i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, 0);        // 0 or blank                
}
```

### What is this question intending to test?

Typically, this question is intended to test your knowledge of closures in JavaScript. 
    
It may be also be intentionally or unintentionally testing:

- If you're used to ES2015's let & const, it may be testing whether or not you remember the lexical scoping rules of var. 

- If you understand the Event Loop in JavaScript (a setTimeout of even 0 will pop us at the back of the message queue!)

- If you understand the difference between Pass By Reference and Pass By Value in function calls.
        
### Why am I being asked this?

Before ES2015 JavaScript had no block level scoping. Thus the necessity of understanding a simple closure was extremely important - this was essentially a bread and butter pattern to know.
    
A common task could be say looping over an array [1,2,3,4,5] and inserting several buttons to the DOM with an event handler and text equal to the value of 'i' in the loop. 
    
### Solution:

This will probably be considered cheating, but you can of course skip all the waffle and just use ES2015 features:

```javascript
for (let i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i)    // 0,1,2,3,4
    }, 1000)
}
```

A possible var based solution could look like this:

```javascript
function renderMessage (message) {
// renderMessage function scope, value of message is equal to whatever was passed in when the function was called.
  setTimeout(function() {
    console.log(message)
  }, 1000)
}

for (var i = 0; i < 5; i++) {
//global scope (for anything that references the value of i)
  renderMessage(i)              
}
```

A key piece of the solution here is that **each time** we call the renderMessage function, we are encapsulating each reference to i within each call of that function's lexical environment.

This lexical environment is determined (and fixed) when we call the function (and **not** when we execute it or any remaining code associated with it).

In our loop example, we're creating 5 different, unique lexical environments each of which contain a different value of i.  

It is a summation of both the lexical scoping rules of var (no block level scope) and the Event Loop triggering a new message from setTimeout that is leading us to this behavior. 

I think it is important to stop and consider additional blocks of code.

Calling a separate function **without a set timeout** either within the for loop block or outside of it:

```javascript
function immediateMessage(message) {
    console.log(message)
}

for (var i = 0; i < 5; i++) {
    immediateMessage(i)         // 0,1,2,3,4
}
```

The same output is repeated here: 

```javascript 
for (var i = 0; i < 5; i++) {
    function immediateMessage(message) {
        console.log(message)
        }
    
    immediateMessage(i)         // 0,1,2,3,4
}
```

In a sort of contrived example, we can try adding additional function calls to the current function's call stack, and return
the same result as before:

```javascript
for (var i = 0; i < 5; i++) {
  a(i)
}

function a (x) {
  b(x)
}

function b(y) {
  immediateMessage(y)
}

function immediateMessage(z) {
  console.log(z)         // 0,1,2,3,4
}
```

All three of these examples rely on a **single message in the Event Loop** generating new function calls within the current call stack and thus reference the "correct" value of i. 

To illustrate this more explicitly using the above scenario:

```javascript
// Each Message is an item in the queue of the Event Loop 



{Message 1:
for loop (i = 0): //Global scope:  i = 0;
a calls b with value i = 0;
c calls immediateMessage with value i = 0;
immediateMessage console.logs the value of i;

for loop (i = 1): // Global scope: i = 1;

..

..

..

end of Message 1}

// Note: We only have one message here.
```

However, when we add the setTimeout, we are new pushing each function call to a new message. 

Illustrated below:

```javascript
for (i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i)    
    }, 1000)
}

{Message 1:
for loop (i = 0):                           //Global scope:  i = 0;
// a new event listener is registered, when the timeout expires after at least 1000 ms has passed, a console.log will display whatever i references.

..
..
..

for loop (i = 4):             //Global scope:  i = 4;
// a new event listener is registered, when the timeout expires after at least 1000 ms has passed, a console.log will display whatever i references.

// i = 5;  // global scope
// break loop;
//end of Message 1}

{ Message 2:
console.log the value that i references (right now!)
// returns 5
// end of Message 2}

{ Message 3:
console.log the value that i references (right now!)
// returns 5
// end of Message 3}

..
..
..

```


Thus far, we have covered the importance of 1. Closures and 2. The Event Loop.   

There is one final topic that is also important to highlight, the difference between a function call by reference and by value.

The key reason our initial example failed was because in that setTimeout, we are not passing the value of i but we are instead passing a reference to the value of i (which can change).

Let's review the code again:

```javascript
// becase we are using var, this for loop brace "{" does not have it's own lexical scope.
// so essentially we are delcaring out here, in the global scope:
// var i = 0;

for (var i = 0; i < 5; i++) {  
    setTimeout(function() {     // thus the value of "i" is the same both within and outside of the loop. 
        console.log(i)         
        }, 1000)
}
```

To look back at the function call stack and event loop for this block of code WITHOUT using a function to wrap a closure around the value of i:

```javascript
in loop i = 0;

call function renderMessage(i); 

function renderMessage(i) fires and when the setTimeout for 1000ms expires, add a new message to queue in the event loop to console.log the value of i. 

..

..

..

loop i = 4;

..

// break loop, as conditional fails now that i == 5;

// end  of the current function call stack

// go to the next message in queue in the event loop
// where we passed a console.log with a reference to i.

```

It is extremely important to remember one of the reasons why this error occurred in the first place is because we were talking about what i was referencing. 

# Modern Day Examples: 

More realistically, you will be working with a frontend framework or library such as React which thankfully use a combination of Babel and polyfills to allow developers to write code with cutting edge syntax. 

Therefore, we will have variable declarations with let available. However, we will still need to be aware of closures.

Consider this standard code in React:

```javascript
in some <Parent> component: 
    ..
    
    this.state = {
        count: 1
    }
    
    ..
    
    render() {
        return (
            <Child />
        )
    }
```

Now let's try to update this:

```javascript
in some <Child> component:
    this.state = undefined; // to be explicit, we have no state here.
    
    // in some on click event handler:
    handleClick = () => event => {
         this.setState({ count: event.target.value})  // we cannot change the parent's state!
    }
```

In order to allow some Child component update our Parent state, we need a closure! (which in the case of React, we would pass down and access it via props.
    
```javascript 
in some <Parent> component: 

    this.state = {
        count: 1
    }
    
    updateCount(value) {
        this.setState({count : value})  
    }
    ..
    
    render() {
        return (
            <Child setCount={updateCount} />   
            // pass in the updateCount which has a closure around the Parent's setState 
        )
    }
```

And voila! We have utilized the power of closures to encapsulate a variable.

Now in <Child>, by the magic of closures, we can call (and access the scope of) the Parent's setState value. 
    
```javascript
handleClick = () => event => {
         this.props.setCount(event.target.value)}
```
