## 📌 Import FS Module

const fs = require('fs')

### 🔍 Explanation

- `fs` → Built-in module in Node.js
- Used for:
    - File handling
    - Folder operations

---

# 📁 Creating a Folder (mkdir)

if(!fs.existsSync("./docs")){  
    fs.mkdir('./docs', (err)=>{  
        if(err){  
            console.log(err.message);  
        }else{  
            console.log("Folder created")  
        }  
    })  
}else{  
    console.log("Folder not creted")  
}

## 🔍 Explanation

- `existsSync()` → checks if folder exists
- `mkdir()` → creates folder

### ⚠️ Note

- Prevents error by checking existence before creating

---

# 📝 Writing a File

fs.writeFile('./docs/newfile.txt', 'Hi my name is aj', (err)=>{  
    if(err){  
        console.log(err.message);  
    }else{  
        console.log('File written')   
    }  
});

## 🔍 Explanation

- Creates file if not exists
- Overwrites if file already exists

---

# 📖 Reading a File

if(fs.existsSync('./docs/newfile.txt')){  
    fs.readFile('./docs/newfile.txt',(err,data)=>{  
        if(err){  
            console.log(err.message)  
        }else{  
            console.log(data.toString())  
        }  
    })  
}else{  
    console.log("Not exists")  
}

## 🔍 Explanation

- Reads file content as **Buffer**
- `toString()` → converts buffer to readable text

---

# ❌ Deleting a File (unlink)

if(fs.existsSync('./docs/newfile.txt')){  
    fs.unlink('./docs/newfile.txt',(err)=>{  
        if(err){  
            console.log(err.message);  
        }else{  
            console.log('file deleted')  
        }  
    });  
}

## 🔍 Explanation

- Deletes file
- Requires file to exist

---

# 🗑️ Removing Folder (rmdir)

fs.rmdir('./docs',(err)=>{  
    if(err){  
        console.log(err.message)  
    }else{  
        console.log('folder deleted')  
    }  
})

## 🔍 Explanation

- Deletes folder
- Folder must be **empty**

---

# ⚖️ Sync vs Async

|Method|Type|Behavior|
|---|---|---|
|existsSync()|Sync|Blocks execution|
|mkdir()|Async|Non-blocking|
|writeFile()|Async|Non-blocking|
|readFile()|Async|Non-blocking|
|unlink()|Async|Non-blocking|
|rmdir()|Async|Non-blocking|

---

# 🧠 Key Concepts

- Always check existence before operations
- Async methods prevent blocking
- Buffer → needs conversion using `.toString()`
- Folder must be empty before deleting

---

# ⚠️ Important Notes

- `writeFile()` overwrites existing data
- `rmdir()` works only on empty folders
- Use proper error handling (`err.message`)

---

# 🚀 Execution Flow

1. Check if `docs` folder exists
2. Create folder if not exists
3. Create & write file
4. Read file
5. Delete file
6. Delete folder

---

# 🧠 One-Line Summary

👉 **FS module allows Node.js to create, read, update, and delete files & folders efficiently**