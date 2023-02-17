# Files and Directories

Nuxt IAM adds several directories and files to your Nuxt application. Here's a brief explanation of them.

## components

The `components` directory is part of Nuxt. Nuxt IAM adds several components to your app. You will find the additional components in the **components** directory. Each component's name starts with **iam** e.g. iamDashboard, iamLogin.

- **iamAdmin**: Deals with user administration
- **iamDashboard**: Contains dashboard user interface and logic such as checking if user is logged in, and email verification. Also acts as the layout wrapper for other pages.
- **iamLogin**: Contains login UI (user interface) and logic
- **iamRegister**: Frontend user registration component
- **iamReset**: Frontend user password reset component
- **iamVerifyEmailToken**: Verifies if token sent in email verification is valid
- **iamVerifyFailed**: Component that notifies user is email verification token or password verification token failed
- **iamVerifyPasswordReset**: Receives password verification token from email
- **iamVerifySuccessful**: Notifies user if password reset was successful

## composables

The `composables` directory is part of Nuxt. It houses all the composables of your application. Nuxt IAM adds the following composables to your application:

- **useIam**: Contains functions and logic for authentication such as user registration, logging in, checking if user is authenticated etc.
- **useIamAdmin**: Contains functions and logic for user administration.

## iam

The `iam` directory is the main Nuxt IAM directory. It contains the vast majority of files and backend logic that make Nuxt IAM work.

### authz

The `authz` directory is found in the `iam` directory. It is designed to house any functions that deal with authroization.

### middleware

The `middleware` directory is found inside the `iam` directory. Its purpose is to store any middleware functions, that is, functions that may need to run between controllers and models.

### misc

The `misc` directory is a folder for miscellaneous items. It contains functions such as the type definitions that Nuxt IAM uses, functions that send email, and helper functions that perform work required in different places of Nuxt IAM. The helper functions file is extensive and should be refactored in the future.

### mvc

The `mvc` directory stands for model, view, and controller. It only houses the models and controllers because Nuxt IAM is an API (application program interface) authentication framework and APIs have no views. Nevertheless, we have kept the 'v' for convention.

The `mvc` contains the controllers (functions that handle requests from users and return responses), and models (functions that handle data). Because the amount of data and logic that is required for authentication and authorization to work properly is quite extensive, the mvc directories also contain files called `queries`. Queries deal with database access.

The `mvc` directory's main directories are `authn`, which handles authentication, `refresh-tokens`, which handle refresh token logic, and `users`, which handles users data logic. Each of these directories contains a controller file, a model file, and a queries file.

We worked hard to make the code readable and easy to understand and so we do not have many long functions, but we do have many small functions.

In general, the data flow in Nuxt IAM is:

- request: server api --> controller --> model --> query --> helper
- response: server api <-- controller <-- model <-- query <-- helper

To modify Nuxt IAM to your liking, you need to understand the `mvc` directory.

### ui

The `ui` directory contains user interface items such as the Nuxt IAM logo.

## pages

The `pages` directory is part of Nuxt. Nuxt IAM adds several pages to your apps frontend. The pages are wrappers around components. The following pages are added to your Nuxt front end. Find them in your **pages** directory:

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

## prisma

The `prisma` directory is added by [Prisma](https://prisma.io), which Nuxt IAM uses as its object-relational mapper. Prisma is required for Nuxt IAM to work. The `prisma` directory may contain a `migrations` directory. The `migrations` directory will house all migrations made to your database. Learn more about [Prisma migrations](https://www.prisma.io/docs/concepts/components/prisma-migrate).

The `prisma` directory will contain `schema.prisma` file. This is an important file for Prisma. It contains the schema (structure) of your database. It shows the names of your tables, their columns, and their data types. **Only modify this file if you know what you are doing.**

Check out the Prisma web site to learn more about the [Prisma schema](https://www.prisma.io/docs/concepts/components/prisma-schema).

### Tables

When you examine your `prisma.schema` file you'll notice several tables. Some tables are exposed to the frontend such as `users`, and `refresh-tokens`, but most tables are not. The other tables are internal tables and should only be exposed to the frontend if need be.

To modify the tables in the UI, we recommend using a database management tool such [DBeaver](https://dbeaver.io/).

## server

The `server` directory is part of Nuxt. Inside the `server` directory, you will find an `api` directory, which is also part of Nuxt. Nuxt IAM adds an `iam` directory to the `api` directory, and inside the `iam` directory adds the following directories:

- `authn` - the global Nuxt IAM authentication endpoint. All queries that begin with `api/iam/authn` hit this endpoint, and are sent to the controller in the file `iam/mvc/authn/controller.ts`, which routes them to the appropriate destination.
- `refresh-tokens` - the global Nuxt IAM refresh-tokens endpoint. All queries that begin with `api/iam/refresh-tokens` hit this endpoint, and are sent to the controller in the file `iam/mvc/refresh-tokens/controller.ts`, which routes them to the appropriate destination.
- `users` - the global Nuxt IAM users endpoint. All queries that begin with `api/iam/users` hit this endpoint, and are sent to the controller in the file `iam/mvc/users/controller.ts`, which routes them to the appropriate destination.

You can probably see that the `iam/mvc` directory and the `server/api/iam` mirror each other. This is by design and Nuxt IAM would like to keep it that way.
