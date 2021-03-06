# Simple OAuth2 Github

This library is a wrapper around [Simple OAuth2 Library](https://github.com/lelylan/simple-oauth2)

Specially made for [Authorization Code Flow](https://tools.ietf.org/html/draft-ietf-oauth-v2-31#section-4.1) with GitHub.

## Requirements

Latest Node 8 LTS or newer versions.

## Getting started

```
npm install --save simple-oauth2 @jimmycode/simple-oauth2-github
```

or 

```
yarn add simple-oauth2 @jimmycode/simple-oauth2-github
```

### Usage

```js
const simpleOAuth2Github = require('@jimmycode/simple-oauth2-github');
const github = simpleOAuth2Github.create(options);
```

`github` object exposes 3 keys:
* authorize: Middleware to request user's authorization.
* getToken: Middleware for callback processing and exchange the authorization token for an `access_token`
* oauth2: The underlying [simple-oauth2](https://github.com/lelylan/simple-oauth2) instance.

### Options

SimpleOAuth2Github comes with default values for most of the options.

**Required options**

| Option       | Description                                   |
|--------------|-----------------------------------------------|
| clientId     | Your App Id.                                  |
| clientSecret | Your App Secret Id.                           |
| callbackURL  | Callback configured when you created the app. |


**Other options**

| Option           | Default                      | Description                                                                                                                                                                               |
|------------------|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scope            | ['read:user', 'read:email']  | https://developer.github.com/apps/building-oauth-apps/understanding-scopes-for-oauth-apps                                                                                                 |
| state            | ''                           | Your CSRF anti-forgery token. More at: https://auth0.com/docs/protocols/oauth2/oauth-state                                                                                                |
| returnError      | false                        | When is false (default), will call the next middleware with the error object. When is true, will set req.tokenError to the error, and call the next middleware as if there were no error. |
| authorizeHost    | 'https://github.com'         |                                                                                                                                                                                           |
| authorizePath    | '/login/oauth/authorize'     |                                                                                                                                                                                           |
| tokenHost        | 'https://github.com'         |                                                                                                                                                                                           |
| tokenPath        | '/login/oauth/access_token'  |                                                                                                                                                                                           |
| authorizeOptions | {}                           | Pass extra parameters when requesting authorization.                                                                                                                                      |
| tokenOptions     | {}                           | Pass extra parameters when requesting access_token.                                                                                                                                       |

## Example

### Original boilerplate

```js
const oauth2 = require('simple-oauth2').create({
  client: {
    id: process.env.GITHUB_CLIENT_ID,
    secret: process.env.GITHUB_CLIENT_SECRET
  },
  auth: {
    authorizeHost: 'https://github.com',
    authorizePath: '/login/oauth/authorize',

    tokenHost: 'https://github.com',
    tokenPath: '/login/oauth/access_token'
  }
});

router.get('/auth/github', (req, res) => {
  const authorizationUri = oauth2.authorizationCode.authorizeURL({
    redirect_uri: 'http://localhost:3000/auth/github/callback',
    scope: ['read:user', 'read:email']
  });

  res.redirect(authorizationUri);
});

router.get('/auth/github/callback', async(req, res) => {
  const code = req.query.code;
  const options = {
    code,
    redirect_uri: 'http://localhost:3000/auth/github/callback'
  };

  try {
    // The resulting token.
    const result = await oauth2.authorizationCode.getToken(options);

    // Exchange for the access token.
    const token = oauth2.accessToken.create(result);

    return res.status(200).json(token);
  } catch (error) {
    console.error('Access Token Error', error.message);
    return res.status(500).json('Authentication failed');
  }
});
```

### With SimpleOAuth2Github

```js
const simpleOAuth2Github = require('@jimmycode/simple-oauth2-github');

const github = simpleOAuth2Github.create({
  clientId: process.env.GITHUB_CLIENT_ID,
  clientSecret: process.env.GITHUB_CLIENT_SECRET,
  callbackURL: 'http://localhost:3000/auth/github/callback'
});

// Ask the user to authorize.
router.get('/auth/github', github.authorize);

// Exchange the token for the access token.
router.get('/auth/github/callback', github.accessToken, (req, res) => {
  return res.status(200).json(req.token);
});
```
