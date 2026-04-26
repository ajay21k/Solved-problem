## 📌 Simple Function Example

const hello = (hname) =>{  
    console.log(`${hname}`);  
}  
hello("Ajay");

### 🔍 Explanation

- Arrow function `hello`
- Takes parameter `hname`
- Prints the value using template literal

---

## 🌍 Global Objects

console.log(global);

### 🔍 What is `global`?

- Global object in Node.js (like `window` in browser)

⚠️ Note:

- ❌ `window` is **not available** in Node.js
- ✅ Node uses `global` instead

---

# ⚡ Async Function

async function myfunction(){  
    return "Hello";  
}  
  
console.log(myfunction())

### 🔍 Explanation

- `async` function always returns a **Promise**

👉 Output:

Promise { 'Hello' }

### ✅ To get actual value:

myfunction().then(res => console.log(res));

---

# ⏱️ Timers in Node.js

## 📌 setTimeout()

setTimeout(()=>{  
    console.log("This is an time out function")  
    clearInterval(intfunc);  
},2000);

### 🔍 Explanation

- Executes **once** after 2 seconds
- Stops interval using `clearInterval()`

---

## 📌 setInterval()

const intfunc = setInterval(()=>{  
    console.log("This is an interval function");  
},1000)

### 🔍 Explanation

- Runs every **1 second**
- Continues until stopped

---

## 🔄 Flow of Execution

1. Interval starts → prints every 1 sec
2. After 2 sec → timeout runs
3. Timeout stops interval

---

# 📁 File & Directory Info

console.log(__dirname);  
console.log(__filename);

## 🔍 __dirname

- Gives current folder path

Example:

C:\Users\Ajay\project

---

## 🔍 __filename

- Gives full file path

Example:

C:\Users\Ajay\project\app.js

---

# ⚖️ Browser vs Node.js

|Feature|Browser|Node.js|
|---|---|---|
|Global Object|window|global|
|DOM Access|Yes|No|
|File Access|No|Yes|

---

# 🧠 Key Concepts

- `global` → global object in Node
- `async` → returns Promise
- `setTimeout` → runs once
- `setInterval` → runs repeatedly
- `__dirname` → folder path
- `__filename` → file path

---

# ⚠️ Notes

- `console.log` alone does nothing (needs parentheses)
- Order matters:
    - Define `intfunc` before clearing it (else error in strict cases)

---

# 🚀 One-Line Summary

👉 **Node.js provides global objects, async behavior, and timers to handle non-blocking operations efficiently**

---const http = require('http')

const fs = require('fs')

const _ = require('lodash')

const server = http.createServer((req,res)=>{

     console.log("Request Made")

    console.log(req.url)

    console.log(req.method)

  

    let path = './docs'

    if(req.url === '/about'){

        res.statusCode =200

        path+= '/about.html'

        res.setHeader('Content-Type','text/html');

        fs.readFile(path,(err,data)=>{

            if(err){

                console.log(err.message)

            }else{

                res.write(data)

                res.end()

            }

        })

    }else if(req.url === '/'){

        consoli

        res.statusCode = 302

        res.setHeader('Location','/contact')

        res.end()

    }

    else if(req.url === '/contact'){

        res.statusCode = 200

        res.setHeader('Content-Type','text/html')

        fs.readFile('./docs/contact.html',(err,data)=>{

            if(err){

                console.log(err.message)

            }else{

                res.end(data)

            }

        })

    }else{

       res.statusCode = 404

  

       res.setHeader('Content-Type','text/html')

  

       fs.readFile('./docs/notfound.html',(err,data)=>{

        if(err){

            console.log(err.message)

            res.end()

        }else{

res.write(data)

res.end()

        }

       })

    }

  

    // res.setHeader('Content-Type','text/html');

    // fs.readFile('./index.html', (err,data)=>{

    //     if(err){

    //         console.log(err.message)

    //         res.end()

    //     }else{

    //         // res.write(data);

    //         res.end(data);

    //     }

    // })

    // res.end()

});

  

server.listen(3000, 'localhost',()=>{

    console.log("Server is listening")

    console.log(_.random(15,25))

})