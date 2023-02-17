# Features

The following are some of the features of Nuxt IAM. This page assumes you are using all the features of Nuxt IAM. If you're looking for API routes, please go to [backend](./7-backend.md) documentation.

## User Registration

Nuxt IAM comes with pages and an api that allow you to register a user.

### Local registration

To register a user locally, that is using **email** and **password** , navigate to the page `/iam/register`. See a [live example](https://nuxt-iam.vercel.app/iam/register)

### Verify user email

To verify user email registration, you need to go to your **.env** file, setup your email sender, and set the **.env** variable `IAM_VERIFY_REGISTRATIONS="true"`. Please see [Configuration](./6-configuration.md) for setting up your email sender.

### Google registration

To register using Google, you'll need to have already set up your [Google console](https://developers.google.com/identity/gsi/web/guides/overview) app and obtained your credentials. It's free. You'll need your **Google client id.**

Then set your **.env** variables `IAM_ALLOW_GOOGLE_AUTH="true"` and `IAM_GOOGLE_CLIENT_ID="12345"`. The 12345 represents your Google client id.

After that, navigate to the `/iam/login` page, and click the Google button. See a [live example](https://nuxt-iam.vercel.app/iam/login)

## User login

Nuxt IAM comes with pages and an api that allow you to login as a user.

### Local login

To login as a user locally, that is using **email** and **password** , navigate to the page `/iam/login`. See a [live example](https://nuxt-iam.vercel.app/iam/login)

### Google login

To login using Google, navigate to the `/iam/login` page, and click the Google button. Nuxt IAM will create an account for you using data from your Google account. See a [live example](https://nuxt-iam.vercel.app/iam/login)

## User password reset

For this feature to work, you need to **configure your email sender**. Please see [Configuration](./6-configuration.md). If you forget your password, navigate to the page `/iam/reset`. See a [live example](https://nuxt-iam.vercel.app/iam/reset).

## User dashboard

After you have successfully logged in, you'll be taken to your dashboard. See a [live example](https://nuxt-iam.vercel.app/iam/dashboard)

## User profile

The user profile is a child page of the dashboard and contains basic information about your user account. It allows you to change some information about yourself.

## User settings

The user settings allow you to change your password and delete your account. Deleting your account will permanently delete your account and remove all associated data. Soft deletes have **not** been implemented yet.

## User logout

Clicking the user log out link will log you out of your profile area. This removes all cookies and deactivates your refresh token so that you'll need to log in to get back into your account.

## Admin area

The admin area is an area in the profile that is reserved for user administration. Nuxt IAM is designed to allow you to configure it how you want.

### No automatic admin access

All users who register with Nuxt IAM **do not** have access to the admin area. Nuxt IAM does not follow the policy of the first registered user automatically getting administrator rights as this is considered unsafe because a bad actor can assume that the very first record in the database belongs to the admin user.

The **only** way to get admin access if you're the **first user**, is to modify your record in the database. To get admin access, add this value to your **permissions field** in the **users table**: `canAccessAdmin`. Any person who has that has `canAccessAdmin` in their permissions field, will be able to access the admin area.

After you have admin access, you can give any user admin access as well by modifying their record.

### Roles and permissions

Nuxt IAM has a few roles and permissions. Nuxt IAM uses both role-based access control and attribute-based access control. Nuxt IAM has three types of roles: `SUPER_ADMIN`, `ADMIN`, and `GENERAL`.

#### Roles

Nuxt IAM comes with three basic roles.

- `SUPER_ADMIN`: Is required to perform access the [users API](./7-backend.md), and is considered to be the most privileged role.
- `ADMIN`: Has no privileges. You may add privileges if you desire.
- `GENERAL`: Is considered to have the lowest level privilege. A user with this role, can only only access their account.

#### Permissions

The only permission that comes with Nuxt IAM is `canAccessAdmin`. Any user with this permission can access the admin area.

**The **most privileged** user is the one has a role of `SUPER_ADMIN` **and** has `canAccessAdmin` permission attribute.** When you add more permission attributes, separate them by commas. For example `canAccessAdmin, hasDeleteAuthority, canRemoveUser`.

## Admin user management

When a user who has admin access log in, they can view all users, add users, modify user data, and delete users.

## Admin token management

A user with admin access can also remove individual refresh tokens from users. Removing a refresh token means that that user will not be able to have their tokens automatically refreshed and will need to log in once their access token expires. A user with admin access can also remove all refresh tokens. This is considered a safety measure. Removing all refresh tokens will force **every user in the system** to reauthenticated (login) once their access token expires. Access tokens last only 15 minutes.

## TypeScript

Most of Nuxt IAM is written in TypeScript. The Vue components are mostly written in JavaScript.

## Bootstrap

Nuxt IAM uses [Bootstrap 5](https://getbootstrap.com/docs/5.0/getting-started/introduction/) CSS framework, and some other lower versions of Bootstrap for its frontend needs. You don't have to use Bootstrap. You're welcome to use any CSS framework you need. Nuxt IAM may change to another CSS framework in the future.

Bootstrap was chosen because most of it can be run from a simple CDN call. All pages or components that run Bootstrap call it on the page alone. The CSS framework is not added in the Node modules so that it does not intefere with other frameworks you may want to use such as Tailwind.

Calling Bootstrap from each page is not the most efficient way of using a CSS framework, but it allows Nuxt IAM to remain isolated from the rest of your app, and not interfere with already installed CSS frameworks you may have.
