Perfect 🔥 — here’s your **ready-to-paste `README.md`** file for your GitHub repo:

It’s clean, professional, and follows the exact **Kalvium submission format**.
You can just copy-paste this into your project’s root folder (`README.md`).

---

# 🧩 **User Registration Planning – Mageshwaran Thulasiraman**

## 📘 Overview

This project documents the planning and logic design for a **secure User Registration backend** using **Node.js**, **Express**, and **Mongoose**.
The goal is to conceptually plan how to register users safely, validate their data, hash passwords, and test API endpoints using **Bruno**.

This is a **planning and documentation assignment**, not a full implementation.

---

## 🧱 1. **Schema Design**

Each user in the system will have the following fields:

| Field      | Type   | Required | Validation                      | Description          |
| ---------- | ------ | -------- | ------------------------------- | -------------------- |
| `name`     | String | ✅ Yes    | Minimum 2 characters            | User’s name          |
| `email`    | String | ✅ Yes    | Must be a valid format & unique | Used for login       |
| `password` | String | ✅ Yes    | Minimum 6 characters            | Stored after hashing |

**Conceptual Example:**

```js
const userSchema = new mongoose.Schema({
  name: { type: String, required: true, minlength: 2 },
  email: { type: String, required: true, unique: true, match: /^[^\s@]+@[^\s@]+\.[^\s@]+$/ },
  password: { type: String, required: true, minlength: 6 }
});
```

---

## 🔐 2. **Password Hashing Plan**

Passwords are hashed using **bcrypt** before saving to the database.

**Hashing Steps:**

```
Receive input → Validate fields
↓
If valid → bcrypt.hash(password, 10)
↓
Save user with hashed password
↓
Return success message
```

**Pre-save Hook Example:**

```js
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});
```

---

## 🌐 3. **POST `/register` API Flow**

### Step-by-Step Process

1. Receive `{ name, email, password }`
2. Validate fields
3. Check if email already exists
4. Hash password with bcrypt
5. Save user to DB
6. Return `201 Created` response
7. Handle validation and server errors properly

**Example Pseudocode:**

```js
app.post("/register", async (req, res) => {
  const { name, email, password } = req.body;
  if (!name || !email || !password)
    return res.status(400).json({ message: "All fields are required" });

  const existing = await User.findOne({ email });
  if (existing)
    return res.status(400).json({ message: "Email already exists" });

  const hashedPassword = await bcrypt.hash(password, 10);
  const newUser = await User.create({ name, email, password: hashedPassword });

  res.status(201).json({ message: "User registered successfully", userId: newUser._id });
});
```

---

## 🧪 4. **Bruno Test Cases**

Bruno is used to test various scenarios for `/register`.

| Test Case            | Input                                                                       | Expected Output |
| -------------------- | --------------------------------------------------------------------------- | --------------- |
| ✅ Valid Registration | `{ "name": "Aarav", "email": "aarav@email.com", "password": "securepass" }` | 201 Created     |
| ❌ Missing Fields     | `{ "email": "test@email.com" }`                                             | 400 Bad Request |
| ❌ Invalid Email      | `{ "name": "Test", "email": "invalidemail", "password": "securepass" }`     | 400 Bad Request |
| ❌ Short Password     | `{ "name": "Test", "email": "short@email.com", "password": "123" }`         | 400 Bad Request |
| ❌ Duplicate Email    | Same email again                                                            | 400 Bad Request |

**Hash Check:**
After valid registration, check DB — password should start with `$2b$10$` (bcrypt hash).

---

## 📁 5. **Folder Structure**

```
user_registration_mageshwaran/
├── app.js
├── models/
│   └── userModel.js
├── routes/
│   └── userRoutes.js
├── docs/
│   └── assignment-plan.md
├── bruno/
│   └── user_registration.bru
└── README.md
```

---

## 🧠 6. **Security & Validation Notes**

* ❌ Never store plain-text passwords
* ✅ Always validate user input
* ✅ Use proper status codes (`400`, `201`, `500`)
* ✅ Use `unique: true` for email field
* ✅ Never include password field in API responses
