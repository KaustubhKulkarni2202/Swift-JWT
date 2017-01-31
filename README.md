# Kitura-JWT
An implementation of JSON Web Token

![Mac OS X](https://img.shields.io/badge/os-Mac%20OS%20X-green.svg?style=flat)
![Linux](https://img.shields.io/badge/os-linux-green.svg?style=flat)
![Apache 2](https://img.shields.io/badge/license-Apache2-blue.svg?style=flat)

## Summary
An implementation of [JSON Web Token](https://tools.ietf.org/html/rfc7519)

## Table of Contents
* [Prerequisites](#prerequisites)
* [Supported algorithms](#supported-algorithms)
* [Usage](#usage)
* [License](#license)


## Prerequisites

### macOS

* macOS 10.12.0 (*Sierra*) or higher

## Usage

Add

```swift
import KituraJWT
```
to your application.

### Check whether an algorithm is supported
The supported algorithms are listed in `Algorithm`. In order to check if an algorithm is supported and whether it requires a key or a secret, call:

``` swift
public static func isSupported(name: String) -> Supported
```
where `name` is a textual representation of an algorithm, e.g., "RS256" (case insensitive).

`Supported` is an enum with the following cases: unsupported, supportedWithKey (RSA falls into this category), and supportedWithSecret (HMAC, which is currently unsupported).


### Sign a JWT

The `sign` function encodes the header and claims of a JWT instance, creates a signature, and returns a String containing the signed JSON Web Token of the JWT instance.

For example:

```swift
let jwt = JWT(header: Header([.typ:"JWT"]), claims: Claims([.name:"Kitura"]))
let signedJWT = jwt.sign(using: .rs256(key, .privateKey))
```
`signedJWT` will be of the form:

```
<encoded header>.<encoded claims>.<signature>
```
Note, that the `sign` function sets the alg (algorithm) field of the header.

### Decode a JSON Web Token

The static function `JWT.decode` creates an instance of `JWT` for a String containing a JSON Web Token.

For example:

``` swift
let jwt = JWT.decode(encodedAndSignedJWT)
```
Note, that this function doesn't verify the signature of the token.

### Verify the signature of a JSON Web Token

The static function `JWT.verify` verifies the signature of a JSON Web Token given in String form.

For example:

```swift
if !JWT.verify(encodedAndSignedJWT, using: .rs512(key, .publicKey)) {
    print("Verification failed")
}
```

### Validate claims

The `validateClaims` function validates the claims of a JWT instance.
The following claims are validated if they are present in the `Claims` object:
  - iss (issuer)
  - aud (audience)
  - azp (authorized party)
  - at_hash (access token hash value)
  - exp (expiration date)
  - nbf (not before date)
  - iat (issued at date)

Various validations require an input. In these cases, if the claim in question exists and the input
is provided, the validation will be performed. Otherwise, the validation is skipped.

The method returns `ValidateClaimsResult` - an enum that list the various reasons for validation failure.
If the validation succeeds `ValidateClaimsResult.success` is returned.

```swift
let validationResult = validateClaims(issuer: issuer, audience: clientID, accessToken: accessToken)
if validationResult != .success {
  print("Claims validation failed: ", validationResult)
}
```


## License
This library is licensed under Apache 2.0. Full license text is available in [LICENSE](LICENSE.txt).
