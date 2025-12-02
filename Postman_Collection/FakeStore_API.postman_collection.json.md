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
