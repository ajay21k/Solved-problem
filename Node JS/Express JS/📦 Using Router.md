import { Router } from "express";  
  
const router = Router();  
  
router.get('/', (req, res) => {  
    res.send("Hello");  
});  
  
export default router;

---

## 🔗 Mounting Router

app.use('/api/users', userRouter);

---

## 🧠 Key Points

- Keeps code modular
- Improves scalability

  
---  
  
# 📄 3. Request Params  
  
```markdown  
# Request Params  
  
## 📌 What are Params?  
Params are values in URL path.  
  
Example:

/api/users/1

  
---  
  
## 🧩 Syntax  
  
```js  
app.get('/api/users/:id', (req, res) => {  
    const id = req.params.id;  
});

---

## ✅ Example

app.get('/api/users/:id', (req, res) => {  
    const user = users.find(u => u.id === parseInt(req.params.id));  
    res.send(user);  
});

---

## 🧠 Use Case

- Fetch specific resource

  
---  
  
# 📄 4. Query Parameters & Filtering  
  
```markdown  
# Query Parameters  
  
## 📌 What is Query?  
Used to filter/search data.  
  
Example:

/api/products?filter=product_name&value=lap

  
---  
  
## 🧩 Syntax  
  
```js  
const { filter, value } = req.query;

---

## ✅ Example

app.get('/api/products', (req, res) => {  
    const { filter, value } = req.query;  
  
    if (filter && value) {  
        return res.send(  
            products.filter(p =>  
                p[filter]?.toLowerCase().includes(value.toLowerCase())  
            )  
        );  
    }  
  
    res.send(products);  
});

---

## 🧠 Use Case

- Search
- Filtering

  
---  
  
# 📄 5. HTTP Methods (CRUD)  
  
```markdown  
# HTTP Methods (CRUD)  
  
## 📌 CRUD Operations  
  
| Method | Purpose |  
|--------|--------|  
| GET    | Read   |  
| POST   | Create |  
| PUT    | Update (full) |  
| PATCH  | Update (partial) |  
| DELETE | Remove |  
  
---  
  
## ✅ Examples  
  
### GET  
```js  
app.get('/users', (req, res) => {});

### POST

app.post('/users', (req, res) => {});

### PUT

app.put('/users/:id', (req, res) => {});

### PATCH

app.patch('/users/:id', (req, res) => {});

### DELETE

app.delete('/users/:id', (req, res) => {});

---

## 🧠 Key Difference

- PUT → replaces entire object
- PATCH → updates only some fields

  
---  
  
# 📄 6. Middleware  
  
```markdown  
# Middleware  
  
## 📌 What is Middleware?  
Functions that run before route handlers.  
  
---  
  
## 🧩 Syntax  
  
```js  
const middleware = (req, res, next) => {  
    next();  
};

---

## ✅ Example

export const getParamsId = (req, res, next) => {  
    const id = parseInt(req.params.id);  
  
    if (isNaN(id)) {  
        return res.status(400).send({ msg: "Invalid ID" });  
    }  
  
    req.id = id;  
    next();  
};

---

## 🧠 Use Cases

- Validation
- Authentication
- Logging

  
---  
  
# 📄 7. Data Validation (express-validator)  
  
```markdown  
# Validation (express-validator)  
  
## 📌 Why Validation?  
Ensures correct data before processing.  
  
---  
  
## ⚙️ Installation  
  
```bash  
npm install express-validator

---

## 🧩 Schema Example

export const createUserValidationSchema = {  
    user_name: {  
        notEmpty: { errorMessage: "Required" },  
        isLength: {  
            options: { min: 3, max: 12 },  
            errorMessage: "Length invalid"  
        }  
    }  
};

---

## ✅ Usage

router.post(  
    '/',  
    checkSchema(schema),  
    (req, res) => {  
        const errors = validationResult(req);  
  
        if (!errors.isEmpty()) {  
            return res.status(400).send(errors.array());  
        }  
  
        const data = matchedData(req);  
        res.send(data);  
    }  
);

---

## 🧠 Key Functions

- `checkSchema()`
- `validationResult()`
- `matchedData()`

  
---  
  
# 📄 8. PUT vs PATCH  
  
```markdown  
# PUT vs PATCH  
  
## 📌 PUT (Full Update)  
  
```js  
users[index] = { id, ...body };

👉 Replaces entire object

---

## 📌 PATCH (Partial Update)

users[index] = {  
    ...users[index],  
    ...body  
};

👉 Updates only given fields

---

## 🧠 Example

Before:

{ name: "Ajay", age: 20 }

PATCH:

{ age: 21 }

After:

{ name: "Ajay", age: 21 }

  
---  
  
# 📄 9. Project Structure (Best Practice)  
  
```markdown  
# Project Structure  
  
## 📁 Recommended Structure  
  
src/  
 ├── index.mjs  
 ├── routes/  
 ├── controllers/  
 ├── middleware/  
 ├── utils/  
 ├── data/  
  
---  
  
## 📌 Purpose  
  
| Folder | Use |  
|-------|----|  
| routes | endpoints |  
| controllers | logic |  
| middleware | reusable functions |  
| utils | helpers |  
| data | static data |  
  
---  
  
## 🧠 Why Important?  
- Clean code  
- Scalable  
- Industry standard

---

# 📄 10. Common Errors & Debugging

# Common Errors  
  
## ❌ Module Not Found  
👉 Wrong path or missing `.mjs`  
  
## ❌ No Export Found  
👉 Forgot `export`  
  
## ❌ Hanging Request  
👉 No `res.send()`  
  
## ❌ Wrong Path  
👉 `./` vs `../`  
  
---  
  
## 🧠 Debug Tips  
  
- Check file name EXACTLY  
- Use console.log  
- Verify imports

---

# 🎯 FINAL NOTE

You’ve now learned:

- Express basics
- Routing
- Middleware
- Validation
- CRUD operations

👉 This is **solid backend foundation**