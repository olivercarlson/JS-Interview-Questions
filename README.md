# JS-Interview-Questions
A Repository that goes beyond explanations & solutions to common JS Interview Questions by explaining WHY they are asking the question.

Questions will be broken down by 1. The Question 2. Modifications to Stop and Think About, 3. What the question is intending to test / why they are asking this, 4. A Solution, and finally 5. Further Reading  

# 1. Closures

A typical example of this question is something along the lines of "what is the output of this code?" possibly followed by,
"how do you fix it?" 

```
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

This question is intended to test your knowledge of closures in JavaScript. 
    
It may be unintentionally testing:

- If you're used to ES2015's let & const, it may be testing whether or not you remember the lexical scoping rules of var. 

- If you understand the Event Loop in JavaScript (a setTimeout of even 0 will pop us at the back of the message queue!)

- If you understand Pass By Reference and Pass By Value in function calls.
        
### Why am I being asked this?

It's important to remember that before ES2015 JavaScript had no block level scoping. Thus the necessity of understanding a simple closure was extremely important. 
    
A common task could be say looping over an array [1,2,3,4,5] and inserting several buttons to the DOM with an event handler and text equal to 'i' in the loop. 
    
### Solution:

This will probably be considered cheating, but you can of course skip all the waffle and just use ES2015 features:

```
for (let i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i)   
    }, 1000)
}
```

A possible var based solution could look like this:

```
// global scope 
function renderMessage (message) {
    // renderMessage function scope
  
  setTimeout(function() {
    console.log(message)
  }, 1000)
}

// global scope
for (var i = 0; i < 5; i++) {
  renderMessage(i)              // the key here is to to wrap each individual value of i within each function call's protective scope ( aka create a closure ).
}
```

In order to understand why this occurs, we need to look a bit deeper as to what's going on.

If we take a look at the function call stack for our code:

```
in loop i = 0;

call function renderMessage(i = 0);

function renderMessage(i = 0) fires and when the setTimeout for 1000ms expires, add a new message to queue in the event loop to console.log the value of i.

..

..

..

loop i = 4;

call function renderMessage(i = 4);

function renderMessage(i = 4) fires a timer (to be handled elsewhere) is set for 1000ms and when it expires, add a new message to queue in the event loop to console.log the value of i.

// break loop, i == 5;

// end of the current function call stack.

// we can now move on to the next message in queue at the event loop, of which there are 5, one for each console.log

console.log(i = 0);

..

..

..

console.log(i = 4);
```

Thus far, we have covered the importance of 1. Closures and 2. The Event Loop.   

It's important to note that, there is one final toppic that is important to recognize, the difference between a function call by reference and by value.

The key reason our initial example failed was because in that setTimeout, we are not passing the value of i but we are instead passing a reference to the value of i (which can change).

Let's review the code again:

```
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

```
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

It is extremely important to remember one of the reasons why this error occurred in the first place is because we were talking about what i was referencing. 

# Modern Day Examples: 

Closures:

More realistically, you will be working with a frontend framework / library such as React which thankfully use Babel and polyfills to allow developers to write cutting edge syntax. Therefore, we will have variable declarations with let available. However, we will still need to be aware of closures.

Consider this standard code in React:

```
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

```
in some <Child> component:
    this.state = undefined; // to be explicit, we have no state here.
    
    // in some on click event handler:
    handleClick = () => event => {
         this.setState({ count: event.target.value})  // we cannot change the parent's state!
    }
```

In order to allow some <Child> component update our <Parent> state, we need a closure! (which in the case of React, we would pass down and access it via props.
    
```
in some <Parent> component: 
    ..
    
    this.state = {
        count: 1
    }
    
    updateCount(value) {
        this.setState({count : value})  
    }
    ..
    
    render() {
        return (
            <Child setCount={updateCount} />   // pass in the updateCount which has a closure around the Parent's setState 
        )
    }
```

Now in <Child>, by the magic of closures, we can call (and access the scope of) the Parent's setState value. 

```
  handleClick = () => event => {
         this.props.setCount(event.target.value)
    }

```

