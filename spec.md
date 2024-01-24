# Introduction

With the Single Sign-On (SSO) service, healthcare providers can have their professionals log in to a viewer or other application (from here on: Viewer) with identity characteristics from the healthcare information system or another system (from here on: XIS).
Using this SSO-specification, the XIS and the Viewer agree on a technical standard to make the authentication process from the professional to a viewer seamless.

# Requirements

The general idea of this specification is that a XIS creates a JSON Web Token (JWT) with a standard set of claims and submits this token to a SSO endpoint of the Viewer. After validating the JWT, the SSO-service of the Viewer will redirect the requesting XIS to the correct destination URL. This requires no further action from the XIS other than following the diversion in a timely manner.

The public RSA key of the XIS must be submitted to the Viewer vendor. The Viewer vendor then authorizes the XIS public key and allows the XIS to use the Viewer's SSO service endpoints. After this, requests can be made from the XIS to the SSO service of the Viewer.

> The key pair used to sign the JWT must be an RSA key pair. Acceptable key sizes are 2048-bit and 4096-bit RSA keys. The 4096-bit key size is recommended.

# JWT (JSON Web Token)

The data format used to submit the information required for the SSO connection is stored in a JWT. The format is a commonly used standard for exchanging signed JSON data. Documentation about JSON Web Tokens is available on the Wikipedia page (JSON Web Token).
In addition to the normal requirements for a signed JWT, the following requirement applies to the JWT signature:

>The algorithm used for the signature of the JWT must be either RS256 or RS512. RS512 is recommended.

# Payload of the JWT

The payload of the JWT represents the entirety of the authentication-related data that is sent from your partner system to HINQ in JSON format. The JWT payload consists of four groups of data:

1. General data of the JWT token
2. Data of the healthcare organisation to which the professional belongs
3. Data of the healthcare professional who initiates the SSO
4. Data of the patient to which the exchange of data applies

The table below documents all fields in the four groups. An example payload is available in the next section.

## Data in the JWT Payload

**field name**|**data group**|**explanation**|**type**|**cardinality**
:-----:|:-----:|:-----:|:-----:|:-----:
org-id|Healthcare organisation|The unique internal partner system identifier of the organization|string|1..1
org-ura|Healthcare organisation|The URA identifier of the organization|string|0..1
org-agb|Healthcare organisation|The AGB identifier of the organization|string|0..1
org-name|Healthcare organisation|The public name of the healthcare organisation|string|1..1
user-id|Healthcare professional|The unique internal partner system identifier of the healthcare professional |string|1..1
user-uzi|Healthcare professional|The UZI identifier of the healthcare professional|string|0..1
user-big|Healthcare professional|The BIG identifier of the healthcare professional|string|0..1
user-agb|Healthcare professional|The AGB identifier of the healthcare professional|string|0..1
user-given-name|Healthcare professional|The given (first) name(s) of the healthcare professional|string|1..1
user-family-name|Healthcare professional|The family (last) name of the healthcare professional|string|1..1
user-email|Healthcare professional|The work email of the healthcare professional. Must be unique to the user and not re-used across several different user accounts.|string|1..1
patient-bsn|Patient identification|The verified BSN of the patient|string|0..1
patient-id|Patient identification|A unique local identifier of the patient|string|0..1
patient-given-name|Patient identification|The given (first) name(s) of the patient|string|1..1
patient-family-name|Patient identification|The family (last) name of the patient|string|1..1

### Organisation identifiers

Accepted organisation identifiers are: URA code, AGB code and the internal partner system identifier. The internal partner system identifier is always required and must stay consistent with respect to an organization throughout all SSO-logins. The usage of global identifiers is strongly encouraged, and the two are listed in order of preference; the usage of an internal identifier only is discouraged.

Supplying a valid URA or AGB code for the organisation is the responsibility of the partner system. This information is used for logging and legal compliance.

The **Internal partner system identifier** option allows the partner system to define and use a custom organisation identifier. When this identifier is used, the partner system has to supply a list of internal identifiers and the organisation for each identifier in order for HINQ to relate this information to an AGB or URA code.

### Professional identifiers

Accepted professional identifiers are: UZI, BIG, AGB and internal partner system identifier. The global identifiers are listed in order of preference, and the unique internal partner system identifier claim is always mandatory as it is linked to a user in HINQ’s user store during the first use of the SSO, and subsequently used to uniquely identify the user in all further SSO logins. It is vital that the unique identifiers are kept consistent with regards to the users they represent throughout all SSO requests. Similarly, a user email must be unique to its user and not re-used across several different user accounts. The usage of global identifiers, in addition to the unique internal identifier, is strongly encouraged.

### Patient identifiers

When XIS and Viewer are allowed and certified to process BSN-identifiers, the only accepted patient identifier is the BSN. When XIS is not allowed or not certified to process BSN-identifiers, XIS vendor and Viewer vendor need to negotiate on the usage of another unique patient identifier.

# Example JWT and payload

The following is an example of an encoded JWT that adheres to the specification:

```json
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJ1cmwteGlzIiwianRpIjoiYzYxMWFlOWEtNmZmZi00MzQ4LWE0YjAtZDJmOWU2ODZiOTc2IiwiaWF0IjoxNTE2MjM5MDIyLCJleHAiOjE1MTYyNDI2MjIsImRlc3QiOiJodHRwczovL2FjYy5oaW5xLm5sL24vYW1vIiwib3JnLWlkIjoiMTIzNDUiLCJvcmctYWdiIjoiMTIzNDUiLCJvcmctdXJhIjoiMTIzNDUiLCJvcmctbmFtZSI6Ikdlem9uZGhlaWRzY2VudHJ1bSBEZSBLb25pbmciLCJ1c2VyLWlkIjoiTjMwNjNKRC1GWTdMLTIzRkc0MiIsInVzZXItdXppIjoiMTIzNDUiLCJ1c2VyLWJpZyI6IjEyMzQ1IiwidXNlci1hZ2IiOiIxMjM0NSIsInVzZXItZ2l2ZW4tbmFtZSI6IkpvaG4iLCJ1c2VyLWZhbWlseS1uYW1lIjoiRG9lIiwidXNlci1lbWFpbCI6ImpvaG5kb2VAZXhhbXBsZXpvcmcubmwiLCJwYXRpZW50LWJzbiI6IjEyMzQ1Njc4IiwicGF0aWVudC1naXZlbi1uYW1lIjoiSmFuZSIsInBhdGllbnQtZmFtaWx5LW5hbWUiOiJEb2UifQ.n6j7cMiVmy2Z2qbpb08wqymzEt73Akh7PGJia1FojvxVRCu2b9l9nfMIbowuCeMRsJ08IIVqmBG1WyJ6XiyVoiF7LsxBHIvLCsZL2DpjajU2PmeIaWGgFMnE4AKobRqeXdAulfR6rVGVKVlnSR2avwwpSRHS6zt2STDuByC8Yppi5mrFATrFfIG0g0ikSkiRpYuzB7yeBIMySQuemm7XY-qXv2ZEjtu74b99g4i5-CeQw-195PdkbzVCl7_ML6jLTss04c-eITh_g51ppllHnxd3U6r3y1mPfQfEiA7burFo1Lvg-jBObVd7fU5O3G8a9wK-QcKXHoxnLwOWvxbFow
```

The JWT can be decoded, which results in the structure below. You may decode this JWT online using jwt.io.

## Header

```json
{
  "alg": "RS256",
  "typ": "JWT"
}
```

## Payload

```json
{
  "iss": "url-xis",
  "jti": "c611ae9a-6fff-4348-a4b0-d2f9e686b976",
  "iat": 1516239022,
  "exp": 1516242622,
  "dest": "https://acc.hinq.nl/n/",
  "org-id": "12345",
  "org-agb": "12345",
  "org-ura": "12345",
  "org-name": "Gezondheidscentrum De Koning",
  "user-id": "N3063JD-FY7L-23FG42",
  "user-uzi": "12345",
  "user-big": "12345",
  "user-agb": "12345",
  "user-given-name": "John",
  "user-family-name": "Doe",
  "user-email": "johndoe@examplezorg.nl",
  "patient-bsn": "12345678",
  "patient-given-name": "Jane",
  "patient-family-name": "Doe"
}
```
>The actual issuer value `iss` will be communicated between XIS-vendor and Viewer-vendor.

>The actual destination value `dest` will be communicated individually to each partner system. Supported (OTAP) environments shoudl be communicated between XIS vendor and Viewer vendor.

# Making a request

Viewer vendor provides a SSO endpoint per XIS where a JWT should be posted to. If the JWT is successfully validated, Viewer will return an HTTP 302 redirect response, leading the user of XIS to the desired Viewer webpage. If unsuccessful, a different HTTP code and response will be served, detailing the problem.

## Environment endpoints

SSO environments endpoint urls should be communicated by Viewer vendor to XIS vendor. These should be made available to XIS vendor for making requests.

Examples:
- **Acceptance environment:** `https://acc.xyz....`
- **Production environment:** `https://prod.xyz....`

## Request format

The Viewer SSO endpoint expects an HTTP POST request in the following format:

```json
POST <PATH> HTTP/1.1

/* ...generic HTTP headers... */
Content-Type: application/x-www-form-urlencoded
/* ...generic HTTP headers... */

jwt=TOKEN_HERE
```

Example:

```json
POST /auth/realms/hinq/broker/<source-system>/endpoint/clients/zno-webapp/login HTTP/1.1
Host: login.acc.hinq.nl
Content-Type: application/x-www-form-urlencoded
Content-Length: 1030

jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJ1cmwteGlzIiwianRpIjoiYzYxMWFlOWEtNmZmZi00MzQ4LWE0YjAtZDJmOWU2ODZiOTc2IiwiaWF0IjoxNTE2MjM5MDIyLCJleHAiOjE1MTYyNDI2MjIsImRlc3QiOiJodHRwczovL2FjYy5oaW5xLm5sL24vYW1vIiwib3JnLWlkIjoiMTIzNDUiLCJvcmctYWdiIjoiMTIzNDUiLCJvcmctdXJhIjoiMTIzNDUiLCJvcmctbmFtZSI6Ikdlem9uZGhlaWRzY2VudHJ1bSBEZSBLb25pbmciLCJ1c2VyLWlkIjoiTjMwNjNKRC1GWTdMLTIzRkc0MiIsInVzZXItdXppIjoiMTIzNDUiLCJ1c2VyLWJpZyI6IjEyMzQ1IiwidXNlci1hZ2IiOiIxMjM0NSIsInVzZXItZ2l2ZW4tbmFtZSI6IkpvaG4iLCJ1c2VyLWZhbWlseS1uYW1lIjoiRG9lIiwidXNlci1lbWFpbCI6ImpvaG5kb2VAZXhhbXBsZXpvcmcubmwiLCJwYXRpZW50LWJzbiI6IjEyMzQ1Njc4IiwicGF0aWVudC1naXZlbi1uYW1lIjoiSmFuZSIsInBhdGllbnQtZmFtaWx5LW5hbWUiOiJEb2UifQ.n6j7cMiVmy2Z2qbpb08wqymzEt73Akh7PGJia1FojvxVRCu2b9l9nfMIbowuCeMRsJ08IIVqmBG1WyJ6XiyVoiF7LsxBHIvLCsZL2DpjajU2PmeIaWGgFMnE4AKobRqeXdAulfR6rVGVKVlnSR2avwwpSRHS6zt2STDuByC8Yppi5mrFATrFfIG0g0ikSkiRpYuzB7yeBIMySQuemm7XY-qXv2ZEjtu74b99g4i5-CeQw-195PdkbzVCl7_ML6jLTss04c-eITh_g51ppllHnxd3U6r3y1mPfQfEiA7burFo1Lvg-jBObVd7fU5O3G8a9wK-QcKXHoxnLwOWvxbFow
```
