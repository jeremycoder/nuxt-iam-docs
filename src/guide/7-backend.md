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

## Users API

The users API deals with the REST (Represetational State Transfer) of users.

## Refresh Tokens API

The refresh tokens API deals with refreshing JWT tokens.

## Both Backend and Frontend

Nuxt IAM is both frontend and backend. The main authentication and authorization logic takes place in the backend, and you're welcome to change anything as suits your needs. Nuxt IAM adds authentication and authorization components, pages, api routes, and logic to your Nuxt app allowing your app to have authentication and authorization logic. All the components, pages, api routes, and logic are 100% customizable so you can change things any way you want.

## Pages

Nuxt IAM adds several pages to your apps frontend. The pages are wrappers around components. Use these pages and components as is or as starting points for making your app great. The following routes are added to your Nuxt front end. Find them in your **pages** directory:

- **iam**: Parent directory for all pages
- **iam/index**: Introductory page for Nuxt IAM
- **iam/register**: User registration page. After successful registration, you will be directed to login page
- **iam/verifyemail**: If email verification was set to true, (see configuration section), receives email verification token and sends it to backend for verification.
- **iam/login**: User login page. After successful login, you will be directed to iam/dashboard/index
- **iam/dashboard/index**: User dashboard.
- **iam/dashboard/profile**: User profile. User can update their account.
- **iam/dashboard/settings**: User settings. User can update their password and delete their account.
- **iam/reset**: User can reset their password. Does not have to be logged in. User will receive an email with a one-time password reset token.
- **iam/verify**: Page that receives password reset token and sends it to backend for verification
- **iam/verifyfailed**: Displays email or password verification failure.
- **iam/verifysuccessful**: Displays password verification success and a temporary password.

## Components

Nuxt IAM adds the following components to your Nuxt application.

## Server

Nuxt IAM adds the following directories to your **server/api** directory.

- **iam/authn**: global authentication handler
- **iam/refresh-tokens**: refresh tokens handler
- **iam/users**: global users handler

## Backend for Frontend

Nuxt IAM uses the Backend For Frontend (BFF) architectural pattern to increase the security of your Nuxt application. A BFF pattern allows Nuxt IAM to provide the best security practices for any client. Nuxt IAM differentiates between browsers and apps to provide the best security depending on whether the client is a browser or an app.

Every client needs to send the **client-platform** on every request.

`client-platform` is a **required** header and it must be sent with every request. Client platform allows Nuxt IAM to provide the best practices for securing your app. `client-platform` must be:

- `app`: Use `app` if the request is coming from a non-browser such as a mobile app, tablet, or a tool like POSTMAN. Access and refresh tokens will be sent in the response headers. Can be used in **production**.
- `browser`: Use `browser` if the request is coming from a browser. Access and refresh tokens will be sent in **secure, httpOnly** cookies. Can be used in **production**.
- `browser-dev`: Use `browser-dev` if the request is coming from a browser in a development environment. Access and refresh tokens are sent in **unsecure** cookies. Use only in **development.**

## Database

## Tokens

### Access Tokens

### Refresh Tokens
