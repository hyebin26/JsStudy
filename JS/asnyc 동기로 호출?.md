참고: https://stackoverflow.com/questions/14220321/how-to-return-the-response-from-an-asynchronous-call
```jsx
function onComplete(a){ // When the code completes, do this
    alert(a);
}

function getFive(whenDone){
    var a;
    setTimeout(function(){
         a=5;
         whenDone(a);
    },10);
}
```

This is called CPS. Basically, we're passing getFive an action to perform when it completes, we're telling our code how to react when an event completes (like our AJAX call, or in this case the timeout).

Usage would be:
```jsx
function foo(hello) {
  let test = "100000";
  console.log("foo run...");
  test = hello;
  console.log(test); // 20
  return test;
}
function promise(callback) {
  let nums = 10;
  setTimeout(function () {
    nums = 20;
    callback(nums);
  }, 2000);
  return nums;
}
const he = promise(foo); // 10
console.log(he, "he");

```
