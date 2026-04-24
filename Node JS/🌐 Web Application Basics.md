## 📌 What is a Web App?

A **Web Application** is a software application that runs in a browser.

### 🔹 Components of Web App

#### 🎨 Frontend (Client-side)

- Built using:
    - **HTML** → Structure
    - **CSS** → Styling
    - **JavaScript** → Interactivity
- Runs in the browser

#### ⚙️ Backend (Server-side)

- Handles:
    - Data processing
    - Business logic
    - Database communication
- Runs on a server

---

# 🟢 Node.js

## 📌 What is Node.js?

**Node.js** is a runtime environment that allows JavaScript to run outside the browser.

- Built using:
    - **V8 Engine** (from Google)
    - **C++**

---

## ⚡ Features of Node.js

1. Can **read/write files**
2. Can **connect with database**
3. Can **act as a server**

⚠️ **Limitation:**

- Cannot control the **DOM** (since no browser environment)

---

## ✅ Advantages of Node.js

- Supports **3rd-party libraries (npm packages)**
- **Simpler than Spring Boot**
- Same language (**JavaScript**) for frontend & backend

---

# 🌍 Networking Basics

## 📌 IP Address

- Unique address of a device on a network
- Example:

193.168.1.1

---

## 📌 Domain Name

- Human-readable name for IP address
- Example: `google.com`

💡 We use domain because:

- IPs are hard to remember

💰 Domain cost:

- Paid yearly (e.g., ₹500–₹5000)

---

## 📌 DNS (Domain Name System)

- Maps:

Domain Name → IP Address

- Uses a lookup table

---

## 🖥️ Localhost

- Refers to your own computer
- Loopback IP:

127.0.0.1

💡 Used for:

- Running servers locally

---

## 🔌 Port Number

- Each application runs on a unique port

Example:

https://localhost:3000

- `localhost` → domain
- `3000` → port number

---

# 🚀 Node.js Server Example

const http = require('http')  
  
const server = http.createServer((req,res)=>{  
    console.log("Request Made")  
    console.log(req.url)  
    console.log(req.method)  
  
    res.setHeader('Content-Type','text/html');  
    res.write("<h1>This is my Node Response</h1>")  
    res.write('<h2>res</h2>')  
    res.end()  
});  
  
server.listen(3000, 'localhost',()=>{  
    console.log("Server is listening")  
})

## 🔍 Explanation

- `createServer()` → creates server
- `req` → request object
- `res` → response object
- `res.write()` → sends data
- `res.end()` → ends response
- `listen()` → starts server

---

# 📦 Modules in Node.js

## 📌 data.js

names = ["ajay","akash","vikash","praveen"];  
ages = [19,20,20,12]  
  
module.exports = {  
    summaName:names,  
    vayasu:ages  
}

---

## 📌 Importing Module

const names = require('./data');  
const {vayasu,summaName} = require("./data")  
  
console.log(vayasu)  
console.log(summaName)  
console.log(names.vayasu);

---

## 📌 Built-in Module Example

console.log(require('os'))

💡 `os` module gives system-level information

---

# 🧠 Quick Summary

- Frontend → UI (HTML, CSS, JS)
- Backend → Server logic
- Node.js → Runs JS outside browser
- DNS → Domain → IP mapping
- Localhost → Your own machine
- Port → App identifier
- Modules → Code reuse