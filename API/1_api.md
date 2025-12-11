# ✅ **What is an API?**

**API** stands for **Application Programming Interface**.

Think of an API like a **waiter in a restaurant**:

* You (**the client**) tell the waiter what you want.
* The waiter (**API**) takes your request to the kitchen (**server/system**).
* The kitchen prepares the food and gives it to the waiter.
* The waiter brings the result back to you.

### In software terms:

* The **client** (mobile app, website, program) sends a request.
* The **API** delivers the request to the **server**.
* The **server** processes it (e.g., get data from a database).
* The **API** sends the **response** back to the client.

### APIs allow:

* Two systems to communicate
* Apps to share data
* Developers to use functionalities without knowing the internal code

**Example:**
When you log in to Instagram, the app calls Instagram’s **Login API** to verify your username & password.

---

# ✅ **What is a REST API?**

**REST** stands for **Representational State Transfer**.
It is a style of building APIs that follow certain rules.

A **REST API** is an API that uses **HTTP** (same protocol as web browsers) to perform operations on data.

### REST APIs use the following HTTP methods:

| Method     | Meaning             | Example             |
| ---------- | ------------------- | ------------------- |
| **GET**    | Fetch data          | Get all users       |
| **POST**   | Create data         | Create new user     |
| **PUT**    | Update data (fully) | Update user details |
| **PATCH**  | Update partially    | Change user email   |
| **DELETE** | Delete data         | Remove a user       |

---

# 🧩 **Key Concepts of REST**

### 1️⃣ **Resource**

Everything is treated as a resource.
Examples: `/users`, `/products`, `/orders`

### 2️⃣ **Endpoint**

URL used to access a resource.
Example:

```
GET /users/123
```

This means “Get details of user with ID 123”.

### 3️⃣ **Stateless**

The server does **not** remember previous requests.
Every request must contain all required information.

### 4️⃣ **JSON Format**

REST APIs commonly send data as **JSON**:

```json
{
  "id": 1,
  "name": "Renga",
  "email": "renga@example.com"
}
```

---

# 🎯 Simple REST API Example

### **GET /products**

Returns all products.

### **POST /products**

Creates a new product:

```json
{
  "name": "Laptop",
  "price": 55000
}
```

### **DELETE /products/10**

Deletes product with ID 10.

---

# 🛠️ Why Are REST APIs Used?

* Easy to understand
* Lightweight
* Works with any language (Python, Java, JS, etc.)
* Compatible with web & mobile apps
* Scalable for big systems (Netflix, Google, Amazon use it)

---

# ⭐ Summary

| Topic        | Explanation                                         |
| ------------ | --------------------------------------------------- |
| **API**      | Communication bridge between software               |
| **REST API** | A type of API that uses HTTP and follows REST rules |
| **Uses**     | CRUD operations on data                             |
| **Format**   | Mostly JSON                                         |
| **Methods**  | GET, POST, PUT, PATCH, DELETE                       |

---

Here’s a clear, simple explanation of **API Contracts** — an important concept in API development.

---

# ⭐ **What Is an API Contract?**

An **API contract** is like an **agreement** between the **client** and the **server** that defines:

* What the API expects
* What the API returns
* How the API behaves

Think of it like a **legal contract** or a **blueprint**.
It tells everyone exactly **how to talk to the API and what to expect back**.

---

# 🧩 **Why is it called a “Contract”?**

Because **both sides must follow it**:

* The **client** must send requests in the correct format
* The **server** must respond exactly as documented

If either side breaks the contract → things will fail.

---

# 🧾 **What does an API Contract include?**

A good API contract describes:

### 1️⃣ **Endpoint URL**

Example:

```
/users/{id}
```

### 2️⃣ **HTTP Method**

* GET
* POST
* PUT
* DELETE

### 3️⃣ **Request Format**

What the client must send.

Example (POST /users):

```json
{
  "name": "Renga",
  "email": "renga@example.com"
}
```

### 4️⃣ **Response Format**

What the server returns.

Example:

```json
{
  "id": 10,
  "name": "Renga",
  "email": "renga@example.com"
}
```

### 5️⃣ **Status Codes**

* 200 OK
* 201 Created
* 400 Bad Request
* 404 Not Found
* 500 Server Error

### 6️⃣ **Validation Rules**

Example:

* “email must be valid”
* “name must not be empty”

### 7️⃣ **Authentication Rules**

* API keys
* JWT tokens
* OAuth

### 8️⃣ **Error Messages**

What the server returns when something goes wrong.

Example:

```json
{
  "error": "Invalid email format"
}
```

---

# 📘 Simple Example of an API Contract (Human-readable)

### **Endpoint:**

`POST /login`

### **Description:**

Authenticates a user and returns a JWT token.

### **Request Body:**

```json
{
  "username": "renga",
  "password": "secret123"
}
```

### **Response:**

```json
{
  "token": "abcd.efgh.ijkl"
}
```

### **Errors:**

* **401 Unauthorized**

```json
{
  "error": "Invalid credentials"
}
```

---

# 🎯 Why Are API Contracts Important?

### ✔ Prevents misunderstandings

Frontend & backend teams know exactly how communication works.

### ✔ Ensures compatibility

When updating APIs, you ensure nothing breaks accidentally.

### ✔ Helps API testing

Tools like Postman use contracts to automatically test APIs.

### ✔ Easier integration

Other teams or external clients can use your API easily.

### ✔ Enables automation

Contracts can generate:

* API documentation
* Client SDKs (Python, Java, JS)
* Server stubs

---

# 🛠 **How API Contracts Are Written?**

Most common standards:

### **1. OpenAPI / Swagger** (most popular)

Used in REST APIs
Example:

```yaml
paths:
  /users:
    get:
      summary: Get all users
```

### **2. gRPC / Protobuf Contracts**

For high-performance APIs.

### **3. GraphQL Schema**

For GraphQL APIs.

---

# 🔍 Summary

| Concept          | Meaning                                          |
| ---------------- | ------------------------------------------------ |
| **API Contract** | A formal agreement that defines how an API works |
| **Includes**     | Endpoints, request, response, validation, errors |
| **Purpose**      | Prevent confusion, allow smooth communication    |
| **Used by**      | Backend devs, frontend devs, QA, integrators     |

---