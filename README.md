# Pipeline
Pipeline is a class that allows you handle the chain of functions calls

##  How to include Pipeline into your project
Include the follow code into your JS file:
```javascript
function pipeline() {
  var prototype = this;
  prototype.pipe = [];
  prototype.add = function( fun, param ){
    prototype.pipe.push({ fun: fun, param: param });
    return prototype;
  };
  prototype.run = function( param ) {
    var next, result = false;
    if ( next = prototype.pipe.splice(0, 1)[0] ) {
      result = next.fun( param || next.param, prototype.run );
    } else {
      result = true;
    }
    return result;
  };
}
```

## How to use Pipeline
To create a new Pipeline you have to create an instance of Pipeline.
```javascript
var pipeline = new Pipeline();
```
Then add the function names that you want to call in the chain with `.add(functionName)`. You can pass a predefined argument to the function as a second parameter of add function.

```javascript
pipeline.add(functionName, parameter);
```

To finish, just run the chain of functions with the `.run()` function.

```javascript
pipeline.run();
```

Into your function you will recibe two parameters from the execution of your function.

```javascript
function functionName( value, next ) {
  console.log(value);
  var a = 1 + 1;
  next(a);
}
```

The first parameter is the value passed from the `.add(functionName, param)` function, or can be a value passed from the before function executed via `next(param)`.

The last function in the chain will return a `true` value. So, it can be used as chain of responsibility to validate values.

### Full example

```javascript
var pipeline = new Pipeline();

pipeline.add(functionOne, 1);
pipeline.add(functionTwo, {value: 2});

pipeline.run();

function functionOne( number, next ) {
  number ++;
  if (number !== 2) {
    next({value: number}});
  } else {
    next();
  }
}

function functionTwo( obj, next ) {
  console.log(obj);
  next();
}
```

### Used as validator

```javascript
var user = {
  email: "email@example.com",
  password: "somePassword"
};
var pipeline = new Pipeline();

pipeline.add(validateEmail, user.email);
pipeline.add(validatePassword, user.password);

var result = pipeline.run();

if (result) {
  console.log("The fields of user are filled");
}

function validateEmail( email, next ) {
  if (email) {
    return next();
  } else {
    console.log("The user email is required");
    return false;
  }
}

function validateEmail( password, next ) {
  if (password) {
    return next();
  } else {
    console.log("The user password is required");
    return false;
  }
}
```
