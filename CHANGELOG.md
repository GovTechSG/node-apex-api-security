# Change Log

All notable changes to this project will be documented in this file.

This project adheres to [Semantic Versioning](http://semver.org/).

### V1.1.0

- Update to node engine v12.10.x
- Remove test suite dependency due to scalability issue

### V1.0.11

- Patch and update Lodash

### V1.0.10

- Fix vulnerable dependency and update mocha configs

### V1.0.9

- Fix vulnerable dependency , eslint and update license year

### V1.0.8

- Fix Object mutation issue #40 when ApiSigningUtil is called too many times with same object

### V1.0.7

- Update lodash dependency version to fix CVE-2019-10744
- Update Node engine to v10.16.x and associated test cases

### V1.0.6

- Fixed issue #34 by moving test suite data package to dev dependency.
- Bump node dependencies to latest

### V1.0.5

- Fixed Bug on signature URL when appending with standard HTTP/HTTPS Port

### V1.0.4

- Compiled for Node 10.x.x LTS
- Update Travis configuration

### V1.0.3

- npm commands for eslint

### V1.0.2

- Eslint config and linting for library

### V1.0.0

- Bump to version 1.0.0 major release candidate
- Removed deprecated interfaces getTokenFromSecret, getTokenFromCertFileName and getTokenFromCertString
- Update default params handling
- Code clean-up and improve coverage
- Add new test case that use standard test data
- Refresh and update dependencies

### V0.7.8

- Enhance BaseString handling and bug fixed
  - Add support to handle QuerySrting and FormData with null value i.e. { "queryString" : { "param1" : null, "param2" : "" }, "formData" : { "param3" : null, "param4" : "" } }
  - Add support to handle QuerySrting and FormData with empty string i.e. { "queryString" : { "param1" : null, "param2" : "" }, "formData" : { "param3" : null, "param4" : "" } }
  - Add support to handle QuerySrting and FormData with array values i.e. { "queryString" : { "param1" : [ "value1", "value2" ] }, "formData" : { "param3" : [ "value3", "value4" ] } }
  - Add support to handle QuerySrting and FormData with object values i.e. { "queryString" : { "param1" : { "subParam1" : "subvalue2" } }, "formData" : { "param3" : { "subParam3" : "subvalue3" } } }. Please note that param1 and param3 will be send as empty string, and the subvalue will be ignore.
  - Add support to handle URL QuerySrting with empty string value i.e. ?param1=&param2=value2&param3
  - Add support to handle URL QuerySrting with duplicate name parameters i.e. ?param1=&param2=value2&param1=another+item

### V0.7.7

- Remove nonce node library

### V0.7.6

- Update package.json to fix sub-dependency vulnerabilities

### V0.7.5

- Added logic to getSignatureBaseString to generate nonce and timestamp.

### V0.7.4

- Prepare for publishing and update package metadata

### V0.7.2

- Added default value for realm in getSignatureToken

### V0.7.1

- Add support to handle QueryString as parameter to the getSignatureToken API

### V0.7.0

- Remove ApiSecurityUtil and test class
- Refine Readme

### V0.6.1

- Remove unused validator dependency
- Add package-lock json

### V0.6.0

- Recompile for Node 8
- Update ApiSigningUtil URL interface
- Update ApiSigningUtil test specification

### V0.5.0

- Allows client to pass in request as an object for ApiSigningUtil
- Deprecate getToken interface for ApiSigningUtil
- Deprecate ApiSecurityUtil
- Update L1 and L2 to more representative method names

### V0.4.0

- Minor improvements and typo fix in index.js

### V0.4.1

- There are now 3 variants to getToken in ApiSigningUtil.js
- Typedefs has been added for ApiSigningUtil.js

### V0.4.2

- Export typing

### V0.3.0

- Update superagent version for security patch

### V0.2.0

- Additional interface , ApiSigningUtil, that take in parameters when forming RSA256 and HMAC256 security signature token

### V0.1.0

- Initial release with HMAC256 and RSA256 signing utility
