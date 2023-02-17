# Configuration

Here's how to configure Nuxt IAM. Every time you change your environment variables either in **nuxt.config.ts** or **.env** file, you'll need to **restart** your application.

## nuxt.config.ts

The following variables are required in your Nuxt configuration file (nuxt.config.ts). The variables link to the variables in your .env file. Please copy and paste the variables into your **nuxt.config.ts** file.

```
// https://nuxt.com/docs/api/configuration/nuxt-config
export default defineNuxtConfig({
  //...
  runtimeConfig: {
    // IAM token secrets. Please rotate every 2 - 4 weeks
    iamAccessTokenSecret: process.env.IAM_ACCESS_TOKEN_SECRET,
    iamRefreshTokenSecret: process.env.IAM_REFRESH_TOKEN_SECRET,
    iamResetTokenSecret: process.env.IAM_RESET_TOKEN_SECRET,
    iamVerifyTokenSecret: process.env.IAM_VERIFY_TOKEN_SECRET,

    // Public Url
    iamPublicUrl: process.env.IAM_PUBLIC_URL,

    // IAM Emailer
    iamEmailer: process.env.IAM_EMAILER,

    // nodemailer-service
    iamNodemailerService: process.env.IAM_NODEMAILER_SERVICE,
    iamNodemailerServiceSender: process.env.IAM_NODEMAILER_SERVICE_SENDER,
    iamNodemailerServicePassword: process.env.IAM_NODEMAILER_SERVICE_PASSWORD,

    // nodemailer-smtp
    iamNodemailerSmtpHost: process.env.IAM_NODEMAILER_SMTP_HOST,
    iamNodemailerSmtpPort: process.env.IAM_NODEMAILER_SMTP_PORT,
    iamNodemailerSmtpSender: process.env.IAM_NODEMAILER_SMTP_SENDER,
    iamNodemailerSmtpPassword: process.env.IAM_NODEMAILER_SMTP_PASSWORD,

    // IAM SendGrid
    iamSendGridApiKey: process.env.IAM_SENDGRID_API_KEY,
    iamSendgridSender: process.env.IAM_SENDGRID_SENDER,

    // GOOGLE CLIENT ID
    iamGoogleClientId: process.env.IAM_GOOGLE_CLIENT_ID,

    // Do not put secret information here
    public: {
      iamClientPlatform: process.env.IAM_CLIENT_PLATFORM,
      iamVerifyRegistrations: process.env.IAM_VERIFY_REGISTRATIONS,
      iamAllowGoogleAuth: process.env.IAM_ALLOW_GOOGLE_AUTH,
    },
  },

  modules: ["nuxt-vue3-google-signin"],
  googleSignIn: {
    clientId: process.env.IAM_GOOGLE_CLIENT_ID,
  },
  //...
});
```

## .env file

Below is an example of a .env file that contains the necessary variables for Nuxt IAM to work properly. Please add the following variables to your **.env** file.

```
# Environment variables declared in this file are automatically made available to Prisma.
# See the documentation for more detail: https://pris.ly/d/prisma-schema#accessing-environment-variables-from-the-schema

# Prisma supports the native connection string format for PostgreSQL, MySQL, SQLite, SQL Server, MongoDB and CockroachDB.
# See the documentation for all the connection string options: https://pris.ly/d/connection-strings

# PRISMA DATABASE
DATABASE_URL="mysql://dbuser:dbpassword@dbserver:dbport/dbname"

# NUXT IAM TOKEN SECRETS (Please change them every 2 - 4 weeks)
# Can use in node 'crypto.randomBytes(64).toString('hex')'
IAM_ACCESS_TOKEN_SECRET="fa85424538b2878a7785a703d168fc58550c8ef3a02c23b8aee5f8adf98159b218296926d37164db8ed48d28a73c01387cf4fd0032e7e76858e71a09b2b82c88"
IAM_REFRESH_TOKEN_SECRET="c36f673adcbfe27859867697d6c98430c3757d3eafb7bca3fe90fe349baa9d88ec10932aba5da22f37264c2c2bd31404e5a22822be6f054ec9d40a56b28b97e1"
IAM_RESET_TOKEN_SECRET="a67102c7d684ad370409855fe3e7a65f9f9ccffeb82b0d53fe328c66a1405b03737da79a4dd42266a5a83ea9826421b9b031703337be5fd1e1eac0feb1ae2166"
IAM_VERIFY_TOKEN_SECRET="823459ed2ed1d80df1aedd3a5c03f1ee6132c20a076270d63d89e06c6b0f4fb7299991610f17dde4930b54432a6cf7b8d13b6da08b9f89bb8b30bb2c46c37f9e"

# NUXT IAM
# If using a browser like your Nuxt app, use 'browser' for production
# If using a browser like your Nuxt app, use 'browser-dev' for development
# If you're not using a browser, then use 'app'
IAM_CLIENT_PLATFORM = "browser"

IAM_PUBLIC_URL="http://localhost:3000"

# NUXT IAM EMAIL
# nodemailer-service, nodemailer-smtp, sendgrid
IAM_EMAILER="nodemailer-smtp"

# nodemailer-service
IAM_NODEMAILER_SERVICE="hotmail"
IAM_NODEMAILER_SERVICE_SENDER="myusername@outlook.com"
IAM_NODEMAILER_SERVICE_PASSWORD="myExcellentPassword767*"

# nodemailer-smtp
IAM_NODEMAILER_SMTP_HOST="mysmtp.host"
IAM_NODEMAILER_SMTP_PORT="465"
IAM_NODEMAILER_SMTP_SENDER="myname@mydomain.com"
IAM_NODEMAILER_SMTP_PASSWORD="myAmazingPassword753$"

# SENDGRID API KEY
IAM_SENDGRID_API_KEY="12345678901234567890"
IAM_SENDGRID_SENDER="myname@mysendgridaccount.com"

# NUXT IAM VERIFY REGISTRATIONS
IAM_VERIFY_REGISTRATIONS="false"

# IAM GOOGLE CLIENT ID
IAM_ALLOW_GOOGLE-AUTH="true"
IAM_GOOGLE_CLIENT_ID="123...com"
```

## Environment Variables Explained

The following environment variables exist in Nuxt IAM to help it work properly. Edit the **.env** file. Never commit the .env to a public repository like Github.

### Prisma database connection

```
# PRISMA DATABASE
DATABASE_URL="mysql://dbuser:dbpassword@dbserver:dbport/dbname"
```

Nuxt IAM uses [Prisma](https://prisma.io) as its object relational mapper. A database is required in order for Nuxt IAM to work properly. This line tells prisma the name of the database and how to log into it. For more information on Prisma database connections, please see [Prisma database connections](https://www.prisma.io/docs/reference/database-reference/connection-urls)

Please replace the following variables with your own database's variables.

- `mysql`: The database software. Prisma supports other database connections.
- `dbuser`: The database user
- `dbpassword`: The database password
- `dbserver`: The database server
- `dbport`: The database port
- `dbname`: The database name

### Token secrets

```
# NUXT IAM TOKEN SECRETS (Please change them every 2 - 4 weeks)
# Can use in node 'crypto.randomBytes(64).toString('hex')'
IAM_ACCESS_TOKEN_SECRET="..."
IAM_REFRESH_TOKEN_SECRET="..."
IAM_RESET_TOKEN_SECRET="..."
IAM_VERIFY_TOKEN_SECRET="..."
```

All Nuxt IAM JSON web tokens (JWT) are signed. Nuxt IAM uses randomly generated secret strings to sign the tokens. Nuxt IAM recommends that you use a minimum string of **64 randomly-generated cryptographically-secure** characters. Please run this code in Node to generate a random hexadecimal string `crypto.randomBytes(64).toString('hex')`. We recommend that you change all your secrets every **2 - 4 weeks.**

Changing your secrets will force every user to **reauthenticate** regardless of the status of their active or refresh tokens.

- `IAM_ACCESS_TOKEN_SECRET="...`: Secret string used to create access tokens.
- `IAM_REFRESH_TOKEN_SECRET="..."`: Secret string used to create refresh tokens.
- `IAM_RESET_TOKEN_SECRET="..."`: Secret string used to create refresh tokens.
- `IAM_VERIFY_TOKEN_SECRET="..."` : Secret string used to verify tokens.

### Public Url

- `IAM_PUBLIC_URL="http://localhost:3000"`: Url used when sending emails. `http://localhost:3000` is used as an example. Add the correct url for your application.

### Email configuration

Nuxt IAM supports sending emails.

```
# NUXT IAM EMAIL
# nodemailer-service, nodemailer-smtp, sendgrid
IAM_EMAILER="nodemailer-smtp"

# nodemailer-service
IAM_NODEMAILER_SERVICE="hotmail"
IAM_NODEMAILER_SERVICE_SENDER="myusername@outlook.com"
IAM_NODEMAILER_SERVICE_PASSWORD="myExcellentPassword767*"

# nodemailer-smtp
IAM_NODEMAILER_SMTP_HOST="mysmtp.host"
IAM_NODEMAILER_SMTP_PORT="465"
IAM_NODEMAILER_SMTP_SENDER="myname@mydomain.com"
IAM_NODEMAILER_SMTP_PASSWORD="myAmazingPassword753$"
```

#### Email service

Nuxt IAM supports three types of email services: [Nodemailer service](https://nodemailer.com/smtp/well-known/), [Nodemailer SMTP](https://nodemailer.com/smtp/), and [Sendgrid](https://sendgrid.com/).

```
# nodemailer-service, nodemailer-smtp, sendgrid
IAM_EMAILER="nodemailer-smtp"
```

Nuxt IAM needs to know which email service to use to send emails. You need to configure your email services before any features that require email can be used. Nuxt IAM uses email services to send email verification emails, and password reset emails.

- `IAM_EMAILER="nodemailer-smtp"`: Tells Nuxt IAM which email service to use. You can add `nodemailer-smtp`, `nodemailer-service`, or `sendgrid`. In the example, we have chosen `nodemailer-smtp`.

#### Nodemailer Service

Below is an example of a properly configured email service using Nodemailer and Microsoft's Hotmail email service. Nodemailer is free to use, and you can get a free `outlook.com` account.

```
# nodemailer-service
IAM_NODEMAILER_SERVICE="hotmail"
IAM_NODEMAILER_SERVICE_SENDER="myusername@outlook.com"
IAM_NODEMAILER_SERVICE_PASSWORD="myExcellentPassword767*"
```

In the above example, you'll need to have the email address `myusername@outlook.com` and password `myExcellentPassword767*`. Make sure your email address is verified with Hotmail. Perhaps try to send and receive an email with your Hotmail account before using Nuxt IAM. For more information about Nodemailer, please see [Nodemailer services](https://nodemailer.com/smtp/well-known/).

#### Nodemailer SMTP

Below is an example of a properly configured email service using Nodemailer SMTP (simple mail transfer protocol). Nodemailer is free to use.

```
# nodemailer-smtp
IAM_NODEMAILER_SMTP_HOST="mysmtp.host"
IAM_NODEMAILER_SMTP_PORT="465"
IAM_NODEMAILER_SMTP_SENDER="myname@mydomain.com"
IAM_NODEMAILER_SMTP_PASSWORD="myAmazingPassword753$"
```

To use `nodemailer-smtp` you'll need to have the email address with an SMTP host. If you have your own domain name, you can find this information with your web hosting provider. In the example above, `mysmtp.host` is the host, `465` is the port (very common port), `myname@domain.com` is the email address, `myAmazingPassword753$` is the password.

#### Sendgrid Email service

Nuxt IAM also supports [Sendgrid](https://sendgrid.com/) for email services. To use Nuxt IAM with Sendgrid, you'll need to create an account with Sendgrid, have a verified sender email address, and an api key. Sendgrid has a free plan.

```
# SENDGRID API KEY
IAM_SENDGRID_API_KEY="12345678901234567890"
IAM_SENDGRID_SENDER="myname@mysendgridaccount.com"
```

In the example above, `12345678901234567890` is the Sendgrid api key, and `myname@mysendgridaccount.com` is the verified Sendgrid sender email. You'll need to have these configured before you can use Nuxt IAM for email.

### Email verification

Nuxt IAM allows you to add email verification to your application. After a user registers with your application, a user account is created. When the user logs in, if the email is not verified, the user will be prompted to click a button to verify their email. After the button is clicked, an email will be sent to the user's email address on file.

The user has 24 hours to verify their email address. The user will receive an email with **one-time** verification email link. After the email is verified, the user will be able to login and proceed to their account.

The link can only be verified **one time**. If the user attempts to verify again, verification will fail.

```
# NUXT IAM VERIFY REGISTRATIONS
IAM_VERIFY_REGISTRATIONS="false"
```

The example above allows you to ask for email verification at registration or not. `false` means that emails will not be verified. When a user registers with your application, they will be immediately sent to their user profile. Nuxt IAM recommends verifying all registrations. To verify all registrations, change your environment variable to `IAM_VERIFY_REGISTRATIONS="true"`.

### Using Google to Register / Login

Nuxt IAM allows you to use Google identification services to allow users to register and log in using their Google account. You'll need to have a [Google console account](https://developers.google.com/identity/gsi/web/guides/overview) created with a client ID. If you already have an account, you should be able to create [credentials](https://console.cloud.google.com/apis/credentials) as of February 15, 2023. You'll want the **OAuth 2.0 Client IDs** section.

#### Authorized JavaScript Origins

Your authorized JavaScript origins should look similar to below. If using localhost, you'll need to have **both**:

- **http://localhost:3000**, and
- **http://localhost**

Here's an example of a production origin:

- **https://nuxt-iam.vercel.app**

#### Authorized Redirect URI

The authorized rediret uri will need to `http://localhost:3000/iam/dashboard`.

Here's an example of a production authorized uri:

- **https://nuxt-iam.vercel.app/iam/dashboard**

Whatever domain name you use, the redirect uri must be **/iam/dashboard**.

```
# IAM GOOGLE CLIENT ID
IAM_ALLOW_GOOGLE-AUTH="true"
IAM_GOOGLE_CLIENT_ID="123...com"
```

The example above shows properly configured variables for using Google Identity Services. In the example, `IAM_ALLOW_GOOGLE-AUTH="true"` means that this app will be using Google Identity Services. That means a Google Sign In button will appear on your login page. Setting `IAM_ALLOW_GOOGLE-AUTH="false"` means that the you will not be using Google Identity Services, and the Google Sign In button will not be visible on the login button.

`IAM_GOOGLE_CLIENT_ID="123...com"` is the client id for your Google app.

#### Google Sign In .nuxt.config.ts

You'll need to add the following to your **nuxt.config.ts** file to properly use Google Identity Services. If you copied the nuxt.config.ts variables, then you should already have this information in your nuxt.config.ts file.

```
export default defineNuxtConfig({
  //...
  runtimeConfig: {
    //...

    // GOOGLE CLIENT ID
    iamGoogleClientId: process.env.IAM_GOOGLE_CLIENT_ID,

    // Do not put secret information here
    public: {
      // ...
      iamAllowGoogleAuth: process.env.IAM_ALLOW_GOOGLE_AUTH,
      //...
    },
  },

  modules: ["nuxt-vue3-google-signin"],
  googleSignIn: {
    clientId: process.env.IAM_GOOGLE_CLIENT_ID,
  },
  //...
});
```

## Error reporting

Nuxt IAM provides very little frontend error reporting, but provides extensive backend error reporting. If an error occurs, please check your your server console logs.
