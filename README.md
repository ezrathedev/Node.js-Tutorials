

### Install Node.js:


 ![nodeJS image](/images/nodeImg.png)

 
 After you install node.js on your local machine test it in CMD – node -v (should display the version) 
 ```html
 C:\Users\aygun>node -v         ->  v10.16.3
 ```
### Introduction to Node.js and NPM

-	Node.js is a JavaScript Runtime build on Google’s open source V8 JavaScript Engine. 
-	Node.js enables JavaScript to be run in a different environment than just the browser.
-	The V8 Engine executes the code – parse it and runs it in Node.js

 ![NodeJS and V8](/images/nodeV8.png)

### What is Node.js
![NodeJS why and when](/images/nodeWhy.png)


#### Running JavaScript Outside the Browser

-	Start interacting with node in the command line; - we are going to use the build in terminal
Type-in node  -> this will open up Node REPL  - Read-Evaluate-Print-Loop. It is very handy when we want to quickly test a JavaScript code snippet. 
To exit REPL type in .exit or Ctrl + D
Hit tab tab and you will see all the global variables that are available in Node
When you type in an underscore will give you the previous output to use.
Type in String. tab tag will give you the methods of the String class

#### Using Modules: Core Modules

-	Start a new .js file in the root directory
-	Type in some JavaScript code.
-	To run this file in the browser we would have to attach it to an html file;
but this is how we could run it in Node - 
In the Terminal -> command node and the name of the file -> node index.js

``` javascript
const hello = "Hello World";
console.log(hello);

```

-	More advanced features of Node would be reading files from the files system. 
In order to that we would need to use a Node Module – all kind of functionality is stored in Modules; in this case it would be the fs module (file stream). 
To make use of these modules we require them into our code and then store the result of the requiring function in a variable.
const fs = require('fs');

#### Node Documentations:

![NodeJS why and when](/images/nodeDoc.png)


### Reading and Writing Files
	
#### Read from file

![NodeJS why and when](/images/nodeReadWrite.png)

``` javascript
const fs = require('fs');
const textIn = fs.readFileSync('./txt/input.txt','utf-8');
console.log(textIn);
	Run it in node -> this will read and display the content of the input.txt file.
	Write to file
const fs = require('fs');

// ES6 syntax:
const textOut = `This is what we know about the avocato: ${textIn}.\nCreated on ${Date.now()}`;
// Previous Syntax without the backstrings:
// 'this is: ' + textIn;
fs.writeFileSync('./txt/output.txt',textOut);
console.log('File written');

```

### Blocking and Non-Blocking: Asynchronous Nature of Node.js

- Non-Blocking I/O model –asynchronous read function - instead of blocking the one single thread this model does the heavy work in the background. Where it stays until it’s finished reading the data from the file.
- We then register a callback function to be called once the data is available. All other users can perform their tasks in a single thread one after another while the file is still being read in the background. In PHP you get one thread for each user.
- A callback in our code does not make it automatically asynchronous. Passing functions in another functions is common in JavaScript. 
![NodeJS blocking none blocking](/images/nodeBlocking.png)

#### Callback Hell 
The callback model - functions is called once the one before has finished its work can quickly lead to some hard to read and manageable code. 
##### For example: 
The second file read depends on the first one; than the third file read depends on the second one; and then the finally we want to use the final data to write as a result. This makes it hard to read and reason about. This problem is called *Callback Hell*. To solve this problem, we can use *ES6 Promises or ES8 async/await*. 
```javascript
const fs = require('fs');

fs.writeFile('start.txt', 'utf-8', (err,data1) => {
    fs.writeFile(`${data1}.txt`,'utf-8',(err,data2) => {
        fs.readFile('append.txt','utf-8',(err,data3) => {
            fs.writeFile('final.txt', `${data2} ${data3}` ,'utf-8', (err) => {
                if(err) throw err;
            });
        });
    });
});
```
-	Node was originally designed around callbacks and we still will be using them.

### Reading and Writing Files Asynchronously

##### Example:
```javascript
const fs = require('fs');
//third parameter is a callback function() with two arguments; (err if there was one and the actual data itself.)
fs.readFile('./txt/start.txt', 'utf-8', (err, data)  => { 
    console.log(data);
});
console.log('Will read file') // this will run before the callback function. NodeJS will read the file in the background and won't block the code and will immediately move to the next line of code. Once everything is read it will return to the call back funcion to run.
```
##### OUTPUT:
PS C:\Users\aygun\OneDrive\Documents\Udemy\The Complete Bootcamp2019\complete-node-bootcamp-master\complete-node-bootcamp-master\1-node-farm\starter> node index.js
Will read file
read-this
##### Example: Steps that depend on the result of the previous step.
```javascript
const fs = require('fs');
//third param is a callback function with two arguments; err if there was one and the acutal data itself.
fs.readFile('./txt/start.txt', 'utf-8', (err, data1)  => { 
    fs.readFile(`./txt/${data1}.txt`, 'utf-8', (err, data2)  => { 
        console.log(data2);
        fs.readFile('./txt/append.txt', 'utf-8', (err, data3)  => { 
            console.log(data3);
        });
    });
});
```
PS C:\Users\aygun\OneDrive\Documents\Udemy\The Complete Bootcamp2019\complete-node-bootcamp-master\complete-node-bootcamp-master\1-node-farm\starter> node index.js
The avocado �🥑 is also used as the base for the Mexican dip known as guacamole, as well as a spread on corn tortillas or toast, served with spice
APPENDIX: Generally, avocados �🥑 are served raw, but some cultivars can be cooked for a short time without becoming bitter

##### Example: we want to write the last two strings into a file.
```javascript
const fs = require('fs');
//third param is a callback function with two arguments; err if there was one and the acutal data itself.
fs.readFile('./txt/start.txt', 'utf-8', (err, data1)  => { 
    if(err) return console.log('ERROR');   //error handler 
    fs.readFile(`./txt/${data1}.txt`, 'utf-8', (err, data2)  => { 
        console.log(data2);
        fs.readFile('./txt/append.txt', 'utf-8', (err, data3)  => { 
            console.log(data3);
            // only err argument needed; use template string the data 
               we want to write as an additional argument.
            fs.writeFile('./txt/final.txt', `${data2}\n${data3}`, 'utf-
8', err => {  
                console.log('Your file has been written.');
            });
        });
    });
});
```
The arrow functions syntax is ES6. This is how it would look like without the arrow function:
```javascript
function(err, data1){ //a function always gets the this keyword. }
```
### Creating a Simple Web Server
-	Accepting request and sending back responses – Networking capability
-	For that we need to include another module - HTTP
```javascript
const http = require('http');
```

- Creating the Server is the first part.
```javascript
http.createServer((req,res) => { // takes in two args, request variable and response variable
    res.end('Hello from the server'); // (The response and request objects have tools that we can use )
});
```
Listening the incoming request is the second part. 
In order to do that is to save the result of createServer to a variable
```javascript
const server = http.createServer((req,res) => { 
    res.end('Hello from the server'); 
});
// use that server an on that call listen.
server.listen(8000, '127.0.0.1', () => {
    console.log('Listening to requests on port 8000');
});
```
	Run the node application; We can get to this URL on port 8000 on our computer 
##### Output:
PS C:\Users\aygun\OneDrive\Documents\Udemy\The Complete Bootcamp2019\complete-node-bootcamp-master\complete-node-bootcamp-master\1-node-farm\starter> node index.js
Listening to requests on port 8000
![NodeJS Server display](/images/nodeServer.png)

*Note: To stop the server running => Ctrl + C*

### Routing
	For routing use the ‘url’ model.
```javascript
const url = require('url');
```
*(Note: To select multiple word phrases in visual studio, highlight the word and press Ctrl + D)*

##### Example: we will check the path names and respond to that particular url
```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req,res) => { 
    const pathName =  res.url;                      requested url from the page
    console.log(res,url);
    res.end('hello from the server');
    if(pathName === '/' || pathName === '/overview'){
        res.end('This is the OVERVIEW');
    }else if(pathName === '/product'){
        res.end('This is the PRODUCT');
    }
});

server.listen(8000, '127.0.0.1', () => {
    console.log('Listening to requests on port 8000');
});
```
##### Run the server:
PS C:\Users\aygun\OneDrive\Documents\Udemy\The Complete Bootcamp2019\complete-node-bootcamp-master\complete-node-bootcamp-master\1-node-farm\starter> node index.js
Listening to requests on port 8000

##### OUTPUT:
![NodeJS Routing display1](/images/nodeRouting1.png)
![NodeJS Routing display2](/images/nodeRouting2.png)

When there is a request that we that wasn’t handled in the code, the server will not respond, and the search wheel won’t stop spinning. To handle this problem, add another route within the else statement.
```javascript
const server = http.createServer((req,res) => { 
    const pathName =  req.url;

    if(pathName === '/' || pathName === '/overview'){
        res.end('This is the OVERVIEW');
    }else if(pathName === '/product'){
        res.end('This is the PRODUCT');
    }else{
        res.end('Page not found!')
    }
});
```
 A 404 is an http status code; so, we can also add the http status to the response.
```javascript 
    }else{
        res.writeHead(404);     You can examine the server response in Dev tools(inspect). 
        res.end('Page not found!')
    }
```
A HttpHeader is a piece of information about the response *(metadata about the response)*. We can use writeHead () to send back headers by adding objects as a parameter to the method. One of the standard headers is to inform the browser of the Content Type. When we set to Content Type to text/html, the browser will now expect some html.
We can also add our own headers in here and pass on data about the response.
##### Example:
```javascript
const server = http.createServer((req,res) => { 
    const pathName =  req.url;

    if(pathName === '/' || pathName === '/overview'){
        res.end('This is the OVERVIEW');
    }else if(pathName === '/product'){
        res.end('This is the PRODUCT');
    }else{
        res.writeHead(404, {
            'Content-type':'text/html',
            'my-own-header':'hello-world'
        });
        res.end('<h1>Page not found!</h1>')
    }
});
```

![NodeJS Routing display3](/images/nodeRouting3.png)

### Building a (Very) Simple AP

- API – A service from which we can request some data. 
In this example some data that we are offering in the application – The data is in our json file. JSON is a simple text format that looks like a JavaScript Object. Each object inside this array has keys which must be of type string and a values attached to each key it. This is the data that the API will send to the client when requested.
##### Example: 
First add another route to our project and a simple placeholder for the response.
```javascript
const server = http.createServer((req,res) => { 
    const pathName =  req.url;

    if(pathName === '/' || pathName === '/overview'){
        res.end('This is the OVERVIEW');
    }else if(pathName === '/product'){
        res.end('This is the PRODUCT');
    }else if(pathName === '/api'){
        res.end('API');
    }else{
        res.writeHead(404, {
            'Content-type':'text/html',
            'my-own-header':'hello-world'
        });
        res.end('<h1>Page not found!</h1>')
    }
});
```

- Next, 
  - Read data from the data.json file, then
  - parse JSON into JavaScript, and then 
  - send back the result to the client.
To find where the current file is located - a path that starts with a dot changes depending on where we run node. A better way is to use __dirname variable, which will locate the script that we want to execute. For that make use of a template string and use the variable. (The required functions are an exception to this rule.)
When sending back the data we need to we need to tell the browser the content type. In our case it’s JSON. 
```javascript
const fs = require('fs');
const http = require('http');
const server = http.createServer((req,res) => { 
    const pathName =  req.url;

    if(pathName === '/' || pathName === '/overview'){
        res.end('This is the OVERVIEW');
    }else if(pathName === '/product'){
        res.end('This is the PRODUCT');
    }else if(pathName === '/api'){
        fs.readFile(`${__dirname}/dev-data/data.json`,'utf-8', (err, data) => {
            const productData = JSON.parse(data);
            res.writeHead(200,{ 'Content-type': 'application/json'});
            res.end(data);
        });
    }else{
        res.writeHead(404, {
            'Content-type':'text/html',
            'my-own-header':'hello-world'
        });
        res.end('<h1>Page not found!</h1>')
    }
});
```
![NodeJS API](/images/nodeAPI.png)

##### The issue with this code:
Each time a user makes that API request, the server will read the file to send it back. A better solution would be if a user hits this route to send back the data without having to read it every time. In this case we will use the synchronous version for the read function, which won’t be a problem because we can place it in the top level. Top Level code is only executed once in the beginning. 
The Callback function  (   *http.createServer((req,res) => { … });*  )  is the code that is executed every time a user makes a new request. The code that is outside the callback function, the top-level code, is only executed once when we start the program. 
*(We need to have a good understanding of which code is blocking and why.)*	
To solve this issue update your code to :
```javascript
const data = fs.readFileSync(`${__dirname}/dev-data/data.json`, 'utf-8');
const dataObj = JSON.parse(data);				      	             top-level code

const server = http.createServer((req,res) => { 
    const pathName =  req.url;

    if(pathName === '/' || pathName === '/overview'){
        res.end('This is the OVERVIEW');
    }else if(pathName === '/product'){
        res.end('This is the PRODUCT');
    }else if(pathName === '/api'){		                      add another route
        res.writeHead(200,{ 'Content-type': 'application/json'});
        res.end(data); 							       sending back json
    }else{
        res.writeHead(404, {
            'Content-type':'text/html',
            'my-own-header':'hello-world'
        });
        res.end('<h1>Page not found!</h1>')
    }
});

```

----------------------------------------

  1. What is the Title of The Man in the Taupe Blazer?
  2. What does that title mean?
  3. What languages did you see, where are they on the developer road map?
  4. What is the conclusion at the end of the article?


#### Plan for the day:

1. What is the roadmap for this week?
  1. Front-End
    - Git
    - Basic Terminal Usage
    - Learn to Research
    - Character Encodings
    - Github
    - HTML
    - CSS

2. VS-Code Familiarization
  - Terminal
    - Opened with `ctrl + \``
    - terminal/shell
    - Language is called *CLI*
  - File Explorer
    - Opened with `ctrl + b`
    - This shows you the file structure of your system
    - Folders vs. Workspace
    - You can just right click in the file explorer to add folders and files
  - Command Pallete `ctrl + p` or `ctrl + shift + p`
    - Show keyboard shortcuts `ctrl + k, ctrl + s`

3. HTML (Lesson 1: Building Your First Web Page)
  - Define HTML & CSS
  - *HTML Terms:*
    - elements
    - tags
    - attributes
    - HTML document structure
    - self closing elements
    - code validation
  - *CSS Terms:*
    - selectors
    - properties
    - values
    - type selectors
    - class selectors
    - id selectors
    - additional selectors
  - Referencing CSS
  - CSS Resets
  - Cross Browser compatibility
4. What is Github/Git?
  - VCS: Version Control System
  - DVCS: Distributed Version ontrol System
  - Git: is the VCS we are going to use
  - Git Projects (repositories)
    - Commands:
      - git init
      - git clone
      - git add
      - git commit
      - git status
      - git pull
      - git push
      - git status
  - GitHub to Git


