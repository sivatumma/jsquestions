## JS Questions
Javascript Interview Questions

## What will be the output of this code

```
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  setTimeout(function() {
    console.log('Index: ' + i + ', element: ' + arr[i]);
  }, 3000);
}
```
## What will be the output of this code
```
for (var i = 0; i < 3; i++) {
  setTimeout(function() { alert(i); }, 1000 + i);
}
```
## Write a function that would allow you to do this
```
var addSix = createBase(6);
addSix(10); // returns 16
addSix(21); // returns 27
```
## How would you use a closure to create a private counter?

## Explain event delegation 
#### (Instead of binding a handler to each of a similar `<li>` in an app, what is the better way)
```
document.addEventListener('DOMContentLoaded', function() {
  
  let app = document.getElementById('todo-app');
  
  // attach event listener to whole container
  app.addEventListener('click', function(e) {
    if (e.target && e.target.nodeName === 'LI') {
      let item = e.target;
      alert('you clicked on item: ' + item.innerHTML);
    }
  });
  
});
```
  
