# Backend

Nuxt IAM provides you with an extensive backend. Below is an explanation of files and API logic that deals with the backend.

## Database

Nuxt IAM requires a database to operate successfully, and uses [Prisma](http://www.prisma.io) as its object relation mapper (ORM). For database configuration, please see [Configuration](./6-configuration).

Prisma adds a **prisma/schema** file to your application. The schema file tells Prisma the structure of your database. For more information about your Prisma schema file, please see [Prisma schema](https://www.prisma.io/docs/concepts/components/prisma-schema)

The default Nuxt IAM Prisma schema should look similar to

```
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

model users {
  id             Int              @id @default(autoincrement())
  uuid           String           @unique(map: "uuid") @db.VarChar(60)
  email          String           @unique(map: "email") @db.VarChar(255)
  password       String           @db.VarChar(255)
  avatar         String?          @db.VarChar(1000)
  permissions    String?          @db.VarChar(4000)
  first_name     String           @db.VarChar(255)
  last_name      String           @db.VarChar(255)
  role           Role             @default(GENERAL)
  email_verified Boolean          @default(false)
  is_active      Boolean          @default(true)
  last_login     DateTime?        @db.DateTime(0)
  created_at     DateTime         @default(now()) @db.DateTime(0)
  deleted_at     DateTime?        @db.DateTime(0)
  refresh_tokens refresh_tokens[]
  sessions       sessions[]
  provider_users provider_users[]
}

model provider_users {
  id               Int      @id @default(autoincrement())
  provider         Provider
  provider_user_id String   @unique(map: "provider_user_id")
  user             users?   @relation(fields: [user_id], references: [id], onDelete: Cascade)
  user_id          Int
}

model sessions {
  id           Int       @id @default(autoincrement())
  user         users?    @relation(fields: [user_id], references: [id], onDelete: Cascade)
  user_id      Int
  sid          String    @unique(map: "sid")
  start_time   DateTime  @default(now())
  end_time     DateTime?
  access_token String    @db.VarChar(4000)
  csrf_token   String    @db.VarChar(255)
  is_active    Boolean
  ip_address   String
}

enum Role {
  SUPER_ADMIN
  ADMIN
  GENERAL
}

enum Provider {
  GOOGLE
}

model refresh_tokens {
  id           Int      @id @default(autoincrement())
  token_id     String   @unique(map: "token_id") @db.VarChar(60)
  user         users?   @relation(fields: [user_id], references: [id], onDelete: Cascade)
  user_id      Int
  is_active    Boolean
  date_created DateTime @default(now()) @db.DateTime(0)
}

model one_time_tokens {
  id           Int        @id @default(autoincrement())
  token_id     String     @unique(map: "token_id") @db.VarChar(60)
  token_type   tokenType?
  expires_at   DateTime   @db.DateTime(0)
  date_created DateTime   @default(now()) @db.DateTime(0)
}

enum tokenType {
  RESET
}

```

To add and modify tables, familiarize yourself with [Prisma](http://www.prisma.io).

## Server

Nuxt IAM adds the following directories to your **server/api** directory.

- **iam/authn**: global authentication handler
- **iam/refresh-tokens**: refresh tokens handler
- **iam/users**: global users handler

## API Requests and Responses

All API requests must have send the `client-platform ` header. The `client-platform` header tells Nuxt IAM what type of client is sending the request, and can therefore provide the best security for the client.

### Client platform

`client-platform` is a **required** header and it must be sent with every request. Client platform allows Nuxt IAM to provide the best practices for securing your app. `client-platform` must be:

- `app`: Use `app` if the request is coming from a non-browser such as a mobile app, tablet, or a tool like POSTMAN. Access and refresh tokens will be sent in the response headers. Can be used in **production**.
- `browser`: Use `browser` if the request is coming from a browser. Access and refresh tokens will be sent in **secure, httpOnly** cookies. Can be used in **production**.
- `browser-dev`: Use `browser-dev` if the request is coming from a browser in a development environment. Access and refresh tokens are sent in **unsecure** cookies. Use only in **development.**

If the `client-platform` header is not sent, the API will respond with an error. In some cases, if `client-platform` is not sent, it will default to `browser`, which assumes the request is being sent by a browser in production.

#### Client Platform: 'app'

If `client-platform` is app, you'll need to manage the access and refresh tokens that are sent. For example, when a user successfully logs in, Nuxt IAM will respond with an **access token** and a **refresh token** in the header. You'll need the access token in your next request if you'll be requesting restricted data such as the user's profile.

Access tokens only last **15 minutes**, so after the 15 minutes is up, you need to login again to get a new set of access and refresh tokens. Refresh tokens last **14 days.**

If your access token has expired, but your refresh token has not expired, you can send a POST request to `/api/iam/authn/refresh` with your expired access token and valid refresh token in the header, and you should get new tokens.

You must then use those tokens to access any restricted data.

### API responses

API responses should always be in the format below

```
{
  "status": ["success"] | ["fail"],
  "data": {},
  "error" {},
}

```

`status` is always sent. `data` is sometimes sent depending on the request. `error` is only sent if an error occurred.

#### Success

Here's an example of a successful API response when a user is successfully registers:

```
{
  "status": "success",
    "data": {
        "email": "jeremy@example.com"
    }
}
```

Here's an example of an error occuring when we try to register a user who already exists. Email must be unique throughout the system.

#### Fail

```
{
  "status": "fail",
  "error": {
      "message": "Email already exists",
      "statusCode": 409,
      "statusMessage": "Email already exists"
  }
}
```

## Authentication (authn) API

The authentication API handles all authentication logic.

### Authentication API Endpoints

Nuxt IAM adds the following authentication endpoints to your app. See examples below.

- **/api/iam/authn/profile**: GET request will get the authenticated user's profile
- **/api/iam/authn/isauthenticated**: GET request returns value determining whether user is authenticated (logged in) or not
- **/api/iam/authn/register**: POST request registers a user
- **/api/iam/authn/login**: POST request logs a user in
- **/api/iam/authn/login-google**: POST request logs a user in using Google
- **/api/iam/authn/refresh**: POST request gets a new pair of access and refresh tokens
- **/api/iam/authn/reset**: POST request resets a user's password
- **/api/iam/authn/verifyreset/[token]**: POST request sends password verification token
- **/api/iam/authn/verifyemailtoken**: POST request to for email verification
- **/api/iam/authn/logout**: POST request to log a user out
- **/api/iam/authn/update**: PUT request to update user profile
- **/api/iam/authn/delete**: DELETE request to delete user profile / account

### Authentication API Requests and Responses

The following section provides examples of authentication API requests and responses.

### Register user

To register a user, send a POST request to `/api/iam/authn/register`.

#### Request

Here's an example request to register a user. Remember, `client-platform` can be `app`, `browser`, or `browser-dev.` In the example below, `client-platform` is `app`, because this request is being sent from a non-browser application.

```
const response = await $fetch("/api/iam/authn/register", {
    method: "POST",
    headers: {
      "client-platform": 'app',
    },
    body: {
      first_name: 'Jeremy',
      last_name: 'Mwangelwa',
      email: 'jeremy@example.com',
      password: 'MyExamplePassword123*',
    },
  });
```

#### Response

Here's an example of a successful response.

```
{
  "status": "success",
    "data": {
        "email": "jeremy@example.com"
    }
}
```

If the response status was `success`, then the user was successfully registered and added to the database. A registered user can now be logged in.

### Login user

To login, send a POST request to `/api/iam/authn/login`.

#### Request

```
const response = await $fetch("/api/iam/authn/login", {
    method: "POST",
    headers: {
      "client-platform": 'app',
    },
    body: {
      email: 'jeremy@example.com',
      password: 'MyExamplePassword123*',
    },
  });
```

#### Response

##### Body

```
"status": "success",
    "data": {
        "email": "jeremy@example.com"
    }
```

##### Response after login

```
iam-access-token: Bearer eyJhbGciOiJIUzI1NiIs...0g3IYFA

iam-refresh-token: Bearer eyJhbGciOiJIUzI1...xIMUybnk
```

In a successful login, an access token and a refresh token will be sent. If your `client platform` is `app`, the tokens will be sent in the headers. If your `client platform` is `browser`, the tokens will be sent in **secure, httpOnly** cookies. If your `client platform` is `browser-dev`, the tokens will be sent in unsecure cookies.

**Only use browser-dev in development**

##### Client platform and tokens

If your client platform is `app` and you need to access a protected resource that requires authentication or you need to refresh your tokens, you'll need to send valid access and refresh tokens in your **request headers**. For example:

```
const response = await $fetch("/api/iam/authn/refresh", {
    method: "POST",
    headers: {
      "client-platform": "app",
      "iam-access-token": "Bearer eyJhbGciOiJI....UzI1NiIs",
      "iam-refresh-token": "Bearer eyJhbGcesTJI....UzI1NiIs",
    },
```

If your client platform is `browser` or `browser-dev,` access and refresh tokens are automatically sent by your browser in cookies. You don't have to concern yourself with them.

### Logout user

To logout, send a POST request to `/api/iam/authn/logout`.

#### Request

```
const response = await $fetch("/api/iam/authn/logout", {
    method: "POST",
    headers: {
      "client-platform": "app",
    },
  });
```

If your client platform is `browser` or `browser-dev`, Nuxt IAM will delete your access and refresh tokens and deactivate your refresh tokens in the database, and you will be immediately logged out of the system.
If your client platform is `app`, Nuxt IAM will deactivate your refresh tokens in the database. You will be logged out as soon as your access token expires.
Logging out immediately is not possible because JSON web tokens cannot be revoked once given. We cannot revoke access tokens once given. However, the access token expires in 15 minutes,
so 15 minutes is the maximum amount of time that a user on client platform `app` will be logged out but still be able to access resources.

### Get user profile

To get their profile/account, an authenticated user should send a GET request to `/api/iam/authn/profile`.

#### Request

```
const response = await $fetch("/api/iam/authn/profile", {
    headers: {
      "client-platform": 'browser',
    },
  });
```

In the example above, the GET request is implied.

### Login with Google

To login with Google, a user send a POST request to `/api/iam/authn/login-google`.

#### Request

```
const response = await $fetch("/api/iam/authn/login-google", {
    method: "POST",
    headers: {
      "client-platform": "browser",
    },
    body: {
      token: token,
    },
  });
```

The token is the access/id token you receive from Google after you authenticate with them.

### Check if user is authenticated/logged in

To check if the current user is authenticated, send a GET request to `/api/iam/authn/isauthenticated`.

#### Request

Below is an example function that checks if the user is logged in and returns true or false.

```
let isAuthenticated = false;

  // Api response always has status, data, or error
  const { status, error } = await $fetch("/api/iam/authn/isauthenticated", {
    headers: {
      "client-platform": clientPlatform,
    },
  });

  // If status is 'fail', not authenticated
  if (status === "fail") {
    if (error) console.log("error: ", error);
    isAuthenticated = false;
  }

  // If status is 'success', is authenticated
  if (status === "success") {
    isAuthenticated = true;
  }

  return isAuthenticated;
```

### Update user profile

To update their profile/account, an authenticated user must send a PUT request to `/api/iam/authn/update`.

#### CSRF (cross-site request forgery) protection tokens

A csrf token must be sent with any request that modifies data. Csrf tokens are sent in the body of data after a user is authenticated. For an example of proper usage, please see the [`iamDashboard`](./5-frontend.md) component, or the [`iam/dashboard/admin`](./5-frontend.md) page

#### Request

For your request to be successful, you must supply the user's `uuid`, `csrf_token`, and at least one of the updatable fields (first name, last name etc.) The uuid is **not** the id. Nuxt IAM uses both id and uuid for a user account and they are both unique. The uuid is displayed to the public, but the id is used internally most of the time.

If you're updating the password, you must supply both **current** and **new password** and the new password must follow the minimum password strength. **The password must contain at least 8 characters, an upper-case letter, and a lower-case letter, a number, and a non-alphanumeric character.**

Below is an example.

```
const response = await $fetch("/api/iam/authn/update", {
    method: "PUT",
    headers: {
      "client-platform": 'browser',
    },
    body: {
      uuid: values.uuid,
      first_name: values.firstName,
      last_name: values.lastName,
      csrf_token: values.csrfToken,
      current_password: values.currentPassword,
      new_password: values.newPassword,
    },
  });

```

### Refresh tokens

To refresh tokens, send a POST request to `/api/iam/authn/refresh`. Refreshing tokens will return a new access token and a new refresh token. All your other tokens will be invalidated.

For this to work, you'll need to have an active or expired access token, and an active refresh token. If both your tokens are expired, you will not be able to refresh your tokens.

#### Request

If your client platform is `app`, you'll need to send your tokens in the header as below.

```
const response = await $fetch("/api/iam/authn/refresh", {
    method: "POST",
    headers: {
      "client-platform": "app",
      "iam-access-token": "Bearer eyJhbGciOiJI....UzI1NiIs",
      "iam-refresh-token": "Bearer eyJhbGcesTJI....UzI1NiIs",
    },
  });
```

The tokens will return in the response headers.

If your client platform is `browser` or `browser-dev`, you'll need to send your tokens as cookies. If you use the Nuxt IAM frontend pages, you won't have to concern yourself with tokens.

```
const response = await $fetch("/api/iam/authn/refresh", {
    method: "POST",
    headers: {
      "client-platform": "browser",
    },
  });
```

The tokens will return in cookies. If your client platform is `browser`, the tokens will return in **secure, httpOnly** cookies. If your client platform is `browser-dev`, the tokens will return in **unsecure** cookies. `browser-dev` is to be used only for development.

### Delete profile / account

To delete their profile/account, an authenticated user must send a DELETE request to `/api/iam/authn/delete`.

#### Request

The body of the request needs the user's **uuid** of the user and a **csrf token**. The uuid is **not** the id. Nuxt IAM uses both id and uuid for a user account and they are both unique. The uuid is displayed to the public, but the id is used internally most of the time.

```
const response = await $fetch("/api/iam/authn/delete", {
    method: "DELETE",
    headers: {
      "client-platform": "app",
    },
    body: {
      uuid: uuid,
      csrf_token: csrfToken,
    },
  });

```

For an example of how to properly delete your profile, please see the `iam/dashboard/settings` page.

### Reset password

To reset your password, send a POST request to `/api/iam/authn/reset`.

#### Request

```
const response = await $fetch("/api/iam/authn/reset", {
    method: "POST",
    headers: {
      "client-platform": 'browser',
    },
    body: {
      email: email,
    },
  });
```

If an email address exists in the system, an email with a **one-time** password reset token will be sent. The token expires in **1 hour.** For security purposes, the response is always `success`. Nuxt IAM does not reveal whether the email exists or not.

### Verify password reset token

_Some may consider this topic advanced. For examples, see how the provided pages work in Nuxt IAM._

To verify a password reset token, send a POST request to `/api/iam/authn/verifyreset`. A password reset token can only be verified **once.** Any further verifications will fail.

#### Request

```
const response = await $fetch("/api/iam/authn/verifyreset", {
    method: "POST",
    headers: {
      "client-platform": 'browser',
    },
    body: {
      token: token,
    },
  });
```

If an email address exists in the system, an email with a **one-time** password reset token will be sent. The token expires in **1 hour.** For security purposes, the response is always `success`. Nuxt IAM does not reveal whether the email exists or not.

### Verify email

_Some may consider this topic advanced. For examples, see how the provided pages work in Nuxt IAM._

To verify an email, send a POST request to `/api/iam/authn/verifyemail`. An email with a verification token link will be sent to you. An email can only be verified **once**. Any other verifications will fail.

#### Request

```
const response = await $fetch("/api/iam/authn/verifyemail", {
    method: "POST",
    headers: {
      "client-platform": 'browser',
    },
    body: {
      email: email,
    },
  });
```

If an email address exists in the system, an email with a **one-time** email verification token will be sent. The token expires in **1 day.**

### Verify email token

_Some may consider this topic advanced. For examples, see how the provided pages work in Nuxt IAM._

To verify an email verification token, send a POST request to `/api/iam/authn/verifyemailtoken`.

#### Request

```
const response = await $fetch("/api/iam/authn/verifyemailtoken", {
    method: "POST",
    headers: {
      "client-platform": clientPlatform,
    },
    body: {
      token: token,
    },
  });
```

## Users (users) API

The users API handles all requests regarding users. Users are considered a restricted resource, therefore cannot be accessed without **authentication** and **authorization.**

### Users API Endpoints

Nuxt IAM adds the following users endpoints to your app. See examples below.

- **/api/iam/users**: GET request will return all users. Maximum return is 100 users. Use `skip` and `take` request parameters to get a set of users e.g., `/api/iam/users?skip=40&take=20`. See more information on [Prisma pagination](https://www.prisma.io/docs/concepts/components/prisma-client/pagination).
- **/api/iam/users/[uuid]**: GET a specific user record given the user's uuid. uuid is **not** the user's id. Both are unique, but function differently. uuid is displayed publicly, but the user id is used mostly internally.
- **/api/iam/users**: POST request will **not** create a user. It will return a 422 error. You must use `/api/iam/authn/register` to create a user.
- **/api/iam/users/[uuid]**: PUT request updates a user given the user's uuid
- **/api/iam/users/[uuid]**: DELETE request deletes a user given the user's uuid

### Users API Requests and Authorization

User data is considered a restricted resource and therefore Nuxt IAM requires that anyone accessing user data be authenticated and authorized. You are of course welcome to change any authentication or authorization logic as you like.

### Getting all users

To get all users, send a GET request to `/api/iam/users`.

#### Request

Here's an example request to get all users. Remember, `client-platform` can be `app`, `browser`, or `browser-dev.` In the example below, `client-platform` is `app`, because this request is being sent from a non-browser application.

```
const response = await $fetch("/api/iam/users", {
    headers: {
      "client-platform": "app",
    },
  });
```

To paginate use [Prisma pagination](https://www.prisma.io/docs/concepts/components/prisma-client/pagination) parameters `skip` and `take`. In the request below, we'll skip the first 40 users, and grab the next 10.

```
const response = await $fetch("/api/iam/users?skip=40&take=10", {
    headers: {
      "client-platform": "app",
    },
  });
```

#### Authorization required

To get all users, a user must be **authenticated** (be logged in), have the role of `SUPER_ADMIN`, and have their **email verified.**

### Getting a particular user

To get a specific user, send a GET request to `/api/iam/users/[uuid]`. `uuid` needs to be a valid user uuid.

#### Request

```
const response = await $fetch("/api/iam/users/[uuid]", {
    headers: {
      "client-platform": "app",
    },
  });
```

#### Authorization required

To get a specific user, a user must be **authenticated** (be logged in), and must either have the role of `SUPER_ADMIN`, or be the **owner** of the record.

### Create a new user

To create a new user, follow the directions for registering a new user.

### Update a user

To update a specific user, send a PUT request to `/api/iam/users/[uuid]`. `uuid` needs to be a valid user uuid.

#### Request

```
const response = await $fetch(`/api/iam/users/[uuid]`, {
    method: "PUT",
    headers: {
      "client-platform": "app",
    },
    body: values,
  });
```

As of February 2023, values need to be of this type:

```
  first_name?: string;
  last_name?: string;
  role?: 'SUPER_ADMIN' | 'ADMIN' | 'GENERAL';
  csrf_token?: string;
  is_active?: boolean;
  permissions?: string;
```

The `?` means that the values are optional. That means you can update all of them at the same time, or only one.

#### Authorization required

To update a specific user, a user must be **authenticated** (be logged in), must either have the role of `SUPER_ADMIN`, or be the **owner** of the record, and must present the **csrf token**.

### Delete a user

To delete a user, send a DELETE request to `/api/iam/users/[uuid]`. `uuid` needs to be a valid user uuid.

#### Request

```
const response = await $fetch(`/api/iam/users/[uuid]`, {
    method: "DELETE",
    headers: {
      "client-platform": 'browser',
    },
    body: {
      csrf_token: 'abc...',
    },
  });

```

#### Authorization required

To delete a user, you must be **authenticated** (be logged in), must either have the role of `SUPER_ADMIN`, or be the **owner** of the record, and must present the **csrf token**.

## Refresh Tokens (refresh-tokens) API

_Some may consider the following topics to be adavanced._

The refresh-tokens API handles all requests regarding refresh tokens. Refresh tokens are considered a restricted resource and, therefore, cannot be accessed without **authentication** and **authorization.**

Refresh tokens are used to get a new set of access and refresh tokens. Nuxt IAM allows only **one** refresh token to be active at any one time. To first get access and refresh tokens, a user must log in. Once logged in, Nuxt IAM will send the user one access token and a refresh token.

If you login again (by sending a POST request to the login endpoint), you will obtain a new set of tokens and the old refresh token will be made inactive. Attempting to refresh your tokens with an inactive token triggers a "stolen token alert" and makes all your refresh tokens inactive. You will be forced to login again. This trigger allows the user to only have one token that can be used to refresh tokens.

Nuxt IAM allows you to work with refresh tokens as a security measure. Refresh tokens are JSON (JavaScript Object Notation) web tokens. They are automatically generated and signed by Nuxt IAM. Any change in the token invalidates the token. A user with an invalid refresh token will not be able to obtain a new set of access and refresh tokens. The user will need to successfylly authenticate (log in) to get a new set of tokens.

Nuxt IAM provides a limited number of refresh token endpoints.

### Refresh Tokens API Endpoints

Nuxt IAM adds the following refresh tokens endpoints to your app. See examples below.

- **/api/iam/refresh-tokens**: GET request will return all users. Maximum return is 100 users. Use `skip` and `take` request parameters to get a set of refresh tokens e.g., `/api/iam/refresh-tokens?skip=40&take=20`. See more information on [Prisma pagination](https://www.prisma.io/docs/concepts/components/prisma-client/pagination).
- **/api/iam/refresh-tokens/[id]**: DELETE request deletes a specific refresh token. This forces the token user to have to log in once their access token expires.
- **/api/iam/refresh-tokens**: DELETE request deletes all refresh tokens. This will force all users to log in once their access token expires.

### Refresh Tokens API Requests and Authorization

Refresh tokens data is considered a restricted resource and therefore Nuxt IAM requires that anyone accessing user data be authenticated and authorized. You are of course welcome to change any authentication or authorization logic as you like.

### Getting all refresh tokens

To get all refresh tokens, send a GET request to `/api/iam/refresh-tokens`.

#### Request

Here's an example request to get all refresh tokens. Remember, `client-platform` can be `app`, `browser`, or `browser-dev.` In the example below, `client-platform` is `app`, because this request is being sent from a non-browser application.

```
const response = await $fetch("/api/iam/refresh-tokens", {
    headers: {
      "client-platform": "app",
    },
  });
```

To paginate use [Prisma pagination](https://www.prisma.io/docs/concepts/components/prisma-client/pagination) parameters `skip` and `take`. In the request below, we'll skip the first 40 refresh tokens, and grab the next 10.

```
const response = await $fetch("/api/iam/users?skip=40&take=10", {
    headers: {
      "client-platform": "app",
    },
  });
```

In the examples above, the GET request is implied.

#### Authorization required

To get all refresh tokens, a user must be **authenticated** (be logged in), have the role of `SUPER_ADMIN`, and have their **email verified.**

### Deleting a particular refresh token

To delete a specific refresh token, send a DELETE request to `/api/iam/refresh-tokens/[id]`. This will force the user to login after their access token has expired.

#### Request

```
const response = await $fetch("/api/iam/refresh-tokens/[id]", {
    headers: {
      "client-platform": "app",
    },
  });
```

#### Authorization required

To delete a specific refresh token, a user must be **authenticated** (be logged in), must have the role of `SUPER_ADMIN`, have their **email verified,** and must present the **csrf token**.

### Deleting all refresh tokens

To delete all refresh token, send a DELETE request to `/api/iam/refresh-tokens`. This will force all users to login after each of their access tokens expire.

#### Request

```
const response = await $fetch("/api/iam/refresh-tokens", {
    headers: {
      "client-platform": "app",
    },
  });
```

#### Authorization required

To delete all refresh tokens, a user must be **authenticated** (be logged in), must have the role of `SUPER_ADMIN`, have their **email verified,** and must present the **csrf token**.
