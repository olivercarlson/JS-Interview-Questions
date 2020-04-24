# JS-Interview-Questions
A Repository that goes beyond explanations & solutions to common JS Interview Questions by explaining WHY they are asking the question.

Questions will be broken down by 1. The question 2. Questions to ask yourself first, 3. What the question is intending to test / why they are asking this and 5. how to solve this. 

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
        - Whether or not you remember the lexical scoping rules of var, if you're used to ES2015's let & const, this is not    fun.
        
        - If you understand the Event Loop in JavaScript (a setTimeout of even 0 will pop us at the back of the message queue!)
        
        - If you understand Pass By Reference and Pass By Value in function calls.
        
### Why am I being asked this?
    It's important to remember that before ES2015 JavaScript had no block level scoping. Thus the necessity of understanding a simple closure was extremely important. 
    
    A common task could be say looping over an array [1,2,3,4,5] and inserting several buttons to the DOM with an event handler and text equal to 'i' in the loop. 
    
### Solution:

A possible solution could look like this:

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
  renderMessage(i)           // the key here is to to wrap each individual value of i within a function's scope      
}
```

If we take a look at the function call stack for our code:

loop i = 0;

call function renderMessage(i = 0);

function renderMessage(i = 0) fires and when the setTimeout for 1000ms expires, add a new message to queue in the event loop to console.log the value of i.

loop i = 1;

call function renderMessage(i = 1);

function renderMessage(i = 1) fires and when the setTimeout for 1000ms expires, add a new message to queue in the event loop to console.log the value of i.

..

..

loop i = 4;

// break loop, i == 5;

// end of the current function call stack.

// we can now move on to the next message in queue at the event loop, of which there are 5, one for each console.log

console.log(i = 0);

console.log(i = 1);

..

..

..





    
