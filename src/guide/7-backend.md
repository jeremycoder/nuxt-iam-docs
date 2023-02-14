# Backend

The bulk of Nuxt IAM's login exists in the backend. Here's an explanation of the APIs.

## Database

Nuxt IAM requires a database to function correctly.

## Server

Nuxt IAM adds the following directories to your **server/api** directory.

- **iam/authn**: global authentication handler
- **iam/refresh-tokens**: refresh tokens handler
- **iam/users**: global users handler

## Authentication API

The authentication API deals with authentication logic.

### Authentication API Endpoints

Nuxt IAM responds to the following endpoints sent to **api/iam/authn**. See api request and response examples for examples.

- **iam/authn/register**: Register user
- **iam/authn/login**: Log user in
- **iam/authn/profile**: Get user profile
- **iam/authn/update**: Update user profile
- **iam/authn/reset**: Reset user password
- Continue...

The following are API routes that Nuxt IAM adds to your app.

#### API Responses

API responses should always be in the format below

```
"status": ["success"] | ["fail"],
 "data": {},
 "error" {},
```

`status` is always sent. `data` may or may not be sent depending on the request. `error` is only sent if an error occurred.

##### Success

Here's an example of a successful API response when a user is successfully registered:

```
"status": "success",
   "data": {
       "email": "jeremy@example.com"
   }
```

Here's an example of an error occuring when we try to register a user who already exists. Email must be unique throughout the system.

##### Fail

```
"status": "fail",
  "error": {
      "message": "Email already exists",
      "statusCode": 409,
      "statusMessage": "Email already exists"
  }
```

#### Register user

To register a user, send a POST request to `/api/iam/authn/register`.

##### Request

```
const response = await $fetch("/api/iam/authn/register", {
    method: "POST",
    headers: {
      "client-platform": ['app']|['browser']|['browser-dev'],
    },
    body: {
      first_name: 'Jeremy',
      last_name: 'Mwangelwa',
      email: 'jeremy@example.com',
      password: 'MyExamplePassword123*',
    },
  });
```

##### Response

```
"status": "success",
    "data": {
        "email": "jeremy@example.com"
    }
```

If the response status was `success`, then the user was successfully registered and added to the database. A registered user can now be logged in.

#### Login user

To login, send a POST request to `/api/iam/authn/login`.

##### Request

```
const response = await $fetch("/api/iam/authn/login", {
    method: "POST",
    headers: {
      "client-platform": ['app']|['browser']|['browser-dev'],
    },
    body: {
      email: 'jeremy@example.com',
      password: 'MyExamplePassword123*',
    },
  });
```

##### Response

###### Body

```
"status": "success",
    "data": {
        "email": "jeremy@example.com"
    }
```

###### Headers

```
access-token: Bearer eyJhbGciOiJIUzI1NiIs...0g3IYFA

refresh-token: Bearer eyJhbGciOiJIUzI1...xIMUybnk
```

In a successful login, an access token and a refresh token will be sent. If your `client platform` is `app`, the tokens will be sent in the headers. If your `client platform` is `browser`, the tokens will be sent in secure, httpOnly cookies. If your `client platform` is `browser-dev`, the tokens will be sent in unsecure cookies.

**Only use browser-dev in development**

## Users API

The users API deals with the REST (Represetational State Transfer) of users.

## Refresh Tokens API

The refresh tokens API deals with refreshing JWT tokens.
