# Node_JS_Practice_questions
A stream in Node.js is a way to read or write data piece-by-piece, without loading the entire data into memory.
If you load this fully into memory → ❌ Node.js will crash or lag.
With streams → ✔ Node handles it smoothly, because it processes small chunks.

Giva an example for get data using streams and without streams

reading file ssytem without using streams
=============================================================
let fs = require('fs');
const data = fs.readFileSync('dinesh.txt');
console.log(data);
Reading file system using streams
=============================================================
let fs = require('fs');
const stream =  fs.createReadStream('dinesh.txt');

stream.on('data',chunk=>{
console.log("chunk",chunk);
})

stream.on('end',()=>{
console.log("Done");
})

===============Cluster with Example===================
Cluster in Node.js is a built‑in module that allows us to run multiple Node.js processes (workers) that all share the same server port. 
Since one Node process uses only one CPU core, cluster helps us utilize all CPU cores, increase throughput, and improve reliability. 
If one worker crashes, the master will automatically restart it.

=================Why should we use ======================
Node.js is single‑threaded, so one process can use only one CPU core. 
On multi‑core servers, a single Node process under‑utilizes the machine.
Cluster lets me fork multiple workers so each worker uses a different CPU core. 
This gives better performance, more parallel request handling, and automatic restarts if a worker crashes
=============where to use==================
Cluster is used for scaling HTTP servers, API gateways, WebSocket servers, or any Node.js backend that needs to handle a large number of concurrent requests.
It is not good for CPU‑heavy tasks; for CPU‑bound work we should use worker_threads instead
========================= Example ======================
// cluster-example.js
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isMaster) {
  const numCPUs = os.cpus().length;
  console.log(`Master ${process.pid} is running`);

  // Create one worker per CPU
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // Restart worker if it crashes
  cluster.on('exit', (worker) => {
    console.log(`Worker ${worker.process.pid} died — starting a new one...`);
    cluster.fork();
  });

} else {
  // Worker processes create the server
  http.createServer((req, res) => {
    res.end(`Handled by worker ${process.pid}`);
  }).listen(3000);

  console.log(`Worker ${process.pid} started`);
}
===============Buffers==========================================================
A Buffer is a raw binary data container—think of it as an array of bytes.
Node.js uses Buffers to handle binary data efficiently, especially when dealing with:

Files (fs)
Network sockets (TCP/HTTP)
Streams (gzip, encryption, uploads)
Images, audio, protocol packets
Base64/Hex conversions


============ bind,call,apply====================================================
call(thisArg, ...args) → Invoke immediately, setting this to thisArg, pass args individually.
apply(thisArg, argsArray) → Invoke immediately, setting this to thisArg, pass args as a single array.
bind(thisArg, ...args) → Does NOT invoke immediately. Returns a new function with this permanently bound (and optionally pre‑filled arguments = partial application).

call — call now with a specific this (args individually)
===========================================================
function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

const person = { name: 'Dinesh' };

console.log(greet.call(person, 'Hello', '!')); // "Hello, Dinesh!"

apply — call now with a specific this (args as an array)
============================================================
function sum(a, b, c) {
  return a + b + c;
}

console.log(sum.apply(null, [1, 2, 3])); // 6

const nums = [5, 2, 9];
console.log(Math.max.apply(null, nums)); // 9
// Modern equivalent:
console.log(Math.max(...nums)); // 9

bind — create a new function with bound this (and optional partial args)
========================================================================

function multiply(factor) {
  return this.base * factor;
}

const ctx = { base: 10 };
const times = multiply.bind(ctx);   // 'this' permanently = ctx
console.log(times(5)); // 50

=============================what is spawn and fork in node js with proper example ==================
// spawn-example.js
import { spawn } from 'node:child_process';

// List files (Linux/macOS). On Windows, use: 'cmd', ['/c', 'dir']
const ls = spawn('ls', ['-lh', '.'], { stdio: ['ignore', 'pipe', 'pipe'] });

ls.stdout.on('data', (chunk) => {
  process.stdout.write(chunk); // stream to console
});

ls.stderr.on('data', (chunk) => {
  process.stderr.write(chunk);
});

ls.on('close', (code, signal) => {
  console.log(`ls exited with code=${code} signal=${signal ?? 'none'}`);
});

ls.on('error', (err) => {
  console.error('Failed to start process:', err.message);
});
================================================================================
spawn and fork both create child processes. 
spawn runs any external command and streams its stdout/stderr, 
so it’s ideal for long‑running or large-output tasks like ffmpeg or psql. 
fork is specialized for launching another Node.js script and provides a built‑in IPC channel using process.
send and message events—perfect for Node‑to‑Node worker processes and background jobs. 
I use spawn for external tools with streaming, and fork when I need two‑way messaging with another Node process

================================================================================
Explain the Node.js event loop phases.
What is the call stack, callback queue, microtask queue in Node.js?
Difference between process.nextTick(), setImmediate(), Promise microtasks?
What are worker threads? When do we use them?
How does Node.js handle CPU‑intensive tasks?
What is the libuv library and its role in Node.js?
Explain the event-driven architecture of Node.js.
Difference between synchronous and asynchronous code in Node.js.
Difference between CommonJS and ES Modules?
How do you handle 1 million records efficiently in Node.js?
What is clustering in Node.js? How to implement it?
How do you scale Node.js horizontally and vertically?
What is load balancing? How do you implement it with Node.js?
How to optimize memory usage in Node.js?
What is stream processing? When is it used?
Explain callback hell and how to avoid it.
Difference between Promise, async/await, callbacks.
What are generators? How can they help async programming?
What is backpressure in Node.js streams?
Explain microservices architecture in Node.js.
How do you communicate between microservices?
REST
Message brokers (Kafka, RabbitMQ)
gRPC
How do you implement distributed logging?
How do you handle transactions in microservices?
What is an API Gateway? Why do we use it?
Explain middleware in Express.
Difference between Express and Fastify.
How to implement global exception handling?
How do you create custom middleware for logging, security, validation?
What are route handlers and controllers?
Explain OWASP risks for APIs.
How to prevent XSS, CSRF, SQL Injection?
What is Helmet? How do you use it?
How do you securely handle secrets and environment variables?
How to set up rate limiting in Node.js?
What is JWT? How do you secure JWT tokens?
Why do we use Redis?
How to implement distributed caching?
How to avoid cache stampede?
Redis Pub/Sub vs Streams.
==============================================================================
Microtasks queue (a.k.a. Promise jobs):
Runs after the current JavaScript call stack finishes, and after each event-loop phase—before moving to the next phase.
Includes: Promise.then/catch/finally, queueMicrotask.

process.nextTick() queue (Node-specific):
Runs before the microtasks queue, immediately after the current operation completes, and also after each event-loop phase (it has even higher priority than Promise microtasks).
Can starve the event loop if abused.

setImmediate() (check phase):
Schedules a callback to run in the check phase (near the end of a turn of the event loop), after I/O events are processed.
Often runs before setTimeout(0) when scheduled from I/O callbacks

Example:
=====================================

console.log("start the Execution");

Promise.resolve().then(()=>{
    console.log("Promise Resolved");
});

setImmediate(()=>{
    console.log("Immediate log is executed");
})


process.nextTick(()=>{
    console.log("Process next Tick is executed");
})


console.log("End the Execution");
=========================output========================
start the Execution
End the Execution
Process next Tick is executed
Promise Resolved
Immediate log is executed

=========================callstack================
Functions are pushed on top of the stack when called.
When a function returns, it is popped off the stack.
Only one function runs at a time (Node.js is single-threaded at the JS level).
Callback Queue (a.k.a. Macrotask Queue)
setTimeout
setInterval
setImmediate
I/O callbacks
UI events
Timers
console.log("Start");

setTimeout(() => {
  console.log("Timeout callback");
}, 0);

console.log("End");
=================Microservices in Node js?==================================
Microservices architecture means breaking a large application into small, independent services.
In Node.js, each microservice is a lightweight Express/Fastify app with its own database, deployments, and scaling.
Services communicate via REST or message brokers like Kafka/RabbitMQ.
An API Gateway handles routing, authentication, rate‑limiting, and logging.
For consistency we use event‑driven patterns like Saga or Outbox.
Each service is deployed and scaled independently, giving high fault‑tolerance and better performance.

================Backend Web Frameworks (REST APIs)==================
Express.js – Minimal, fast, flexible, most widely used
Fastify – Faster than Express, schema‑based, great for high performance
NestJS – Enterprise-grade, TypeScript-first, Angular-style architecture
Koa.js – Created by Express team, modern middleware, async‑based
Hapi.js – Secure, configuration‑driven, enterprise-oriented
AdonisJS – Full-stack MVC framework, Laravel‑like
=========================================================================================
Difference between synchronous and asynchronous code in Node.js.

Synchronous code executes line‑by‑line, and each operation must finish before the next one starts.
If one task is slow (file read, DB query, API call), it blocks the entire thread and prevents Node.js from handling other requests.
❌ Problems
Blocks the event loop
Server becomes slow
Only one request can be handled at a time during blockin

const fs = require("fs");

console.log("Start");

const data = fs.readFileSync("bigfile.txt", "utf8"); // BLOCKING
console.log(data);

console.log("End");

Asynchronous code allows operations (file read, DB query, network call) to run in the background via the event loop without blocking the main thread.

Node continues executing other tasks while the async operation completes.
✔ Benefits

Highly scalable
Handles thousands of requests
Event loop remains free
Faster APIs

✔ Use Case

I/O operations (DB, File, Network)
Timers
Streams
Microservices communication

======================Example (Non-blocking)==================
const fs = require("fs");
console.log("Start");
fs.readFile("bigfile.txt", "utf8", (err, data) => {
  console.log("File read complete");
});
console.log("End");

===========================================================================
What is Helmet? How do you use it?
It is a middleware in node js Application that helps to secure your application
const express = require("express");
const helmet = require("helmet");
const app = express();
app.use(helmet());
app.get("/", (req, res) => {
  res.send("Helmet is working!");
});
app.listen(3000);

This applies 11+ security headers automatically.
-===================================================================
Explain loggers?
We implemented structured logging using Winston with JSON output, 
correlation IDs, and redaction of sensitive fields. 
In production, logs go to stdout and are scraped by Promtail, which ships them to Grafana Loki with stable labels like app, env, and service. 
In Grafana, we use LogQL with | json to filter by fields (status, path, correlationId), build dashboards (error rate, latency), and set alerts. 
For certain services we also support direct Loki transport using winston-loki, with batching and retries. This setup is fast, secure, observable, and easy to debug across microservices.

=============================================================
Redis chache mechanaism implement


// console.log("start the Execution");
// setImmediate(()=>{
//     console.log("Immediate log is executed");
// })

// process.nextTick(()=>{
//     console.log("Process next Tick is executed");
// })


// Promise.resolve().then(()=>{
//     console.log("Promise Resolved");
// });


// console.log("End the Execution");



/**
 * Example: Redis Caching in Node.js from Scratch
 * ----------------------------------------------
 * This example uses Express + Redis to cache API responses.
 */

//eVR9dp4b95TKeymVJp7ygKTY2S9GUgtB
// redis-15214.c266.us-east-1-3.ec2.cloud.redislabs.com:15214

const express = require("express");
const redis = require("redis");
const axios = require("axios");

const app = express();
const PORT = 3000;

// Create Redis client
const client = redis.createClient({
   password:'eVR9dp4b95TKeymVJp7ygKTY2S9GUgtB',
    socket: {
        host: "redis-15214.c266.us-east-1-3.ec2.cloud.redislabs.com",
        port: 15214,
    },

});

// Handle Redis connection events
client.on("connect", () => console.log("✅ Connected to Redis"));
client.on("error", (err) => console.error("❌ Redis Error:", err));

// Connect to Redis
(async () => {
    try {
        await client.connect();
    } catch (err) {
        console.error("Failed to connect to Redis:", err);
        process.exit(1);
    }
})();

/**
 * Middleware to check cache
 */
async function cache(req, res, next) {
    const { id } = req.params;
    try {
        const data = await client.get(id);
        if (data) {
            console.log("📦 Cache hit");
            return res.json({ source: "cache", data: JSON.parse(data) });
        }
        console.log("🚀 Cache miss");
        next();
    } catch (err) {
        console.error("Cache read error:", err);
        next(); // Continue without cache
    }
}

/**
 * Simulated database fetch
 */
function fetchFromDatabase(id) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve({ id, value: `Data for item ${id}`, timestamp: new Date() });
        }, 2000); // Simulate slow DB
    });
}

/**
 * Route with caching
 */
app.get("/item/:id", cache, async (req, res) => {
    const { id } = req.params;
    try {
        const data = await fetchFromDatabase(id);

        // Store in Redis with TTL (e.g., 60 seconds)
        await client.setEx(id, 60, JSON.stringify(data));

        res.json({ source: "database", data });
    } catch (err) {
        console.error("Error fetching data:", err);
        res.status(500).json({ error: "Internal Server Error" });
    }
});
app.get('/getProducts',async (req,res)=>{
    try{
        let products = await axios.get('https://dummyjson.com/products');
        res.send(products.data);

    }catch(error){  
        console.error("Error fetching data:", error);
        res.status(500).json({ error: "Internal Server Error" });
    }
})
app.listen(PORT, () => {
    console.log(`🚀 Server running on http://localhost:${PORT}`);
});
====================================================================================
console.log("A");
setTimeout(() => console.log("B - timeout"), 0);
setImmediate(() => console.log("C - immediate"));
Promise.resolve().then(() => console.log("D - promise"));
process.nextTick(() => console.log("E - nextTick"));
console.log("F");
===================output============================
A
F
E - nextTick
D - promise
B - timeout       // Usually
C - immediate     // Order may swap based on context
====================================================================================
==================Why Node js is a single threaded langauges==============
Node.js is single‑threaded at the JavaScript execution level because the event loop and call stack run on one thread.
However, Node.js is highly scalable because asynchronous I/O operations run in the background using libuv’s thread pool and OS kernel threads.
So Node.js is single-threaded in terms of programming model, but internally it uses multiple threads to handle heavy work.
=============================================================================
What is Concurrency in Node.js?
Concurrency in Node.js means handling multiple tasks at the same time using non‑blocking asynchronous operations.
Node.js uses the event loop and callback queue to allow many I/O operations to execute concurrently, even though JavaScript itself is single-threaded.
==================== Example =================================
console.log("Start");

setTimeout(() => {
  console.log("Timeout Finished");
}, 2000);

console.log("End");
========================Example using file syaytems=====================
const fs = require('fs');

console.log("Start reading files...");

fs.readFile('file1.txt', 'utf8', (err, data) => {
  console.log("File 1 done");
});

fs.readFile('file2.txt', 'utf8', (err, data) => {
  console.log("File 2 done");
});

console.log("Other work can continue...");

=========================output=======================
Start reading files...
Other work can continue...
File 1 done
File 2 done
==================================================
Why is this concurrent?
Both readFile operations run in parallel via libuv thread pool
Node continues executing without blocking

===========Explain libuv thread pool in Node js?===================
Node.js uses libuv’s thread pool to offload expensive operations like file I/O, crypto, and DNS, so the main thread stays free to handle more requests.
JavaScript is executed in a single thread, but libuv provides multiple threads internally for heavy work.

=============================================================
What is piping in node js with proper example 

In Node.js, piping is a mechanism to connect the output of one stream to the input of another stream. It lets data flow efficiently and incrementally without loading the entire content into memory—great for large files, network traffic, and real-time processing.

In short: readableStream.pipe(writableStream) moves data chunk-by-chunk with backpressure handled automatically.

================Example =====================
// copy-file.js
const fs = require('fs');
const path = require('path');

const source = path.join(__dirname, 'input.txt');
const dest = path.join(__dirname, 'output.txt');

const readable = fs.createReadStream(source);   // Readable stream
const writable = fs.createWriteStream(dest);    // Writable stream

readable.pipe(writable);

writable.on('finish', () => {
  console.log('File copied successfully!');
});

readable.on('error', console.error);
writable.on('error', console.error);
====================================================================
Difference between CommonJS and ES Modules? in node js

CommonJS (CJS):
CommonJS (CJS) uses require() and module.exports, loads modules synchronously, and is common in older Node projects.
ES Modules (ESM)
use import/export, are asynchronous, standard across browsers and Node, support top‑level await, and enable better tooling like tree‑shaking.
===================================================
what is load balancing in node js?
load balancing is a process of distributed  incoimg multiple request acoress multiple backend servives
its advan high avaiablity,scalaibity,high performance 

//================ find the occurance of charcters in an array in javascript ===================//

function  occuranceValues(arr){
  const counts =  arr.reduce((acc,curr)=>{
    acc[curr] =  (acc[curr] || 0) + 1
    return acc;

  },{});
  return counts;
}

let array  = [1,2,3,4,5,6,7,8,8,8,8,8,5,5,5,5,53,3,3,3];
console.log(occuranceValues(array));

// what is the ouput of the following code 
console.log(typeof null); // object
console.log(typeof undefined); //undefined
console.log(typeof NaN);// number
console.log(typeof {}); //object
console.log(typeof []);//object
console.log(typeof "");//string
console.log(typeof 123);//number
console.log(typeof function(){}); // function
console.log(typeof Symbol("sym")); //symbol
console.log(typeof BigInt(123));//bigint

//========================== Explain call.apply and bind =========================//
call(thsiArgs,...args) -- invoke immediately and arguments are passed as comma seperated
apply(thisArgs,argsArray) --invoke immedialtly and argumnets as single array
bind(thsiArgs,...args) -- does not innvoke and retuns a nes function with this
function greet(greeting,wel){
  return `${greeting} ${this.name} ,${wel}`

}
let user = {"name":"dinesh"};
console.log(greet.call(user,'Hiii','Welcome'));
using Apply
================
function sum(a,b,c){
  return a+b+c;
}
console.log(sum.apply(null,[1,2,3])); // 6
using bind
==============
function multiple(a ,b){
  return a * b;
}

let double = multiple.bind(null,2);
console.log(double(7));//14
//what is the output of the following code 
let a  = {x:1};
let b= a;
b.x =2;
console.log(a.x);//2
// what is the differnece between shallow copy and deep copy
shallow copy :
------------
A shallow copy copies only top level of an object/array
nested objects are not copied
exmaple:
let original  = {"name":"dinesh","age":30,"address":{"city":"chennai","state":"tamilnadu"}};
let shallowCopy  = {...original};
shallowCopy.name  = "Teja";
console.log(original.name);//teja

shallowCopy.address.city = "Bangalore";
console.log(original.address.city);//Bangalore
Deep copy:
============
A deep copy duplicate everything inclusing nested object 
original and copied object are totally indepenednt
if you can change one thing that is not effected the another one

const original = { 
  name: "Dinesh", 
  address: { city: "Hyderabad" }
};

let deepCopy = JSON.parse(JSON.stringify(original));

deepCopy.name = "Teja";
console.log(original.name); // Dinesh
original.name = "Dinesh Kumar";
console.log(deepCopy.name); // Teja
deepCopy.address.city = "Bangalore";
console.log(original.address.city); // Hyderabad

//============== waht is debouncing ===================
Debouncing is a technique to make sure a function runs only after a user stops performing an action for a specific delay. 
If the user keeps triggering the event, the timer resets.
For example, in a search box, instead of calling the API on every keystroke,
we call it only once after the user stops typing for, say, 300ms.
This reduces unnecessary API calls and improves performance.
example:
---------
Fix: Debounce input so you call the API after the user stops typing.
search implemention in when user type the keyword,when user stops the keyword after certail delay its call the API call.

===============Self calling function ========================
It is a function that executes immediately after it is defined.
(function (){
  console.log("self calling functions");
}());

What is the difference between spawn and fork methods in Node.js?
The spwan function() is used to create a new child process  and launch it using command line
 Node.js invokes this method when the child processes return data.
 Coming to the fork() method, it can be considered as an instance of the already existing spawn() method. 
 Spawning ensures that there is more than one active worker node to handle tasks at any given point in time.

 // occurance of cagaracters in a string

 function stringoccurance(str) {
    let stringArray = str.toLowerCase();
    let charCount = {};
    for (let i = 0; i < stringArray.length; i++) {
        if (charCount[stringArray[i]]) {
            charCount[stringArray[i]]++;
        } else {
            charCount[stringArray[i]] = 1
        }

    }
    return charCount;
}

let string = "hello world welcome to javascript programming";
console.log(stringoccurance(string)); // {h:1,e:3,l:3,o:3,w:2,r:2,d:1,c:1,a:2,j:1,s:1,p:1,g:1}
====================================================================================================
Method-2 using Higher order functions
let string = "hello world welcome to javascript programming";
let stringArray  =  string.split("");
console.log(stringArray);
const data = stringArray.reduce((acc,current)=>{
    if(acc[current]){
       acc[current] = acc[current]+1;
    }else{
        acc[current] = 1;
    }
    return acc;
},{})

console.log(data);

====================== flateendArray using recursion =====================
function flatEndArray(arr){
    let response  = [];
    for(let i=0;i<arr.length;i++){
        if(Array.isArray(arr[i])){
            response= response.concat(flatEndArray(arr[i]))
        }else{
            response.push(arr[i]);
        }
    }
    return response;

}

let arr = [1,2,[3,4],[5,6,[7,8]]];
console.log(flatEndArray(arr)); // [1,2,3,4,5,6,7,8]
===============Method 2====================

let flatEndArray = [1,2,[3,4],[5,6,[7,8]]].flat(Infinity);
console.log(flatEndArray)
===================Method 3 =====================
function flatEndArray(array){
  let results = [];
  let stack  = [...array];
  while(stack.length){
    const item  =  stack.pop();
    if(Array.isArray(item)){
      for(let i=0;i<item.length;i++){
        stack.push(item[i])
      }
    }else{
      results.push(item);
    }
  }
  return results.reverse();
}
let arr = [1,2,[3,4],[5,6,[7,8]]];
console.log(flatEndArray(arr)); // [1,2,3,4,5,6,7,8]

==========================Right Rotate an Array by k = 2===========
let arr = [1, 2, 3, 4, 5];
let k = 2;
output--- [4, 5, 1, 2, 3];

function rightRotateArray(arr, k) {
    k = k % arr.length; // here percentage is a;lways take sthe remainder value
    return arr.slice(-k).concat(arr.slice(0,-k));
}
let arr = [1, 2, 3, 4, 5];
let k = 2;
console.log(rightRotateArray(arr, k)); // [4, 5, 1, 2, 3]

========================Left  Rotate an Array by k = 2===========

function leftRotateArray(arr,k){

  let n= arr.length;
  k = ((k % n) + n) % n;
   return arr.slice(k).concat(arr.slice(0,k));

}

let arr = [1, 2, 3, 4, 5];
let k = 2;
console.log(leftRotateArray(arr, k)); // [4, 5, 1, 2, 3]

======================Kth largest eklement in an array =================
function kthLargetElement(arr,k){
    arr= arr.sort((a,b)=>b-a);
    return arr[arr.length -k];
}
let input =[10,20,30,80,50]
let k=1;
console.log(kthLargetElement(input,k)); // 6

======================Kth smallest klement in an array =================
function kthSmallestElement(arr,k){
    arr= arr.sort((a,b)=>a-b);
    return arr[arr.length -k];
}
let input =[10,20,30,80,50]
let k=3;
console.log(kthSmallestElement(input,k)); // 6

============get max occurance char in count ===============
function getMaxOcuuranceCharInCount(string){
  let charCount = {};
  let maxValue  =1;
  let obj = {}
  for(let i=0;i<string.length;i++){
    if(charCount[string[i]]){
      charCount[string[i]] ++
    }else{
      charCount[string[i]] = 1
    }
  }
 for(let item in charCount){
    if(charCount[item] > maxValue){
        maxValue = charCount[item];
        obj['maxChar'] = item;
        obj['maxValue'] = maxValue
    }
 }
 return obj;
}
let inputString  = "DineshD";
console.log(getMaxOcuuranceCharInCount(inputString))

===============================Most Non Repeted Charcters in a string=============================================
function firstNonRepeatingChar(str) {
  for (let i = 0; i < str.length; i++) {
    if (str.indexOf(str[i]) === str.lastIndexOf(str[i])) {
      return str[i];
    }
  }
  return null; // or '' or -1 based on your preference
}

let input  = "swiss";
console.log(firstNonRepeatingChar(input)); // nu
=====================================================================================
what is libvie thread pool in node js?
Node.js itself is single-threaded, but heavy operations are executed in a background thread pool provided by libuv.

libuv has 4 worker threads by default, 
which handle expensive tasks like file I/O, 
DNS lookup, compression, encryption, and hashing. 
This keeps the event loop free so Node remains non-blocking and scalable.


Node is single-threaded for executing JavaScript, 
but non-blocking because heavy tasks are pushed to libuv’s thread pool. 
This pool has 4 threads that run tasks like file I/O, 
encryption, compression, and DNS resolution. 
This prevents the main event loop from getting stuck and allows Node to handle thousands of requests efficiently.

===============What is Cluster in Node.js?=============
Node.js runs JavaScript in a single thread, meaning one CPU core is used by default.
But modern servers have multiple CPU cores.
The Cluster module allows you to create multiple Node.js processes (workers) that all share the same server port, 
enabling Node.js to use all CPU cores and handle more traffic.


The Node.js Cluster module is used to run multiple worker processes on all CPU cores.
It improves performance by parallelizing workload, because each worker handles requests independently while sharing the same server port.
The master process manages workers, restarts crashed ones, and does not handle HTTP requests directly

=======================spwan and fork =============================================
spawn is used to run any external command (like python, ffmpeg, ls, etc.).
It gives streaming output and does NOT create an IPC channel by default.
It is good for running heavy external programs or commandrs.


fork is a specialized version of spawn used to start another Node.js process.
It automatically creates an IPC channel (message passing), so parent and child can communicate using send() and message events.
It is useful for running background workers and parallelizing Node.js code.

===============================================================================================================================
1. what is event emitter?
2. what is Event loop?
3. what is clousers?
4. what is hositing?
5. what is debouncing?
6. what is event handlers?
7. what is shallowCopy and deepCopy?
8. what is call back functions?
9. what is async await promise?
10. what is cluster?
11. waht is spawn and fork?
12.inversion of control
13.function currying?
14.call,apply,bind?

=========================Hoisting ========================================
Hoisting is the JavaScript mechanism where variable and function declarations are moved to the top of their scope (global or function scope) during the creation phase of the execution context — before the code is executed.
Phase 1: Memory Creation Phase

JS scans the entire file
Allocates memory for variables and functions
Adds them to the Global Execution Context
var is initialized with undefined
let and const are placed in TDZ (Temporal Dead Zone)
Function declarations are stored completely

Phase 2: Code Execution Phase

JS executes the code line by line
Assignments happen here
TDZ ends when the variable is declared in code

====================================Temporal Dead Zone======================================
TDZ is the phase where a variable is in scope but cannot be accessed because it hasn’t been initialized yet. This happens with let and const before their declaration line.

===================================clousers=============================
A closure is a function bundled together with its lexical scope, allowing the function to access variables from the outer scope even after that outer function has finished executing.

=================what is lexial scope in javascript?=======================
If a function is written inside another function →it can use the outer function’s variables.
=================== what is event loop?=======================
The event loop is the system that allows JavaScript to handle asynchronous operations. It continuously checks the call stack, and when it’s empty, it takes the next task from the microtask queue (promises) or callback queue (timers, I/O) and pushes it to the call stack.

JavaScript runs your code in the Call Stack.
When it sees an async task (setTimeout, fetch…), it sends it to Web API.
When the async task finishes:

If it's a promise → goes to Microtask Queue
If it's a timer/callback → goes to Callback Queue


Event Loop checks:

👉 Is the Call Stack empty?
👉 If yes → first process Microtask Queue
👉 Then process Callback Queue

===================== Diff beteween Microtask Queue and Callback Queue=============
Microtask Queue (High Priority)
Contains:

Promises (.then(), .catch(), .finally())
async/await
MutationObserver
queueMicrotask()

These tasks run immediately after the current execution, before rendering or timers.

Callback Queue / Macrotask Queue (Low Priority)
Contains:

setTimeout
setInterval
setImmediate (Node.js)
I/O callbacks (file read, network requests)
DOM events
setTimeout(fn, 0) (still macrotask!)

These run after Microtasks are finished.

5. Why Microtasks Run First?
Because they are used for:

Resolving promises
Ensuring correct ordering of async code
Keeping the app stable and predictable

======================== what is call abck functions?==================
A callback function is a function that you pass as an argument to another function, so that it can be executed later.
Callbacks are mostly used for asynchronous tasks
Examples of async tasks:
setTimeout
API calls
reading files
database queries

==================== Why Do We Need Callbacks?==================
Because JavaScript is single‑threaded, but you still want to handle tasks like:
✔ API response
✔ Timers
✔ Reading files
✔ Database queries
without blocking the main thread.
Callbacks allow JS to continue running other code until the async task completes.

===============================promise keyword========================
Promises are objects that represent future values in JavaScript. They were introduced in ES6 (2015) to solve major problems caused by callback‑based async programming, such as callback hell, inversion of control, messy error handling, and inconsistent async patterns. Promises bring a cleaner, chainable syntax (.then, .catch), better error propagation, and serve as the foundation for async/await.

A Promise is used to handle asynchronous operations in a cleaner and more predictable way.

A promise has 3 states:

Pending
Fulfilled (Resolved)
Rejected
=================================what is event emitter?=====================
Event Emitters are objects in Node.js that allow different parts of your application to communicate with each other using events.
They follow the publish–subscribe (pub/sub) pattern:

Some code emits/raises an event
Other code listens for that event
When the event happens → the listener functions run automatically

Node.js is built on asynchronous, event‑driven architecture, so it needs a way to:
✔ handle events like file reading completion
✔ signal API response success
✔ broadcast messages
✔ communicate between modules
✔ trigger actions automatically

const EventEmitter = require('events');
const emitter = new EventEmitter();

// Listener
emitter.on('greet', () => {
  console.log('Hello Dinesh!');
});

// Emit event
emitter.emit('greet');

====================what is event handlers?===================
An event handler is a function that gets executed when an event occurs.
An “event” is anything the system detects like:

Click
Key press
Mouse movement
Form submit
Data received
Timer finished

===================what is debouncing in javasciprt?=============
Debouncing ensures a function runs only once after the user stops triggering the event
It is used to prevent unnecessary repeated function calls, especially in events like typing, resizing, scrolling, and clicking.

Imagine a search bar:

A user types: D, Di, Din, Dine, Dinesh
Without debouncing → API will call 5 times ❌
With debouncing → API will call only once ✔
(after the user stops typing)

===========================Throttling =====================
Throttling in JavaScript restricts the execution of a function so that it runs only once in a specified time period, no matter how many times the event gets triggered. This is extremely useful for high‑frequency events like scroll, resize, and mousemove.

=============what is call ,apply and bind in details with proper explanation==========================
call(thisArg, arg1, arg2, …) → invoke immediately, pass args individually.
apply(thisArg, [args]) → invoke immediately, pass args as an array.
bind(thisArg, arg1, arg2, …) → do not invoke now; returns a new function with this (and optionally some args) fixed for future calls.

==========================inversion of control ===================
Inversion of Control (IoC) means you hand over control of your function to another function, API, or framework, letting it decide when and how your code is executed. Callbacks, Promises, events, and frameworks all use IoC.

Imagine ordering food.
When you order from Zomato/Swiggy:

You don’t cook the food
You don’t decide when they start preparing it
You don’t decide who delivers it

They call you when food arrives.
This is Inversion of Control.

IoC solves many problems:
✔ 1. Better modularity & decoupling
Your code is cleaner because you don’t manage everything manually.
✔ 2. Reusable logic
Frameworks handle repetitive boilerplate (routing, events, async operations).
✔ 3. Avoiding callback hell (Promises & async/await)
Promises were introduced partly to fix “Inversion of control issues” with callbacks.
✔ 4. Better async handling

exmaples:

app.get("/home", (req, res) => {
  res.send("Home route");
});


==============What is Function Currying in JavaScript?=========
Currying is a technique that transforms a function with multiple arguments into a sequence of functions, each taking one argument at a time.
function createAPI(baseURL) {
  return function(endpoint) {
    return function(id) {
      return `${baseURL}/${endpoint}/${id}`;
    };
  };
}

const api = createAPI("https://api.myapp.com");

console.log(api("users")(10)); 
// https://api.myapp.com/users/10

console.log(api("posts")(20)); 
// https://api.myapp.com/posts/20

function log(type) {
  return function(message) {
    console.log(`[${type}] ${message}`);
  }
}

const info = log("INFO");
const error = log("ERROR");

info("Server started");
error("Server crashed");

==================what is shallowCopy and deepCopy?=========================
A Shallow Copy creates a new object, but it copies only the top‑level properties.
👉 If the object contains nested objects or arrays, the nested values are NOT copied.
Only the reference is copied.
This means modifying nested objects affects both copies.
const original = {
  name: "Dinesh",
  address: {
    city: "Hyderabad"
  }
};

// Shallow Copy
const copy = { ...original };

copy.address.city = "Bangalore";

console.log(original.address.city); // "Bangalore"

const copy = { ...original };

console.log(original === copy);               // false (different top-level objects)
console.log(original.address === copy.address); // true  (same nested reference)

================what is deep copy ======================
A Deep Copy creates a completely new object, and all nested objects/arrays are fully copied.
👉 No shared references. The two objects become 100% independent.

const original = {
  name: "Dinesh",
  address: {
    city: "Hyderabad"
  }
};

// Deep Copy
const deepCopy = JSON.parse(JSON.stringify(original));

deepCopy.address.city = "Bangalore";

console.log(original.address.city); // "Hyderabad" (unchanged)

console.log(original === deepCopy1);                 // false
console.log(original.address === deepCopy1.address); // false (independent)

==================== what is cluster?=========================
Node.js runs on a single thread, so only one CPU core is used.
 Cluster module solves this by creating multiple worker processes, 
 each running the same Node.js application on different CPU cores. 
 Each worker is an independent process, and all workers share the same server port. 
 The master process manages the workers, handles restarts, and helps load-balance incoming connections. 
 Cluster gives fault tolerance, better performance, and leverages multi-core CPUs efficiently.

It improves performance, increases reliability, and avoids Node.js single-thread limitations.

====================what is spawn and fork? ========================

spawn runs any external command (like ffmpeg, git, python) and streams stdout/stderr—great for long‑running tasks and large outputs.
fork launches a new Node.js process to run a JS module and gives you a built‑in IPC (message) channel for parent↔child communication—great for offloading CPU work and building workers.



15. what is Api gateway?
16.why node js is a single threaded langauage?
17.Explain promises methods
18.expalin diff between javaacript and typesctipt?
19.How to secure your node js Application?
20.what is package.json file
21.what is NPM
22.Node js a concurrency?
23.what is sync and async functions?
24.Explain self calling function and once function?
25.Give an exmaple for clousers?
26.Diff between not defined and undefined?
27.Difference between process.nextTick(), setImmediate(), Promise microtasks?
28.Explain the event-driven architecture of Node.js.
29.what is stack and queueue?


.=================why node js is a single threaded langauage?==========
Node.js is single‑threaded because it uses the JavaScript event loop and callback mechanism to handle asynchronous operations efficiently without blocking the main thread. 
This makes Node lightweight, fast, and scalable for I/O‑intensive tasks.

Node.js runs JavaScript on a single main thread, 
which gives developers a simple, non-blocking model. 
But internally Node.js is not single-threaded. 
It uses libuv’s thread pool (default 4 threads) 
to perform heavy tasks like file I/O, DNS, and crypto. 
Network operations are handled by OS-level threads. 
The V8 engine itself uses separate threads for garbage collection and compilation.
 So while your JavaScript doesn’t run in multiple threads, Node internally uses multiple threads to achieve high performance and non-blocking behavior.


=============What is package.json in Node.js?=====
package.json is the main configuration file for any Node.js project.
It stores important information about your project like:

Project name, version, description
Dependencies (libraries your app needs)
Scripts (commands you can run like start, test)
Entry file
Metadata about author, license, repository

It is essentially the heart of any Node.js project.

package-lock.json is automatically generated by npm and stores the exact versions of dependencies and sub‑dependencies.

====================What is NPM ?======================
NPM stands for Node Package Manager.
It is the default package manager for Node.js, used to:
✅ Install libraries (packages)
✅ Manage dependencies for your project
✅ Run project scripts
✅ Publish and share your own packages
NPM comes automatically when you install Node.js.

=========what is sync and async functions?and give an exmaples=================
Synchronous functions block the execution until they complete, while asynchronous functions allow the program to continue running other tasks without waiting. Node.js uses asynchronous functions to stay fast and handle thousands of requests concurrently.


=================what is the ouput of the following code?================
console.log("Start");

setTimeout(() => console.log("timeout"), 0);

setImmediate(() => console.log("immediate"));

Promise.resolve().then(() => console.log("promise"));

process.nextTick(() => console.log("nextTick"));

console.log("End");

Start
End
nextTick
promise
timeout
immediate


process.nextTick() runs callbacks immediately after the current function, before the event loop continues. It has the highest priority.
Promise microtasks run next, after nextTick but before any I/O or timers.
setImmediate() runs in the check phase of the event loop, after I/O callbacks.
The execution order is: nextTick → Promises → Timers → Immediate.


======================== self calling  functions==================
A Self‑Calling Function is a function that executes immediately as soon as it is defined — without being called manually.

✅ Why do we use IIFE?
IIFE solves several problems:
✅ 1. To create a private scope
Variables inside IIFE are not accessible outside.
✅ 2. To avoid global variable pollution
Prevents naming conflicts.
✅ 3. Used in old JavaScript before “let/const” existed.
✅ 4. Used for initialization code
(code that should run only once)

================What is a "Once Function"?=================
A Once Function is a function that runs only ONE time, even if called multiple times.
✅ Used when:

You want an initialization to happen only once
Connecting DB only once
Registering event handler one time
Setting config only once
Preventing multiple API calls
const greetOnce = once(() => {
  console.log("Hello only once!");
});

greetOnce(); // runs
greetOnce(); // ignored
greetOnce(); // ignored

eg:
Once Function using Closure
function initialize() {
  console.log("App initialized");
}

const runOnce = (() => {
  let done = false;

  return function() {
    if (!done) {
      done = true;
      initialize();
    }
  };
})();

runOnce();
runOnce();
runOnce();

==========Diff between not defined and undefined?===========
✅ 1. undefined
A variable is declared but not assigned any value.
✅ JavaScript creates memory for it
✅ You can access it
✅ Value is: undefined

✅ 2. not defined
A variable is NOT declared at all in your code.
❌ No memory created
❌ Cannot access it
❌ Causes a ReferenceError

=====what is stack and queueue? ==============
✔ What is a Stack?

A stack is a LIFO data structure where the last inserted element is removed first.

✔ What is a Queue?

A queue is a FIFO data structure where the first inserted element is removed first.

===================Explain promises methods==============
✅ Promise.all
✅ Promise.allSettled
✅ Promise.race
✅ Promise.any
✅ Promise.resolve
✅ Promise.reject
✅ Promise.finally

✅ Promise.all 
Runs all promises in parallel
✅ Resolves only if all promises succeed
❌ Rejects immediately if any promise fails

const p1 = Promise.resolve(10);
const p2 = Promise.resolve(20);
const p3 = Promise.reject(30);

Promise.all([p1, p2, p3])
  .then(result => console.log(result))
  .catch(err => console.log("Error:", err));

 Promise.allSettled
Runs all promises in parallel
✅ Waits for all of them
✅ Never fails
Gives result of both resolved and rejected promises.

const p1 = Promise.resolve("Success");
const p2 = Promise.reject("Fail");

Promise.allSettled([p1, p2]).then(console.log);


Promise.race()

Returns the result of the first promise that finishes
✅ Whether it resolves or rejects

const p1 = new Promise(res => setTimeout(() => res("Fast"), 100));
const p2 = new Promise(res => setTimeout(() => res("Slow"), 1000));

Promise.race([p1, p2]).then(console.log);

Promise.any()
Returns the first successful (fulfilled) promise.
✅ Ignores all rejected promises
❌ Rejects only if ALL promises fail

const p1 = Promise.reject("Error 1");
const p2 = new Promise(res => setTimeout(() => res("Success"), 500));

Promise.any([p1, p2])
  .then(console.log)
  .catch(console.error);

If all reject → it throws an AggregateError.

==============================Event‑Driven Architecture============================
Node.js uses an event‑driven architecture where the application flow is controlled by events and callbacks. The event loop listens for events like I/O completion, timers, or incoming requests and executes the associated callbacks asynchronously. This allows Node.js to handle thousands of concurrent connections efficiently using a single thread

====================Examples for clousers ==========================
To hide private data (Data encapsulation)
To maintain state without using global variables

===============what is descructing in javascript?================

Destructuring is a JavaScript feature that lets you unpack values from arrays and properties from objects into separate variables in a clean and concise way. It improves readability and reduces code duplication.

==============can we explain Spread operator vs Destructuring================
The spread operator (...) expands arrays/objects into individual elements, mainly used for copying or merging.
Destructuring extracts values from arrays/objects into variables.
Spread = Expand, Destructure = Unpack.

========================Rest operators ======================
Collect values into a single variable

function sum(...numbers) {
  console.log(numbers);
}

sum(1, 2, 3, 4);

30.what is buffers?
31.what is streams?
32.what is pipe?


Authentication verifies who the user is. 
Authorization checks what the user is allowed to do. 
In Node.js, we commonly authenticate using JWT tokens or sessions, 
and authorize using middleware that checks user roles or permissions. 
First, the user logs in and receives a JWT. 
Every request includes this token, which we verify.
Then we check the user’s role and allow or block access accordingly.

==========API Gateway =======================
API GATEWAY IS A SERVER THAT ACTS AS A SINGLE ENTRY POINT FOR ALL CLIENT REQUEST in a micro service artuchuture
//it recivies API Calls from client ,routes them to approrpite backend sevices ,handle to task like
//1.Routing
//2.Authication and Authorization
//3.Rate Limiting
//load Balacer
//loggers
// in production API Gateway are often to impelmented like using NGNIX,AWS API GATEWAY
// rate limit
// let rateLimit = require('express-rate-limit');
// const limiter = rateLimit({
// windowMs: 60 * 1000 * 1,
// max:5,
// message:"TOO Many Request"
// })
// app.use(limiter)
// load balancing
// Load Balancing is the process of distributing incoming requests across multiple backend servers (or instances) to ensure:
//High availability,Scalability,Fault tolerance,Better performance
//Imagine you have 3 instances of user-service running on:
// http://localhost:3001
// http://localhost:3002
// http://localhost:3003
// const express = require('express');
// const axios = require('axios');
// const app = express();
// // ✅ List of backend instances for user-service
// const userServiceInstances = [
// 'http://localhost:3001',
// 'http://localhost:3002',
// 'http://localhost:3003'
// ];
// let userServiceIndex = 0; // For round-robin
// // Round-robin function
// function getNextUserService() {
// const instance = userServiceInstances[userServiceIndex];
// userServiceIndex = (userServiceIndex + 1) % userServiceInstances.length;
// return instance;
// }
// // Gateway Route
// app.get('/user/:id', async (req, res) => {
// const targetUrl = getNextUserService();
// console.log(targetUrl)
// try {
// const response = await axios.get(`${targetUrl}/user/${req.params.id}`);
// res.send(response.data);
// } catch (err) {
// res.status(500).send('User Service unavailable');
// }
// });
// app.listen(8000, () => {
// console.log('API Gateway with Load Balancing running on port 8000');
// });
// explain cluster and node js Articheture
// NODE JS ARTICHURE
//Node.js follows a single-threaded event-driven architecture that is built on top of Google Chrome's V8 engine.
// It is designed to handle concurrent requests efficiently using an event loop and non-blocking I/O.
// Key Components Of Node js
// Google chrome V8 Engine
//single thread
// ✅ 2. Key Components of Node.js Architecture
// Component Description
// V8 Engine---- Converts JavaScript code to machine code. Developed by Google (used in Chrome).
// Single Thread --- All code runs on a single thread – but non-blocking operations make it efficient.
// Event Loop ---Handles asynchronous operations (like API calls, file reads) via events and callbacks.
// Libuv Library ---Provides the event loop and thread pool. Handles non-blocking I/O.
// Thread Pool--- Used internally for tasks that can’t be async (like file system/network tasks).
// Event Queue --- Queue where incoming requests/events are stored.
// Callbacks / Promises --- Used to handle results of async operations once they're ready.
// 3. Request Flow (Architecture Flow)
// Here’s how a request is processed:
// Client Request hits Node.js.
// Node.js places it in the Event Queue.
// The Event Loop picks the request.
// If it’s a simple task (e.g., variable assignment), it’s executed directly on the main thread.
// If it’s a non-blocking async task (e.g., DB call, file read):
// It’s delegated to the Libuv thread pool.
// Once completed, it’s sent to the callback queue.
// Event Loop picks it and sends the result back to the user.


Rate Limiting is a technique used to control the number of requests a user/client can make to your server within a specific time period.

======= what is streams==============
A Stream in Node.js is a mechanism to read or write data continuously in small chunks instead of loading everything into memory at once.
Types of Streams in Node.js
Readable Streams ---=>A Readable Stream lets you read data in chunks from a source.
Writable Streams --=> Writable Streams allow writing data in chunks.
Duplex Streams --->A stream that is both readable and writable.
Transform Streams --- >A Transform Stream is a duplex stream that modifies data as it passes through.

What is Piping in Node.js?

Piping is a mechanism in Node.js that allows you to connect the output of one stream directly to the input of another stream.

====================Buffers ============================
A Buffer in Node.js is a raw memory allocation used to handle binary data. 
It stores bytes and is used when dealing with files, network packets,
streams, and other low-level operations. 
Node uses Buffers because JavaScript cannot handle binary directly.
Buffers work with Streams to provide fast, memory-efficient I/O.


===============How to implement global exception handling?===================
Global exception handling in Node.js means catching all errors in a centralized place instead of writing try/catch everywhere. 
In Express, this is done using a global error-handling middleware. 
For asynchronous operations, we use libraries like express-async-errors. 
For unexpected errors like uncaught exceptions and unhandled promise rejections, 
we use process.on handlers. Global exception handling ensures the application doesn’t crash unexpectedly and provides consistent error responses and proper logging.


========================loggers ==========================
A logger is a tool or library used to record important information about your application while it is running.


==================How to impelemnt catch mechanaism in node js ===========
Redis caching improves performance by storing frequently accessed data in memory. 
When a request comes, we first check Redis. If data exists (cache hit), 
we return it instantly. If not (cache miss), we fetch from DB, store it in Redis with TTL, 
and return. We use cache-aside pattern, TTL for automatic expiry, and delete/update keys on data change. 
Redis drastically reduces DB load and improves response time.

✅ 1. Basics (100% asked)
✅ 1. What is TypeScript?
✅ 2. Difference between JavaScript and TypeScript?
✅ 3. What is a type? Why types are useful?
✅ 4. What is type inference?
✅ 5. What is any, unknown, void, never?
✅ 6. What is strict mode in TypeScript?
✅ 7. What is tsconfig.json and why is it used?
✅ 8. What are interfaces?
✅ 9. What is the difference between type and interface?
✅ 10. What are enums?

✅ 2. Functions (Very commonly asked)
✅ 11. What is optional parameter? (parameter?: Type)
✅ 12. What are default parameters?
✅ 13. What is function overloading?
✅ 14. What are arrow functions in TypeScript?
✅ 15. What is return type annotation?

✅ 3. OOP Concepts (Important for 2 years exp)
✅ 16. What are access modifiers? (public, private, protected)
✅ 17. What is readonly modifier?
✅ 18. What is an abstract class?
✅ 19. Can interface extend another interface?
✅ 20. Can a class implement multiple interfaces?

✅ 4. Intermediate Level (Asked in TS + Node.js interviews)
✅ 21. What are union types?
✅ 22. What are intersection types?
✅ 23. What is a tuple?
✅ 24. What is type alias?
✅ 25. What is the difference between let, const, and var in TS?
✅ 26. What are utility types? (Partial, Pick, Omit)
✅ 27. What is namespace in TypeScript?
✅ 28. What is module in TypeScript?
✅ 29. What are generics? (basic example)
✅ 30. What is keyof?

✅ 5.  TS in Real Projects (Companies test this)
✅ 31. How do you compile TypeScript to JavaScript?
✅ 32. What does "target" do in tsconfig?
✅ 33. What does "module" do? (commonjs, esmodule)
✅ 34. What is source map?
✅ 35. How do you handle type definition errors?
✅ 36. What is @types and DefinitelyTyped?
✅ 37. How do you create custom types for Express req/res?
✅ 38. How to use types for API request/response models?
✅ 39. How to use interfaces for DB models (Sequelize/TypeORM)?
✅ 40. How to handle exceptions with proper TypeScript types?

✅ 6. Practical Scenario Questions (Most important)
✅ 41. How to define type-safe environment variables?
✅ 42. How to ensure API returns strongly typed response?
✅ 43. How do you handle null/undefined safely in TS?
✅ 44. How to define an array of objects with types?
✅ 45. How to create reusable types using generics?
✅ 46. How to convert JS file into TS?
✅ 47. How do you type a function returning a promise?
✅ 48. How to write type-safe middleware?
✅ 49. How do you create an interface for a nested object?
✅ 50. How do you type a function that accepts dynamic key-value pairs?

====================Difference between JavaScript and TypeScript?====================
JavaScript is a dynamically typed scripting language that runs directly in the browser or Node.js. 
TypeScript is a superset of JavaScript that adds static typing, interfaces, generics, and compile‑time error checking. 
TypeScript code must be compiled to JavaScript before execution. 
It provides better tooling, readability, and maintainability—making it ideal for medium to large-scale applications.

==================What is a type? Why types are useful?==================
A type tells the compiler what shape/value a variable should have.
Types give safety, clarity, and maintainability to your code.
“A type defines what kind of value a variable can hold, such as number, string, or an object. 
Types are useful because they catch errors at compile‑time, improve code readability, provide better IntelliSense, and make large applications easier to maintain. TypeScript’s type system reduces bugs and ensures more predictable, safer code.”

==========What is type inference?=============
Type inference is when TypeScript automatically determines the type of a variable based on its assigned value. 
It helps reduce the need for writing explicit types while still providing full type safety. 
For example, when I write let count = 10, TypeScript infers the type as number.

===========any======unknown=======void========
any disables type checking completely. It’s the most flexible but also the most dangerous type.
unknown is similar to any, but safer.
You must check the type before using the variable.
“unknown is a safer version of any. You cannot use an unknown value without checking its type.”
void means the function does not return anything.
If a function returns null or undefined → void is allowed
never is for functions that never return, like throwing errors or infinite loops.”

====What is strict mode in TypeScript?====
Strict mode is a TypeScript compiler option (strict: true) that turns on all strict type‑checking rules. It enables features like strictNullChecks, noImplicitAny, strictFunctionTypes, etc. This helps TypeScript catch more errors at compile time and ensures the code is safer, more predictable, and easier to maintain. Most production applications use strict mode for better type safety

====What are union types?=====
Union types allow a variable to accept multiple types. For example, let id: number | string means id can be either number or string. Union types add flexibility but still maintain type safety. TypeScript requires narrowing checks before using the value, making the code safer and more predictable.
======= what are intersection types?=============
Intersection types allow you to combine multiple types into one using the & symbol. 
A value with an intersection type must satisfy all the included types. For example, type A = X & Y means the object must contain all properties of both X and Y. It's useful when merging models, extending interfaces, or creating rich composite types in TypeScript.

type Timestamp = {
  createdAt: Date;
  updatedAt: Date;
};

type User = {
  id: number;
  name: string;
};

type FullUser = User & Timestamp;

const user: FullUser = {
  id: 1,
  name: "John",
  createdAt: new Date(),
  updatedAt: new Date()
};
``
============How to handle exceptions with proper TypeScript types?=======
In TypeScript, exceptions must be strongly typed.
I always throw Error objects or custom errors instead of throwing strings. In catch blocks, I use the unknown type and perform type‑narrowing like instanceof Error. 
I create custom error classes such as AppError with status codes, and I define typed error response interfaces for API consistency. 
This ensures predictable error handling, better IntelliSense, safer code, and avoids runtime issues.

============What is optional parameter? (parameter?: Type)===============
An optional parameter in TypeScript is defined using ?:. 
It means the parameter is not mandatory when calling the function. 
TypeScript treats optional parameters as type | undefined. 
Optional parameters must come after required parameters and are commonly used in functions where not all arguments are always needed.
