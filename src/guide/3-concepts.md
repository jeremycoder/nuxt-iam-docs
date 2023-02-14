# Concepts

Understanding the following concepts will help you work with Nuxt IAM faster.

## Both Backend and Frontend

Nuxt IAM is both frontend and backend. The main authentication and authorization logic takes place in the backend. Nuxt IAM adds authentication and authorization components, pages, api routes, and logic to your Nuxt app allowing your app to have authentication and authorization logic. All the components, pages, api routes, and logic are 100% customizable so you can change things any way you want.

## Server

Nuxt IAM uses Nuxt's server engine [Nitro](https://nuxt.com/docs/guide/concepts/server-engine) and the entire backend is built on it. Nuxt IAM adds the following directories to your **server/api** directory.

- **iam/authn**: authentication handler
- **iam/refresh-tokens**: refresh tokens handler
- **iam/users**: global users handler

## Backend for Frontend

Nuxt IAM uses the Backend For Frontend (BFF) architectural pattern to increase the security of your Nuxt application. A BFF pattern allows Nuxt IAM to provide the best security practices for any client. Nuxt IAM differentiates between two types of clients: **browsers** and **apps**. It does this by requiring that every request contain the **client-platform** header.

Every client needs to send the **client-platform** header on every request.

`client-platform` is a **required** header and it must be sent with every request. Client platform allows Nuxt IAM to provide the best practices for securing your app. `client-platform` must be:

- **`app`**: Use `app` if the request is coming from a non-browser such as a mobile app, tablet, or a tool like POSTMAN. Access and refresh tokens will be sent in the **response headers**. This is designed to be used in **production**.
- **`browser`**: Use `browser` if the request is coming from a browser. Access and refresh tokens will be sent in **secure, httpOnly** cookies. This is designed to be used in **production**.
- **`browser-dev`**: Use `browser-dev` if the request is coming from a browser in a **development environment**. Access and refresh tokens are sent in **unsecure** cookies. Use only in **development.**

## Database

Nuxt IAM requires a database to operate successfully, and uses [Prisma](http://www.prisma.io) as its object relation mapper (ORM). For database configuration, please see [Configuration](./6-configuration).

## Tokens

Nuxt IAM uses signed JSON web tokens (JWT) as part of its security. There are two types of tokens used: **access tokens** and **refresh tokens.** Access tokens allow a user to access a restricted resource, and refresh tokens allow a user to access a new pair of tokens.

### Access Tokens

Access tokens are JWT tokens that grant an authenticated user access to a particular resource. For example, if a user wants to access their profile, they need to login with their correct email and password combination. If successful, Nuxt IAM will create an access token and refresh token and send them back to the client.

If the client is an **app**, the tokens will be sent in the header as **iam-access-token** for the access token, and **iam-refresh-token** for the refresh token.

If the client is a **browser**, the tokens will be sent in cookies. If the client-platform is browser, the cookies will be secure, if the client-platform is browser-dev, the cookies will be unsecure.

Access tokens expire every **15 minutes**. Once an access token expires, the client will be unable to access the resource, and will need to log in. New tokens can be obtained using a valid refresh token. If you use the pages provided by Nuxt IAM, your access and refresh tokens will be automatically replaced once Nuxt IAM detects that the access token has expired.

If your refresh token expires, you must log in again.

### Refresh Tokens

Refresh tokens are JWT tokens that are used to get new access and refresh tokens. They expire every **14 days**. If your access token expires, you'll need to login again. You can get a new set of access and refresh tokens when you send a POST request to `/api/iam/authn/refresh` with an unexpired refresh token. If your refresh token has expired, you will not be able to get a new set of tokens and you'll need to login.

Every authenticated user can only have one active refresh token at a time.

#### Preventing abuse

All refresh tokens are tracked, and an authenticated user can only have one active refresh token at a time. If anyone tries to get a new set of tokens using an old token, Nuxt IAM will consider that a token stolen and will deactivate all of the user's refresh tokens. The user will need to log in once the access token expires. This feature protects the user's account in case the refresh token is stolen.

## Sessions

Nuxt IAM uses sessions to manage user sessions. Every user can only have one session at a time.
