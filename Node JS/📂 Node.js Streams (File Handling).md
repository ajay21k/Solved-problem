## 📌 Required Modules

const fs = require('fs');  
const { encode } = require('punycode');

### 🔍 Explanation

- `fs` → File System module (built-in)
- `punycode` → Used for encoding (⚠️ not used in this code)

---

# 📖 Read Stream

const readStream = fs.createReadStream('./docs/HugeFile.txt', { encoding: 'utf8' })

## 🔍 What it does

- Reads a file **chunk by chunk** instead of loading entire file into memory

## ⚡ Why Streams?

- Efficient for **large files**
- Prevents **memory overflow**

---

# ✍️ Write Stream

const writeStream = fs.createWriteStream('./docs/copyHugeFile1.txt')

## 🔍 What it does

- Creates a writable stream
- Writes data into a new file

---

# 🔄 Manual Streaming (Commented Code)

readStream.on('data',(buffer)=>{  
    writeStream.write("\n New Buffer \n")  
    writeStream.write(buffer)  
})

## 🔍 Explanation

- `data` event → triggered when a chunk is read
- `buffer` → small part of file data

### Flow:

1. Read chunk
2. Write chunk
3. Repeat until file ends

💡 Adds:

New Buffer

before each chunk

---

# 🔗 Pipe Method (Used in Code)

readStream.pipe(writeStream);

## 🔍 What is pipe()?

- Directly connects:

Read Stream → Write Stream

## ✅ Advantages

- Cleaner code
- Automatic data flow
- Faster & efficient
- No need to manually handle events

---

# ⚖️ Manual vs Pipe

|Feature|Manual (on data)|pipe()|
|---|---|---|
|Code length|Longer|Short|
|Control|More|Less|
|Performance|Good|Better|
|Simplicity|Medium|Easy|

---

# 🧠 Key Concepts

- Streams handle **large data efficiently**
- Data is processed in **chunks (buffers)**
- `pipe()` is the **best practice** for copying files

---

# 🚀 Real-world Use Cases

- Copying large files
- Video/audio streaming
- File uploads/downloads
- Data processing pipelines

---

# ⚠️ Notes

- Ensure file path exists:
    - `./docs/HugeFile.txt`
- Output file:
    - `copyHugeFile1.txt` will be created automatically
- `punycode` import is **unused** → can be removed

---

# 🧠 One-Line Summary

👉 **Streams = process data piece-by-piece instead of loading everything at once**

---