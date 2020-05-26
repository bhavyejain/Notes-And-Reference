# AJAX & JQuery

## AJAX Intro

AJAX := Asynchronous JavaScript and XML

Allows client side of application to communicate with the server.

Without AJAX : use page loads.

Although X in Ajax stands for XML, JSON is used more than XML nowadays because of its many advantages such as being lighter and a part of JavaScript. Both JSON and XML are used for packaging information in the Ajax model.

Process of AJAX script:
1. Some action triggers event, e.g. a button click.
2. AJAX call fires, sends request to a server-side script, using XML.
3. Server takes input from JS, processes the data.
4. Using XML again, script sends data back to the original client side page.
5. A second JS function, called a callback function, catches the data, and updates the webpage.

Some uses of AJAX:
*  Login Forms   [JQuery Form Plugin](http://malsup.com/jquery/form/#getting-started)
*  Autocomplete input   [JQ Autocomplete](https://web.archive.org/web/20091102025658/http://plugins.jquery.com/project/jq-autocomplete)
*  Voting and Rating 
*  Instant update with user content
* Form submission and validation
* Chat rooms and instant messaging [JQuery chat](http://anantgarg.com/2009/05/13/gmail-facebook-style-jquery-chat/)

Detailed introduction to AJAX: [Ajax: A New Approach to Web Applications](https://pdfs.semanticscholar.org/c440/ae765ff19ddd3deda24a92ac39cef9570f1e.pdf)

________________________________
## AJAX with pure JavaScript

### STEP 1 : Make HTTP Request
Create instance of `XMLHttpRequest` object:
```js
httpRequest = new XMLHttpRequest();
```

To specify what function to call after receiving a response, use `onreadystatechange` property of the XMLHttpRequest object.
```js
httpRequest.onreadystatechange = myFunction; 
// no () following myFunction since we are assigning a reference to the function
```

We can also use anonymous functions to define actions to the response.
```js
httpRequest.onreadystatechange = myAnonymousFunction() {
    // process server reponse here
};
```

To make the request, use `open()` and `send()`.
```js
htpRequest.open('GET', 'http://www.example.org/some.file', true);
httpRequest.send();
```

`open()`:
* First parameter -> HTTP request method - GET, POST, HEAD, etc.
* Second parameter -> URL for request.
* Third parameter -> Specify whether request is asynchronous or not. (Default: true)

`send()`:
* Parameter to send() method can be any data we want to send to the server if POST-ing the request. 
* The data can be query string, JSON, XML, and so on.

Note that if you want to POST data, you may have to set the MIME type of the request. For example, use the following before calling send() for form data sent as a query string:
```js
httpRequest.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
```
______________________________________
### STEP 2 : Handling the response

We had provided the name of a JS function to handle the response earlier. First, the function needs to check the request's state. If the state has the value of `XMLHttpRequest.DONE` (corresponding to 4), that means that the full server response was received and it's OK for you to continue processing it.
```js
if (httpRequest.readyState === XMLHttpRequest.DONE){
    // everything is good, response received
}
else{
    // not ready yet
}
```

`readyState` values:
* 0 (uninitialized) or (request not initialized)
* 1 (loading) or (server connection established)
* 2 (loaded) or (request received)
* 3 (interactive) or (processing request)
* 4 (complete) or (request finished and response is ready)

Next, check the response code (inside the true block of previous check):
```js
if(httpRequest.status === 200){
    // perfect
    var response = JSON.parse(httpRequest.responseText);    // sample processing of JSON format string
} else {
    // problem: 404, 500, etc
    var status = httpRequest.statusText;    // get status text
}
```

To access data:
* `httpRequest.responseText` - returns the server response as a string of text.
* `httpRequest.responseXML` – returns the response as an `XMLDocument` object you can traverse with JavaScript DOM functions.
________________________________
#### Example with callback functions:
```js
function makeAjaxCall(url, methodType, callback){
    var xhr = new XMLHttpRequest();
    xhr.open(methodType, url, true);
    xhr.send();
    xhr.onreadystatechange = function(){
        if (xhr.readyState === 4){
        if (xhr.status === 200){
            console.log("xhr done successfully");
            var resp = xhr.responseText;
            var respJson = JSON.parse(resp);
            callback(respJson);
        } else {
            console.log("xhr failed");
        }
        } else {
        console.log("xhr processing going on");
        }
    }
    console.log("request sent succesfully");
}

var URL = "https://api.github.com/users/"+userId;
makeAjaxCall(URL, "GET", processUserDetailsResponse);   // provide unique callback function with the call

var URL = "https://api.github.com/users/"+userId+"/repos";
makeAjaxCall(URL, "GET", processRepoListResponse);     // provide unique callback function with the call

function processUserDetailsResponse(userData){
    console.log("render user details", userData);
}

function processRepoListResponse(repoList){
    console.log("render repo list", repoList);
}
```
____________________________________________

## AJAX wih JQuery

#### A simple example:

```js
$(document).ready(function() {      // check if the document has finished loading
    $.ajax('result.html').done(function(response) {     // jQuery AJAX function to get a file result.html
        $('#result').html(response);        // chained done() function to execute after the response is received
    });
});
```
#### Another example:

```js
$.ajax({
    method: "POST",             // request method
    url: "some.php",            // request URL
    data: {                     // provide data for request
        name: "Bhavye",
        location: "Dehradun"
    },
    success: function(response) {            // callback function to execute if request is successful
        alert("Data saved: " + response);
    },
    error: function(e) {            // handle error in request
        alert("Error: " + e);
    }
});
```

`$.ajax` calls jQuery's AJAX funxtionality.

`success: function(result) { ... }` is an alternative to `.done`.

Syntax for jQuery ajax: `$.ajax({name: value, name: value, ...});`

Possible name/values:
| Name              | Value          
|-----------        |-------
| async              | Boolean - indicates whether request is to be sync/async (default true)
| beforeSend(xhr)    | A function to run before the request is sent
| cache              | Boolean - browser should cache requested pages or not (default true)
| complete(xhr, status)| Function to run when request is finished
| contentType       | Type of content being sent to server. (Default: "application/x-www-form-urlencoded")
| data              | Data to be sent to server
| dataType          | Data type expected of the server response
| context           | Specifies the 'this' value for all AJAX related callback functions
| error(xhr,status,error)| A function to run if the request fails
|username	        | Specifies a username to be used in an HTTP access authentication request
| password          | Specifies a password to be used in an HTTP access authentication request
| success(result,status,xhr)	| A function to be run when the request succeeds
| timeout	        | The local timeout (in milliseconds) for the request
| type	            | Specifies the type of request. (GET or POST)
| url	            | Specifies the URL to send the request to. Default is the current page
| xhr	            | A function used for creating the XMLHttpRequest object

#### Example with callback:
```js
function makeAjaxCall(url, methodType, callback){   // function that accepta a callback function as a parameter
 $.ajax({
    url : url,
    method : methodType,
    dataType : "json",
    success : callback,                 // specify the callback function received as a parameter
    error : function (reason, xhr){
     console.log("error in processing your request", reason);
    }
   });
 }

var URL = "https://www.example.com/someendpoint/";

makeAjaxCall(URL, "GET", function(respJson){
    // do something with JSON data
});
```

#### Example with chained functions:
```js
$(document).ready(function() {      
    $.ajax('result.html', {
        beforeSend: function() {        // executes before the request is sent
            $('#status').text('Loading...');
        }
    })
    .done(function(response) {      
        $('#result').html(response);        
    })
    .fail(function(request, errorType, errorMessage) {
        alert(errorMessage);
        console.log(errorType);
    })
    .always(function() {                // this method executes at the end, no mtter what
        $('#status').text('Completed');
    });
});
```

____________________________________________
## References
1. https://www.jotform.com/blog/how-ajax-works/
2. https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX
3. https://medium.com/front-end-weekly/ajax-async-callback-promise-e98f8074ebd7
4. https://riptutorial.com/ajax