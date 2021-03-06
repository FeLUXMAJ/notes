# AJAX

This is all done via vanilla Javascript, but it can be done via libraries such as JQuery, Axios, Superagent, Fetch API, Node HTTP...  

```javascript
function loadData(){
    var xhr = new XMLHttpRequest();

    xhr.open("GET", "http://www.api.com/data", true);

    xhr.onload = function(){
        if (this.status == 200) {
            return JSON.parse(this.responseText);
        }
    }

    xhr.onerror = function(){
        console.log("error");
    }

    xhr.send();
}
```

**Regular HTTP request**  
`Client` --- Request ---> `Server`  
`Client` <--- Response --- `Server`  

**Ajax**  
`Client` --- JS Call ---> `Ajax Engine` --- XMLHttpRequest (XHR) ---> `Server`  
`Client` <--- HTML --- `Ajax Engine` <--- JSON --- `Server`  

## XMLHttpRequest (XHR) Object
This API in the form of an object is provided by the browser's Javascript environment, and it has its own properties and methods which transfer data between the browser and a server.

```html
<button id="button">Get data</button>
```
```javascript
document.getElementById("button").addEventListener("click", loadData);
```

### Old way

We have to make sure we are on the `readyState 4`.  
```javascript
function loadData(){
    // Create XHR Object
    var xhr = new XMLHttpRequest(); // Can be named req

    // Establish server connection - type, url/file, async
    xhr.open("GET", "http://www.api.com/data", true); // readyState 1

    xhr.onreadystatechange = function(){
        // readyState 1, 2, 3, 4
        if (this.readyState == 4 && this.status == 200) {
            return JSON.parse(this.responseText);
        }
    }

    // If it fails
    xhr.onerror = function(){
        console.log("error");
    }

    // Send request
    xhr.send();
}
```
**readyState values**  
```
0. Request not initialized.  
1. Server connection established.  
2. Request received.  
3. Processing request.  
4. Request finished and response ready.  
```

### New way
The difference from the old way is that `onload` will not run unless we are on `readyState 4`
```javascript
function loadData(){
    // Create XHR Object
    var xhr = new XMLHttpRequest(); // Can be named req

    // Establish server connection - type, url/file, async
    xhr.open("GET", "http://www.api.com/data", true); // readyState 1

    xhr.onload = function(){
        // readyState 1, 4
        if (this.status == 200) {
            return JSON.parse(this.responseText);
        }
    }

    // If it fails
    xhr.onerror = function(){
        console.log("error");
    }

    // Send request
    xhr.send();
}
```

### Loading
```javascript
xhr.onprogress = function(){
    // readyState 3
    // Show loading...
}
```

### POST
```html
<form method="POST" id="form">
    <input type="text" id="input">
    <input type="submit" value="submit">
</form>
```
```javascript
document.getElementById("form").addEventListener("submit", submitForm);

function submitForm(e){
    e.preventDefault();

    var input = document.getElementById("input").value;
    var params = "input=" + input;

    var xhr = new XMLHttpRequest();

    xhr.open("POST", "http://www.api.com/data", true);

    xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");

    xhr.onload = function(){
        // readyState 1, 4
        if (this.status == 200) {
            return JSON.parse(this.responseText);
        }
    }

    xhr.onerror = function(){
        console.log("error");
    }

    xhr.send(params);
}
```
## Fetch API

An AJAX library using promises, available by default in all modern browsers. A promise is like a placeholder for a response we are going to get in the future.   

Fetch always returns a promise. Promise resolves to the response object. This response object has different helper methods like response.json(), response.text(), response.blob() etc.

Fetch also takes a second parameter, which is a configuration object.

### GET
```javascript
// ES5
function getData(){
    fetch("http://www.api.com/data") // Return response promise.
    .then(function(res){
        return res.json(); // Return data.
    })
    .then(function(data){
        return data; // Do something with data.
    })
    .catch(function(err){
        return err; // Catch error.
    });
}

// ES6
function getText(){
    fetch("http://www.api.com/data") // Return response promise.
    .then((res) => res.json()) // Return data.
    .then((data) => data) // Do something with data.
    .catch((err) => err); // Catch error.
}
```
### POST
```javascript
function postData(){
    fetch("http://www.api.com/data", {
        method: "POST",
        headers: {
            "Accept": "application/json",
            "Content-type": "application/json"
        },
        body: JSON.stringify({title:title, body:body}) // Payload
    }) // Return response promise.
    .then(res => res.json()) // Return data.
    .then(data => data) // Do something with data.
    .catch(err => err); // Catch error.
}
```

# Callback

A callback is a function that is to be executed after another function has finished executing — hence the name ‘call back’.

In JavaScript, functions are objects. Because of this, functions can take functions as arguments, and can be returned by other functions. Functions that do this are called **higher-order functions**. Any function that is passed as an argument is called a **callback function**.

```javascript
function foo(input, callback){
    console.log(input)
    callback()
}

// Anonymous callback. Logs Foo Bar.
foo("Foo", function(){
    console.log("Bar")
})

// Named callback. Logs Foo Baz.
foo("Foo", function(){
    baz("Baz")
})

function baz(input){
    console.log(input)
}

// Logs Baz Foo and "TypeError: callback is not a function
// This executes the baz function immediately.
foo("Foo", baz("Baz"))

// Works fine because it doesn't execute immediately.
// Must have no arguments. Logs Foo Qux.
foo("Foo", logQux)

function logQux(){
    console.log("Qux")
}

```

# Promises
The promise object is used for deferred and asynchronous computations and it represents and operation that hasn't completed yet, but is expected in the future.  

Promises are obtained with `.then()`.

```javascript
// Immediately resolved.
let myPromise = Promise.resolve("Foo");
myPromise.then((res) => console.log(res);) // Foo

// Delayed
var myPromise = new Promise(function(resolve, reject){
    setTimeout(() => resolve(4), 2000); // Return 4 after 2 seconds.
});

myPromise.then((res) => {
    res += 3; // Wait for the 4 and add 3 to it.
    console.log(res); // 7 after 2 seconds.
});
```

### HTTP GET Promise
```javascript
function getData(method, url){
    return new Promise(function(resolve, reject){
        let xhr = new XMLHttpRequest();
        xhr.open(method, url);
        xhr.onload = function(){
            if (this.status >= 200 && this.staus <= 300) {
                resolve(xhr.response);
            } else {
                reject({
                    status: this.status,
                    statusText: xhr.statusText
                });
            }
        }
        xhr.onerror = function(){
            reject({
                status: this.status,
                statusText: xhr.statusText
            });
        };
        xhr.send();
    });
}

getData("GET", "http://jsonplaceholder.typicode.com/todos")
    .then(function(data){
        let todos = JSON.parse(data);
        let output = "";
        for (let todo of todos) {
            output += `
                <li>
                    <h3>${todo.title}</h3>
                    <p>${todo.item}</p>
                `;
        }
        document.getElementById("list").innerHTML = output;
    })
    .catch(function(err){
        console.log(err);
    })
```

# Async/Await