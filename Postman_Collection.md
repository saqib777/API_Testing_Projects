```
{
  "info": {
    "name": "ReqRes_API_Testing",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json",
    "description": "Postman collection for testing ReqRes.in API with CRUD operations, validations and negative scenarios."
  },
  "item": [
    {
      "name": "GET List Users",
      "request": {
        "method": "GET",
        "header": [],
        "url": {
          "raw": "{{baseUrl}}/api/users?page=2",
          "host": ["{{baseUrl}}"],
          "path": ["api", "users"],
          "query": [{"key": "page", "value": "2"}]
        }
      },
      "response": [],
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status code is 200', function () { pm.response.to.have.status(200); });",
              "pm.test('Response has data field', function () { var json = pm.response.json(); pm.expect(json).to.have.property('data'); });"
            ]
          }
        }
      ]
    },
    {
      "name": "POST Create User",
      "request": {
        "method": "POST",
        "header": [{"key": "Content-Type", "value": "application/json"}],
        "body": {
          "mode": "raw",
          "raw": "{\n  \"name\": \"saqib\",\n  \"job\": \"QA Engineer\"\n}"
        },
        "url": {
          "raw": "{{baseUrl}}/api/users",
          "host": ["{{baseUrl}}"],
          "path": ["api", "users"]
        }
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test('Status code is 201', function () { pm.response.to.have.status(201); });",
              "pm.test('Created user contains id', function () { var json = pm.response.json(); pm.expect(json).to.have.property('id'); });"
            ]
          }
        }
      ]
    }
  ]
}
```






