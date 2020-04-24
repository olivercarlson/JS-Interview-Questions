# JS-Interview-Questions
A Repository that goes beyond explanations & solutions to common JS Interview Questions by explaining WHY they are asking the question.

# 1. Closures

A typical example of this question is something along the lines of "what is the output of this code?" possibly followed by,
"how do you fix it?" 

###
for (var i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, 1000);
}
###
