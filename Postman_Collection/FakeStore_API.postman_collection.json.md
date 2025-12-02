# FakeStore API Collection — Detailed Documentation
```
{
  "info": {
    "name": "FakeStore_API_Testing",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json",
    "description": "Basic CRUD API testing collection for FakeStoreAPI (Option A)."
  },
  "item": [
    {
      "name": "GET All Products",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{baseUrl}}/products",
          "host": ["{{baseUrl}}"],
          "path": ["products"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status 200', () => pm.response.to.have.status(200));",
              "pm.test('Data is an array', () => pm.expect(pm.response.json()).to.be.an('array'));"
            ]
          }
        }
      ]
    },

    {
      "name": "GET Product By ID",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{baseUrl}}/products/1",
          "host": ["{{baseUrl}}"],
          "path": ["products", "1"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status 200', () => pm.response.to.have.status(200));",
              "pm.test('Product has id', () => pm.expect(pm.response.json()).to.have.property('id'));"
            ]
          }
        }
      ]
    },

    {
      "name": "POST Add Product",
      "request": {
        "method": "POST",
        "header": [
          {"key": "Content-Type", "value": "application/json"}
        ],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"title\": \"Test Product\",\n  \"price\": 109.95,\n  \"description\": \"A new test product\",\n  \"image\": \"https://i.pravatar.cc\",\n  \"category\": \"electronics\"\n}"
        },
        "url": {
          "raw": "{{baseUrl}}/products",
          "host": ["{{baseUrl}}"],
          "path": ["products"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status 200/201', () => pm.expect([200,201]).to.include(pm.response.code));",
              "pm.test('Product has id', () => pm.expect(pm.response.json()).to.have.property('id'));"
            ]
          }
        }
      ]
    },

    {
      "name": "PUT Update Product",
      "request": {
        "method": "PUT",
        "header": [
          {"key": "Content-Type", "value": "application/json"}
        ],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"title\": \"Updated Product\",\n  \"price\": 199.95,\n  \"description\": \"Updated description\",\n  \"image\": \"https://i.pravatar.cc/150?img=3\",\n  \"category\": \"electronics\"\n}"
        },
        "url": {
          "raw": "{{baseUrl}}/products/1",
          "host": ["{{baseUrl}}"],
          "path": ["products", "1"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status 200', () => pm.response.to.have.status(200));",
              "pm.test('Updated has title', () => pm.expect(pm.response.json()).to.have.property('title'));"
            ]
          }
        }
      ]
    },

    {
      "name": "DELETE Product",
      "request": {
        "method": "DELETE",
        "header": [],
        "url": {
          "raw": "{{baseUrl}}/products/1",
          "host": ["{{baseUrl}}"],
          "path": ["products", "1"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status 200/204/201', () => pm.expect([200,204,201]).to.include(pm.response.code));"
            ]
          }
        }
      ]
    }
  ]
}

```



This document explains the purpose, structure, usage, and testing flow of the **FakeStore_API_Testing (Option A)** Postman collection. It is written to help beginners understand how to approach API testing while still serving as professional-grade documentation for SDET portfolios.

---

## **1. Why This Collection Was Created**

The FakeStore API collection was created to:

### ** Build API testing skills on a realistic e‑commerce API**

FakeStoreAPI ([https://fakestoreapi.com/](https://fakestoreapi.com/)) simulates an online store. It exposes endpoints for:

* Products
* Categories
* Carts
* Users

This makes it perfect for learning how real systems behave.

### ** Provide a clean, beginner‑friendly starting point**

Option A focuses on *core CRUD testing* without overwhelming complexity.
It is simple enough to understand quickly, but still real enough to practice proper testing.

### ** Prepare for SDET interviews**

Almost every SDET interview includes:

* writing basic API tests
* explaining status code validation
* understanding CRUD
* discussing Postman + Newman usage

This collection gives you a ready-made example you can confidently walk interviewers through.

### ** Form the foundation for a more advanced version (Option B)**

Option B will include:

* schema validation
* chaining
* error cases
* data‑driven flows
* Newman CI pipelines

But we start small and clean.

---

## **2. What This Collection Covers**

Option A includes all the essential CRUD operations:

### **Endpoints included:**

1. **GET /products** – Fetch all products
2. **GET /products/1** – Fetch single product
3. **POST /products** – Create a product (FakeStore mock behavior)
4. **PUT /products/1** – Update an existing product
5. **DELETE /products/1** – Delete product (mock)

Each request includes:

* a valid URL
* required header/body fields
* basic Postman tests to validate the API response

This collection demonstrates:

* Calling public APIs
* Understanding resource formats
* Performing CRUD operations
* Writing simple assertions

---

## **3. Folder Structure Explanation**

```
FakeStore_API_Collection/
│
├── GET All Products
├── GET Product by ID
├── POST Add Product
├── PUT Update Product
└── DELETE Product
```

Each item in the collection represents one API action.

No folders/grouping yet — because Option A keeps the structure simple.
Advanced grouping will be part of **Option B**.

---

## **4. How to Use This Collection (Step‑by‑Step)**

### **1. Import the collection**

* Open Postman → Import → select `FakeStore_API.postman_collection.json`

### **2. Add environment**

Create an environment in Postman:

* Variable: `baseUrl`
* Value: `https://fakestoreapi.com`

### **3. Select the environment**

Top‑right corner → choose the environment.

### **4. Run each request manually**

Open a request → press **Send** → inspect response.

### **5. Check Test Results**

Click **Tests** tab in the response viewer.
You should see:

* green checks = passed tests
* red tests = something is wrong (good practice to debug)

---

## **5. What the Tests Validate (Detailed Breakdown)**

Each request contains basic but meaningful test scripts.

### **GET All Products**

Validates:

* status code = 200
* response body is an array

Purpose:

* confirm that server is reachable
* confirm that API returns a list of products

---

### **GET Product by ID**

Validates:

* status = 200
* response contains an "id" field

Purpose:

* ensure API supports fetching an individual product

---

### **POST Add Product**

Validates:

* status = 200 or 201 (FakeStore uses mock behavior)
* response contains an "id"

Purpose:

* confirm creation workflow
* ensure product payload is accepted by API

---

### **PUT Update Product**

Validates:

* status = 200
* response body contains "title" field

Purpose:

* verify update is successful
* ensure server returns updated values

---

### **DELETE Product**

Validates:

* status 200/201/204

Purpose:

* confirm request is accepted
* FakeStore does not delete data permanently (mock API), so expect flexible status range

---

## **6. What You Learn by Using This Collection**

Using Option A teaches you:

### **API Basics**

* What an endpoint is
* How HTTP verbs (GET/POST/PUT/DELETE) behave
* How request/response cycles work

### **Postman Usage**

* Running calls
* Writing simple tests
* Reading JSON responses

### **Testing Mindset**

* Check status codes
* Validate response fields
* Verify types (array, object)
* Basic error reasoning

These are the exact fundamentals every SDET needs before moving into:

* automation
* chaining
* schema validation
* CI integration

---

## **7. Future Expansion (What Option B will include)**

You will later be able to upgrade this to a **full professional-grade API test framework**, including:

###  Advanced assertions

###  Data‑driven tests

###  Full CRUD chaining

###  Negative tests

###  Schema validation

###  Pre-request token generation

###  Dynamic data

###  Beautiful Newman HTML reports

###  GitHub Actions CI pipeline

Option A is the foundation.
Option B becomes the full SDET portfolio project.

---

## **8. Who This Documentation Is For**

* **Beginners** learning API testing
* **SDET learners** building portfolio projects
* **Interview preparation** candidates
* **Recruiters** evaluating understanding
* **Anyone reviewing your GitHub** and wanting to see how well you explain technical work

---

## **9. Summary**

This collection was created to give you:

* A clear, simple starting point for API testing
* Real APIs to practice on
* Proper documentation for GitHub and interviews
* A professional foundation for advanced test automation

You now have:
 A working FakeStoreAPI collection
 Clean CRUD tests
A professional explanation of how and why it was built

When you're ready, we can build **Option B**, the advanced version.

