# Simple OAuth2 Github

This library is a wrapper around [Simple OAuth2 Library](https://github.com/lelylan/simple-oauth2)

Specially made for [Authorization Code Flow](https://tools.ietf.org/html/draft-ietf-oauth-v2-31#section-4.1) with GitHub.

## Requirements

Latest Node 8 LTS or newer versions.

## Getting started

```
npm install --save simple-oauth2 simple-oauth2-github
```

or 

```
yarn add simple-oauth2 simple-oauth2-github
```

### Usage

```js
const simpleOAuth2Github = require('simple-oauth2-github');
const github = simpleOAuth2Github.create(options);
```

`github` object exposes 3 keys:
* authorize: Middleware to request user's authorization.
* getToken: Middleware for callback processing and exchange the authorization token for an `access_token`
* oauth2: The underlying [simple-oauth2](https://github.com/lelylan/simple-oauth2) instance.

### Options

SimpleOAuth2Github comes with default values for most of the options.

## Example

### Original boilerplate
### With SimpleOAuth2Github
