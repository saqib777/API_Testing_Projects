# ReqRes API Collection - Detailed Documentation

This document explains every part of the `ReqRes_API_Testing` Postman collection you asked for. It is written to be approachable for beginners and thorough enough for interview preparation or handoff to another QA engineer.

---

## Table of contents

1. Overview
2. Goals of this collection
3. How to import the collection into Postman
4. Environment variables (sample)
5. Collection structure and item-by-item explanation

   * GET List Users
   * POST Create User
6. Test scripts explained (what each assertion does and why)
7. Extending the collection (chaining, negative tests, schema validation)
8. Pre-request scripts and when to use them
9. Running the collection with Newman (commands + report generation)
10. CI ideas: run collection in GitHub Actions
11. Troubleshooting common errors
12. Best practices and checklist
13. Appendix: sample environment JSON and sample Newman shell script

---

## 1) Overview

`ReqRes_API_Testing` is a Postman collection that targets the public ReqRes API ([https://reqres.in](https://reqres.in)). The collection demonstrates core API testing concepts:

* CRUD operations (Create, Read, Update, Delete)
* Response assertions (status codes, response body fields)
* Simple JSON schema validation
* Use of environment variables
* Running collections via Newman for automation

This collection is intentionally small but structured so you can expand it with more tests and workflows.

---

## 2) Goals of this collection

* Teach how to structure Postman collections for clarity and reusability.
* Demonstrate common test assertions and why they matter.
* Provide runnable examples that can be executed manually in Postman or automated using Newman.
* Act as a base for more advanced API testing patterns (token handling, data-driven tests, CI integration).

---

## 3) How to import the collection into Postman

1. Save the JSON file named `ReqRes_API_Testing.postman_collection.json` to your machine (it's in `Postman_Collections/`).
2. Open Postman.
3. Click **Import** (top-left), then **Upload Files** and select the JSON file.
4. The imported collection appears in the Collections pane. You can expand it and run items individually.

Tip: also import the environment file `dev.postman_environment.json` so `{{baseUrl}}` resolves correctly.

---

## 4) Environment variables (sample)

Use a Postman environment to store values that change between runs (dev/staging/prod). For this collection, the important variable is:

* `baseUrl` - the base URL of the API.

**Sample `dev` environment JSON (key fields):**

```json
{
  "name": "Dev",
  "values": [
    {"key": "baseUrl", "value": "https://reqres.in", "enabled": true}
  ]
}
```

Why use environment variables?

* Avoid hard-coded URLs in requests.
* Keep sensitive values (tokens) out of the request bodies or headers.
* Easily switch between environments.

---

## 5) Collection structure and item-by-item explanation

The collection includes two starter requests: `GET List Users` and `POST Create User`. Each request contains a request definition and a `test` script that runs after the response is received.

### a) GET List Users

**Request details**

* Method: `GET`
* URL: `{{baseUrl}}/api/users?page=2`
* Purpose: validate that the API returns user list data and correct status code.

**Why this test is useful**

* Basic read operation. Ensures endpoint is reachable and returns expected structure.
* Used as a smoke test for the API.

**Postman Tests (what they do)**

* `Status code is 200` - checks the HTTP status is 200 OK.
* `Response has data field` - ensures the response JSON contains a top-level `data` field (common pattern for paginated responses).

**Notes**

* If the API pagination changes, adjust the `page` parameter or add a dynamic test to validate the `page` field in the response.

---

### b) POST Create User

**Request details**

* Method: `POST`
* URL: `{{baseUrl}}/api/users`
* Headers: `Content-Type: application/json`
* Body (raw JSON):

```json
{
  "name": "saqib",
  "job": "QA Engineer"
}
```

**Purpose**

* Demonstrates a create workflow and validates that creation returns the expected HTTP status and a generated `id`.

**Postman Tests (what they do)**

* `Status code is 201` - checks the API returned `201 Created`.
* `Created user contains id` - verifies the response JSON includes an `id` field, confirming the resource was created (even if the API is a mock and does not persist data).

**Notes**

* In real-world APIs, also assert that the returned `name` and `job` match the provided request payload (unless intentionally different).

---

## 6) Test scripts explained (what each assertion does and why)

We write Postman JS tests in the `Tests` tab. These are small JavaScript snippets that use Postman’s assertion library.

**Common assertions and why they matter**

* `pm.response.to.have.status(code)`

  * Verifies the HTTP status. Status codes are the first signal of correctness (200, 201, 400, 401, 404, 500, etc.).

* `pm.response.json()`

  * Parses response body into JSON for further checks.

* `pm.expect(json).to.have.property('id')`

  * Confirms the presence of a field. Useful for create operations where server generates identifiers.

* Schema validation (example shown later)

  * Ensures the response follows the expected structure, not just presence of fields.

**Example explained**

```javascript
pm.test('Status code is 201', function () {
    pm.response.to.have.status(201);
});
```

* This test fails if the API does not return 201. A failing test is an immediate red flag for the API behavior.

---

## 7) Extending the collection

Below are recommended extensions and examples with explanations.

### a) Add validations for returned fields

After `POST Create User`, add a test to check `name` and `job` match the request:

```javascript
pm.test('Response contains name and job', function () {
  var json = pm.response.json();
  pm.expect(json.name).to.eql('saqib');
  pm.expect(json.job).to.eql('QA Engineer');
});
```

Why: Makes sure the API returns data consistent with the request.

### b) Add negative tests

Examples:

* POST with missing `name` field - expect `400 Bad Request` or a well-formed error.
* GET with invalid page number - expect `200` with empty `data` or `400` depending on API design.

Pattern:

1. Create a request with invalid input.
2. Assert expected status code and error shape.

### c) Schema validation

Example JSON schema test (simple):

```javascript
pm.test('Schema is valid', () => {
    pm.response.to.have.jsonSchema({
        type: 'object',
        required: ['name','job','id','createdAt'],
        properties: {
            name: { type: 'string' },
            job: { type: 'string' },
            id: { type: 'string' },
            createdAt: { type: 'string' }
        }
    });
});
```

Why: Instead of checking each field separately, schema validation verifies entire structure and types.

### d) Chaining requests (use data from one response in another)

Use `pm.environment.set("userId", json.id)` in the POST response test to capture the `id`.
Then a subsequent request (GET /api/users/{{userId}}) can use that variable.

Example in POST `Tests` tab:

```javascript
var json = pm.response.json();
pm.environment.set('createdUserId', json.id);
```

Then later use `{{createdUserId}}` in another request URL.

Why: Chaining validates end-to-end workflows (create → retrieve → update → delete).

---

## 8) Pre-request scripts and when to use them

Pre-request scripts run before a request is sent. Use them to:

* Compute dynamic values (timestamps, nonces).
* Generate auth tokens or signatures.
* Set or transform environment variables.

Example: If the API requires a timestamp in the header:

```javascript
pm.environment.set('now', new Date().toISOString());
```

Then set header `X-Timestamp: {{now}}`.

---

## 9) Running the collection with Newman

Install Newman (Node.js required):

```
npm install -g newman newman-reporter-htmlextra
```

### Basic run

```
newman run Postman_Collections/ReqRes_API_Testing.postman_collection.json \
  -e Postman_Collections/Environments/dev.postman_environment.json
```

### Save CLI report

```
newman run Postman_Collections/ReqRes_API_Testing.postman_collection.json \
  -e Postman_Collections/Environments/dev.postman_environment.json \
  --reporters cli,json --reporter-json-export Newman_Reports/CLI_Reports/reqres.json
```

### Generate HTML report (htmlextra)

```
newman run Postman_Collections/ReqRes_API_Testing.postman_collection.json \
  -e Postman_Collections/Environments/dev.postman_environment.json \
  -r htmlextra --reporter-htmlextra-export Newman_Reports/HTML_Report/reqres.html
```

**Interpreting results**

* Newman exit code: `0` indicates all tests passed. Non-zero indicates failure.
* The HTML report shows a clear breakdown of passed/failed tests, request timings, and response bodies.

---

## 10) CI ideas: run collection in GitHub Actions

Add a `.github/workflows/api-tests.yml` that installs Node, installs Newman, and runs the collection on PRs or pushes to `main`.

Sample snippet:

```yaml
name: Run API Tests
on: [push]
jobs:
  newman:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Setup Node
      uses: actions/setup-node@v4
      with:
        node-version: '18'
    - name: Install Newman
      run: npm install -g newman newman-reporter-htmlextra
    - name: Run Collection
      run: |
        newman run Postman_Collections/ReqRes_API_Testing.postman_collection.json \
          -e Postman_Collections/Environments/dev.postman_environment.json \
          -r htmlextra --reporter-htmlextra-export Newman_Reports/HTML_Report/reqres-${{ github.run_id }}.html
    - name: Upload Report
      uses: actions/upload-artifact@v4
      with:
        name: api-test-report
        path: Newman_Reports/HTML_Report/reqres-${{ github.run_id }}.html
```

Why: This makes tests run automatically on code changes and provides artifact reports for reviewers.

---

## 11) Troubleshooting common errors

* **404 Not Found**: Check `{{baseUrl}}` value and endpoint path. Ensure environment is selected in Postman.
* **401 Unauthorized**: If you added auth, verify token generation and header placement.
* **Schema validation fails**: The response shape changed; update the expected schema or add defensive tests.
* **Newman cannot find collection**: Use correct file path relative to working directory. If running in CI, ensure the repo file exists in the runner workspace.

---

## 12) Best practices and checklist

Before marking a test as complete, verify:

* [ ] Use environment variables for all URLs and tokens.
* [ ] Tests assert both status codes and meaningful response fields.
* [ ] Negative tests are present (invalid inputs, auth failures).
* [ ] Chaining works end-to-end for critical flows.
* [ ] Test code (Postman scripts) is readable and commented where needed.
* [ ] Newman reports are generated in CI and stored as artifacts.
* [ ] No sensitive tokens are committed to the repo.

---

## 13) Appendix: Sample environment JSON and Newman script

**Sample `dev.postman_environment.json`**

```json
{
  "id": "dev-env",
  "name": "Dev",
  "values": [
    {"key": "baseUrl", "value": "https://reqres.in", "enabled": true}
  ]
}
```

**Sample `run_reqres.sh`**

```bash
#!/bin/bash
set -e
newman run Postman_Collections/ReqRes_API_Testing.postman_collection.json \
  -e Postman_Collections/Environments/dev.postman_environment.json \
  -r htmlextra --reporter-htmlextra-export Newman_Reports/HTML_Report/reqres.html
```


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






