# APEX API Node.js Security Utility
[![npm version](https://badge.fury.io/js/node-apex-api-security.svg)](https://badge.fury.io/js/node-apex-api-security)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/eb0547096e2d4693b8cd19a87977f14f)](https://www.codacy.com/app/GovTech/node-apex-api-security?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=GovTechSG/node-apex-api-security&amp;utm_campaign=Badge_Grade)
[![Build Status](https://travis-ci.com/GovTechSG/node-apex-api-security.svg?branch=master)](https://travis-ci.com/GovTechSG/node-apex-api-security)
[![Coverage Status](https://coveralls.io/repos/github/GovTechSG/node-apex-api-security/badge.svg?branch=master)](https://coveralls.io/github/GovTechSG/node-apex-api-security?branch=master)
[![Known Vulnerabilities](https://snyk.io/test/github/govtechsg/node-apex-api-security/badge.svg)](https://snyk.io/test/github/govtechsg/node-apex-api-security)
[![Open Source Love](https://badges.frapsoft.com/os/v1/open-source.svg?v=103)](https://github.com/ellerbrock/open-source-badges/)

A Javascript utility that generates HTTP security headers for authenticating with secured Apex endpoints, for Node.js.

## Table of Contents
- [Getting Started](#getting-started)
    * [API Usage](#api-usage)
    * [Security Signature Token Example](#security-signature-token-example)
- [Contributing](#contributing)
- [Release](#release)
- [License](#license)
- [References](#references)

## Getting Started

### Installation

```
$ npm install node-apex-api-security
```

### API Usage

**`ApiSigningUtil.getSignatureToken(options)`**

Returns a signature token used for authentication with a secured Apex API.

#### APEX L1 Secured API

Authorization token with **HMACSHA256** signature

```javascript
const ApiSigningUtil = require('node-apex-api-security').ApiSigningUtil;

// Required options for L1 authentication
const requestOptions = {
    appId: 'my-app-id',                     // Apex App ID
    secret: 'my-app-secret',                // Apex App secret used for L1 signature
    authPrefix: 'apex_l1_eg',               // Authentication prefix, determined by authentication level and gateway type
    httpMethod: 'get',                      // HTTP method, e.g. GET/POST
    urlPath: 'https://my.apex.api/endpoint' // URL to Apex API
};

// Apex_l1_ig realm="https://my.apex.api/endpoint",apex_l1_ig_app_id="my-app-id",apex_l1_ig_nonce="UldycUAF56GWJGlWz0YSwOOp5gruJqvBy0CJeZ4XpGk=",apex_l1_ig_signature="u5nTX4ZbkL8c9pp5C79VHu07QPPLG9yx2VxpLX7kqGM=",apex_l1_ig_signature_method="HMACSHA256",apex_l1_ig_timestamp="1523935422173",apex_l1_ig_version="1.0"
const L1SignatureToken = ApiSigningUtil.getSignatureToken(requestOptions);
```

#### APEX L2 Secured API 

Authorization token with **SHA256withRSA** signature

```javascript
const ApiSigningUtil = require('node-apex-api-security').ApiSigningUtil;

// Required options for L2 authentication
const requestOptions = {
    appId: 'my-app-id',                         // Apex App ID
    keyString: '----BEGIN PRIVATE KEY-----...', // The PEM formatted private key's string
    // keyFile: '/path/to/my/private.key',      // Alternatively, simply pass in the path to private key used for L2 signature
    authPrefix: 'apex_l2_eg',                   // Authentication prefix, determined by authentication level and gateway type
    httpMethod: 'get',                          // HTTP method, e.g. GET/POST
    urlPath: 'https://my.apex.api/endpoint'     // URL to Apex API
};

// Apex_l2_ig realm="https://my.apex.api/endpoint",apex_l2_ig_app_id="my-app-id",apex_l2_ig_nonce="UldycUAF56GWJGlWz0YSwOOp5gruJqvBy0CJeZ4XpGk=",apex_l2_ig_signature="u5nTX4ZbkL8c9pp5C79VHu07QPPLG9yx2VxpLX7kqGM=",apex_l2_ig_signature_method="SHA256withRSA",apex_l2_ig_timestamp="1523935422173",apex_l2_ig_version="1.0"
const L2SignatureToken = ApiSigningUtil.getSignatureToken(requestOptions);
```

The generated token should then be added to the `Authorization` header when making HTTP API calls:

```
GET /endpoint HTTP/1.1
Host: my.apex.api
Authorization: Apex_l1_ig realm="https://my.apex.api/endpoint",apex_l1_ig_app_id="my-app-id",apex_l1_ig_nonce="UldycUAF56GWJGlWz0YSwOOp5gruJqvBy0CJeZ4XpGk=",apex_l1_ig_signature="u5nTX4ZbkL8c9pp5C79VHu07QPPLG9yx2VxpLX7kqGM=",apex_l1_ig_signature_method="HMACSHA256",apex_l1_ig_timestamp="1523935422173",apex_l1_ig_version="1.0"
...
```

#### Core Options

- `appId`

Apex App ID. The App needs to be approved and activated by the API provider. This value can be obtained from the gateway portal.

```javascript
let appId = 'my-app-id';
```

- `authPrefix`

API gateway-specific authorization scheme for a specific gateway zone. Takes 1 of 4 possible values.
 
```javascript
let authPrefix = 'Apex_l1_ig'; 
// or
let authPrefix = 'Apex_l1_eg';
// or
let authPrefix = 'Apex_l2_ig';
// or
let authPrefix = 'Apex_l2_eg';
```

- `httpMethod`

 The API HTTP method
 
```javascript
let httpMethod = 'get';
```

- `urlPath`

The full API endpoint, for example https://my-apex-api.api.gov.sg/api/my/specific/data. 

**Note: Must be the endpoint URL as served from the Apex gateway, from the domain api.gov.sg. This may differ from the actual HTTP endpoint that you are calling, for example if it were behind a proxy with a different URL.**

**IMPORTANT NOTE from v0.7.8 onwards : If you are intending pass in the query params in optional parameters queryString or formData, please remove the queryString from the urlPath. Checkout the [optional](#optional-options) section**

```javascript
let urlPath = "https://my.apex.api/v1/resources?host=https%3A%2F%2Fnd-hello.api.example.comß&panelName=hello";
```

- `secret` - **Required for L1 signature**

If the API you are accessing is secured with an L1 policy, you need to provide the generated App secret that corresponds to the `appId` provided.

**Note: leave `secret` undefined if you are using ApiSigningUtil L2 RSA256 Signing**

```javascript
let secret = 's0m3S3ecreT'; 
```

- `keyString` *or* `keyFile` - **Required for L2 signature**
- (optional) `passphrase`

If the API you are access is secured with an L2 policy, you need to provide the private key corresponding to the public key uploaded for `appId`.

Provide *either* the path to your private key used to generate your L2 signature in `keyFile` or the actual contents in `keyString`.

```javascript
let keyFile = '/path/to/my/private.key';
// or
let keyString = '----BEGIN PRIVATE KEY ----\n ${private_key_contents} \n -----END PRIVATE KEY-----';
let passphrase = 'passphrase for the keyString';
```

#### Optional options

- `realm`

An identifier for the caller, this can be set to any value.

- `formData`

Object representation of form data (x-www-form-urlencoded) passed during HTTP POST / HTTP PUT requests

```javascript
//For Signature generation (do not need to be URL encoded)
let formData = {key : 'value'};
```

```javascript
//For making the actual HTTP POST call (need to be URL encoded)
let postData = qs.stringify(formData, null, null, {encodeURIComponent: encodeURIComponent});
let req = request(param.httpMethod, targetURL.href);
req.buffer(true);
req = req.type("application/x-www-form-urlencoded").set("Content-Length", Buffer.byteLength(postData)).send(postData);
```
**NOTE** 

For **formData** parameter used for Signature generation, the key value parameters **do not** need to be URL encoded, 
When your client program is making the actual HTTP POST call, the key value parameters **has** to be URL encoded   


- `queryString`

Object representation of URL query parameters, for the API.

**IMPORTANT NOTE** For version **v0.7.7** and below : You can also leave the query string on the urlPath parameter; it will automatically be extracted, and you won't have to use this parameter.

**IMPORTANT NOTE** From **v0.7.8** onwards : If you pass in the params in queryString or formData, please **remove** the queryString from the urlPath parameter

For example, the API endpoint is https://example.com/v1/api?key=value , then you have you pass in the params in this manner below :


```javascript
 // For example, if the endpoint contains a query string: https://api.example.com?abc=def&ghi=123
 let qsData = {
     abc: 'def',
     ghi: 123
 }
 
 //Prepare request options for signature formation
const requestOptions = {
    appId: 'my-app-id',                     // Apex App ID
    secret: 'my-app-secret',                // Apex App secret used for L1 signature
    authPrefix: 'apex_l1_eg',               // Authentication prefix, determined by authentication level and gateway type
    httpMethod: 'get',                      // HTTP method, e.g. GET/POST
    urlPath: 'https://api.example.com'      // URL that remove away the queryString
    queryString : qsData
};
```

- `nonce`

An arbitrary string, needs to be different after each successful API call. Defaults to 32 bytes random value encoded in base64.

- `timestamp`

A unix timestamp. Defaults to the current unix timestamp.

**Logging**

To see detailed logs while using ApiSigningUtil, set the log level to **trace**

```javascript
ApiSigningUtil.setLogLevel('trace');
```

### Security Signature Token Example

```
Authorization: Apex_l2_ig realm="http://api.mygateway.com",
apex_l2_ig_timestamp="1502199514462",
apex_l2_ig_nonce="UldycUAF56GWJGlWz0YSwOOp5gruJqvBy0CJeZ4XpGk=",
apex_l2_ig_app_id="my-apex-app-id",
apex_l2_ig_signature_method="SHA256withRSA",
apex_l2_ig_signature="Gigxd7Yif2NqiFGI3oi0D3+sVv3QxURLPwCSE9ARyeenYhipG+6gncCR+tWEfaQBGyH9gnG6RtwZh3A==",
apex_l2_ig_version="1.0"
```

## Contributing
For more information about contributing, and raising PRs or issues, see [CONTRIBUTING.md](https://github.com/GovTechSG/node-apex-api-security/blob/master/.github/CONTRIBUTING.md).

## Release
See [CHANGELOG.md](CHANGELOG.md).

## License
Licensed under the [MIT LICENSE ](https://github.com/GovTechSG/node-apex-api-security/blob/master/LICENSE)

## References
+ [Akana API Consumer Security](http://docs.akana.com/ag/cm_policies/using_api_consumer_app_sec_policy.htm)
+ [RSA and HMAC Request Signing Standard](http://tools.ietf.org/html/draft-cavage-http-signatures-05)
+ [Signature Token Validator](https://github.com/GovTechSG/apex-signature-validator)
