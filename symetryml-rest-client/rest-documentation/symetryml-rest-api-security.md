# SymetryML REST API Security

## SymetryML REST Request Authentication

The SymetryML REST API allows you to authenticate your requests. The API’s authentication is based on a "_message-authentication code_" signature created using specific parts of the REST API HTTP request.

Message authentication code (MAC) provides a way to verify the integrity of information sent in a REST request based on a secret key known only to the request sender and receiver. Because only two parties know the secret key, validating the MAC signature also authenticates the corresponding REST request. For this reason, each SymetryML API account has a secret key associated with its SymetryML key ID that is used to create a MAC signature for each REST request. The SymetryML server uses this secret key to verify signatures and authenticate all requests.

## Authentication Header

The SymetryML REST API uses the HTTP Authorization header to pass authentication information to the server. The header contains the information in the following table.

The most common MAC algorithms are Hash-based Message Authentication Code-Message Data 5 (HMAC-MD5) and HMAC-SHA2. The algorithm used in the SymetryML REST API is HMAC-SHA2, as specified in RFC 4868. For examples of how to sign your REST request, see [Appendix B](https://github.com/symetryml/smldocgb/blob/master/symetry-rest/rest-documentation/appendix-b-sample-code.md).

| Header                    | Description                                                                                                                                                                                                                                     |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Authorization**         | Signature                                                                                                                                                                                                                                       |
| **Signature**             | `Base64( HMAC-SHA2(UTF-8-Encoding-Of( StringToSign ) ) );`                                                                                                                                                                                      |
| **StringToSign**          | `HTTP-Verb + "\n" Content-MD5 + "\n" SymetryML-Secret-Key + "\n" Sym-Date Header + "\n" SymetryML-customer-Id + "\n" RequestBody + "\n" CanonicalizedResource + "\n" URLQueryParameters + "\n";`                                                |
| **Sym-Date format**       | See the section covering [Date Header](symetryml-rest-api-security.md#symetryml-date-header).                                                                                                                                                   |
| **CanonicalizedResource** | \<HTTP-Request-URI, from the protocol name up to the query string>                                                                                                                                                                              |
| **URLQueryParameters**    | Any URL query parameters used in the REST request. If no query parameters are present, do not add null or a new line "\n".                                                                                                                      |
| **RequestBody**           | JSON request body. If there is no request body, omit this along with the newline ("\n") that follows it.                                                                                                                                        |
| **sym-client**            | This parameter is not mandatory and not used in computing the string to sign, but is used when compiling logging statistics about the data source and streaming API. For more details please consult the [DS Log](data-source-logs.md) section. |

## SymetryML Date Header

Each SymetryML REST request must contain a SymetryML Date header. The content of this header is a date and time based on the Universal Coordinated Time/Greenwich Mean Time (UTC/GMT) time zone. This header is called `sym-date`.

The SymetryML Date header must:

* Follow this syntax:

```
yyyy-MM-dd HH:mm:ss;nanoseconds
```

For example:

```
2014-07-31 08:01:07;1245
```

* Before 5 minutes of the server time.
* Not be more than 1 minute ahead of the server time.

## Authentication Errors

If an authentication error occurs, the server returns a 401 (UNAUTHORIZED) response. The response contains a `stringToSign` entity that describes the string that the server was trying to validate when the error occurred. You can use this string to troubleshoot the request and identify the validation issue.

> **Note**
>
> The server omits the secret key from the response and outputs SECRETKEY in stringToSign.

The following shows an example of an unauthorized response. .

```javascript
{
      "statusCode":"UNAUTHORIZED",
      "statusString":"Invalid Signature",
      "values":{
            "stringToSign":"DELETE\\n\\nSECRETKEY\\n2013-05-22 18:13:38\\nc1\\nhttp://192.168.0.19:8080/symetry/rest/c1/sYMETRYMLs/r1\\n"
      }
}
```

## Authentication Error Codes

To help you identify and resolve authentication issues quickly, the SymetryML REST API supports a variety of error codes. The following table describes the supported error codes.

| API Error Codes                                                       |           |                                                                                                                                                                                                    |
| --------------------------------------------------------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Error Code                                                            | HTTP Code | Description                                                                                                                                                                                        |
| **Md5 do not match**                                                  | 400       | MD5 of the request body is invalid.                                                                                                                                                                |
| **Invalid User**                                                      | 401       | Server does not know this user.                                                                                                                                                                    |
| **Invalid Signature**                                                 | 401       | Authentication header signature is invalid.                                                                                                                                                        |
| **Authentication header is null**                                     | 400       | Authentication header is missing from the request.                                                                                                                                                 |
| **sym-date header is null**                                           | 400       | SymetryML date header is missing from the request.                                                                                                                                                 |
| **Please update your server time, it is likely out of sync with UTC** | 400       | Date in the request is more than 5 minutes before the server time or more than 1 minute after it. Check the time configuration on your client.                                                     |
| **Invalid Date Format**                                               | 400       | Format of the SymetryML date header is invalid. See the section [SymetryML Date Header](symetryml-rest-api-security.md#symetryml-date-header) for information about the appropriate format to use. |

## Single Sign-On (SSO) Authentication

{% hint style="info" %}
**SymetryML 6.2.0 and later:** SSO authentication is available as an alternative authentication method to the signature-based authentication described above.
{% endhint %}

SymetryML supports Single Sign-On (SSO) authentication, allowing users to authenticate using enterprise identity providers instead of managing SymetryML-specific credentials. When SSO is enabled, users authenticate through their organization's identity provider and then obtain SymetryML credentials (customer ID and secret key) to use with the REST API.

### Supported Authentication Protocols

SymetryML supports two SSO authentication protocols:

1. **OIDC (OpenID Connect)** - Works with identity providers such as:
   - Auth0
   - AWS Cognito
   - Okta
   - Any OpenID Connect compliant provider

2. **LDAP (Lightweight Directory Access Protocol)** - Works with directory services such as:
   - AWS Simple AD
   - Active Directory
   - OpenLDAP
   - Any LDAP v3 compliant directory

### SSO Authentication Flow

When SSO is enabled, the authentication flow works as follows:

1. User authenticates through the identity provider (Auth0, AWS Cognito, LDAP, etc.)
2. After successful authentication, user calls the `/smlaccess` endpoint
3. SymetryML returns the user's customer ID and secret key
4. User uses these credentials to make authenticated REST API calls using the standard signature-based authentication

### Get SML Credentials After SSO Login

After successfully authenticating through your SSO provider, use this endpoint to retrieve your SymetryML credentials.

#### URL

```
GET /smlaccess
```

#### Authentication

This endpoint requires that the user has successfully authenticated through the configured SSO provider. The authentication is handled by pac4j session management.

#### HTTP Responses

| HTTP Status Code | HTTP Status Message   | Description                                                      |
| ---------------- | --------------------- | ---------------------------------------------------------------- |
| **200**          | OK                    | Success. Returns SymetryML credentials.                          |
| **400**          | BAD REQUEST           | User profile not found or user does not exist in SymetryML.      |
| **500**          | INTERNAL SERVER ERROR | Server error occurred while retrieving credentials.              |

#### HTTP Response Entity

The response contains a JSON object with the following structure:

```json
{
  "statusCode": "OK",
  "statusString": "OK",
  "values": {
    "smlCredentials": {
      "smlusername": "<customer-id>",
      "smlusersecretkey": "<secret-key>"
    }
  }
}
```

| Field                   | Description                                                                     |
| ----------------------- | ------------------------------------------------------------------------------- |
| **smlusername**         | The SymetryML customer ID to use in REST API calls                              |
| **smlusersecretkey**    | The SymetryML secret key to use for signing REST API requests                   |

#### Sample Request/Response

```
Request:
GET https://your-server.com/smlaccess

Response:
{
  "statusCode": "OK",
  "statusString": "OK",
  "values": {
    "smlCredentials": {
      "smlusername": "john.doe",
      "smlusersecretkey": "aB3dE6fG9hI0jK1lM2nO3pQ4rS5tU6vW=="
    }
  }
}
```

### Using SSO Credentials with the REST API

Once you have obtained your SymetryML credentials from the `/smlaccess` endpoint:

1. Use the **smlusername** as your customer ID (`{cid}`) in REST API URLs
2. Use the **smlusersecretkey** as your secret key for signing requests (see [Authentication Header](#authentication-header))
3. All subsequent REST API calls should use the standard signature-based authentication method

### SSO Configuration

SSO must be enabled and configured by the SymetryML administrator. Configuration includes:

#### General Settings

| Setting                                      | Description                                                                      |
| -------------------------------------------- | -------------------------------------------------------------------------------- |
| **sym.sso.enable**                           | Enable or disable SSO functionality (set in start.ini)                           |
| **rtlm.option.sso.authentication.protocol**  | Authentication protocol: `OIDC` or `LDAP`                                        |
| **rtlm.option.sso.callback.handler.url**     | URL that receives the callback redirect after successful authentication          |
| **rtlm.option.sso.callback.finish.url**      | URL that receives the redirect after session creation                            |
| **rtlm.option.sso.logout.finish.url**        | URL that receives the redirect after logout                                      |
| **rtlm.option.sso.create.sml.user.if.missing** | Boolean (0 or 1) to automatically create SymetryML users if they don't exist   |

#### OIDC-Specific Settings

For OpenID Connect providers (Auth0, AWS Cognito, etc.):

| Setting                                            | Description                                                  |
| -------------------------------------------------- | ------------------------------------------------------------ |
| **rtlm.option.sso.oidc.client.id**                 | OIDC Client ID                                               |
| **rtlm.option.sso.oidc.client.secret**             | OIDC Client Secret                                           |
| **rtlm.option.sso.oidc.discovery.url**             | OIDC Discovery URL (e.g., `/.well-known/openid-configuration`) |
| **rtlm.option.sso.oidc.user.profile.name.attribute** | User profile attribute to use as SymetryML customer name   |
| **rtlm.option.sso.oidc.user.profile.access.scopes**  | OAuth scopes to request (e.g., `openid profile email`)    |
| **rtlm.option.sso.oidc.config.custom.params.json**  | Optional JSON object with custom parameters (e.g., audience) |
| **rtlm.option.sso.oidc.token.debugging.enable**    | Boolean (0 or 1) to enable token debugging in logs          |

#### LDAP-Specific Settings

For LDAP providers (AWS Simple AD, Active Directory, etc.):

| Setting                                                      | Description                                            |
| ------------------------------------------------------------ | ------------------------------------------------------ |
| **rtlm.option.sso.ldap.url**                                 | LDAP server URL (e.g., `ldap://your-server:389`)      |
| **rtlm.option.sso.ldap.search.dn.resolver.connection.bind.dn** | Distinguished Name (DN) for administrator bind       |
| **rtlm.option.sso.ldap.search.dn.resolver.connection.bind.password** | Password for administrator bind                  |
| **rtlm.option.sso.ldap.search.dn.resolver.connection.start.tls.enable** | Boolean (0 or 1) to enable TLS              |
| **rtlm.option.sso.ldap.search.base.dn**                      | Base DN for user searches                              |
| **rtlm.option.sso.ldap.search.filter**                       | LDAP search filter (e.g., `(cn={user})`)               |
| **rtlm.option.sso.ldap.search.result.user.attributes**       | User attributes to return (e.g., `name,mail`)          |
| **rtlm.option.sso.ldap.user.profile.name.attribute**         | User profile attribute to use as SymetryML customer name |
| **rtlm.option.sso.ldap.authentication.login.url**            | URL of the login form                                  |

### Example OIDC Configuration (Auth0)

```properties
rtlm.option.sso.authentication.protocol=OIDC
rtlm.option.sso.callback.handler.url=https://your-domain.com/sso/sso-callback-redirect.html
rtlm.option.sso.callback.finish.url=../../symetry-web/
rtlm.option.sso.logout.finish.url=https://your-domain.com/sso/sso-logged-out-redirect.html
rtlm.option.sso.create.sml.user.if.missing=1
rtlm.option.sso.oidc.client.id=YOUR_CLIENT_ID
rtlm.option.sso.oidc.client.secret=YOUR_CLIENT_SECRET
rtlm.option.sso.oidc.discovery.url=https://your-tenant.auth0.com/.well-known/openid-configuration
rtlm.option.sso.oidc.user.profile.name.attribute=email
rtlm.option.sso.oidc.user.profile.access.scopes=openid profile email
```

### Example LDAP Configuration (AWS Simple AD)

```properties
rtlm.option.sso.authentication.protocol=LDAP
rtlm.option.sso.callback.handler.url=/symetry-web/sso-callback.html
rtlm.option.sso.callback.finish.url=../../symetry-web/
rtlm.option.sso.logout.finish.url=../../symetry-web/sso-logged-out.html
rtlm.option.sso.create.sml.user.if.missing=1
rtlm.option.sso.ldap.url=ldap://your-ad-server.example.com
rtlm.option.sso.ldap.search.dn.resolver.connection.bind.dn=cn=Administrator,cn=Users,dc=example,dc=com
rtlm.option.sso.ldap.search.dn.resolver.connection.bind.password=YOUR_PASSWORD
rtlm.option.sso.ldap.search.base.dn=cn=Users,dc=example,dc=com
rtlm.option.sso.ldap.search.filter=(cn={user})
rtlm.option.sso.ldap.user.profile.name.attribute=name
rtlm.option.sso.ldap.authentication.login.url=/symetry-web/sso-ldap-login.html
```

### Auto-User Creation

When `rtlm.option.sso.create.sml.user.if.missing` is set to `1`, SymetryML will automatically create a new user account and secret key when a user successfully authenticates through SSO but does not yet have a SymetryML account. This is useful for:

- Streamlining user onboarding
- Allowing any authenticated enterprise user to access SymetryML
- Reducing administrative overhead

When set to `0`, only pre-existing SymetryML users can authenticate via SSO.

### Security Considerations

- SSO credentials (secret keys) are generated automatically and should be treated with the same security as manually-created credentials
- The `/smlaccess` endpoint relies on session-based authentication managed by pac4j
- All subsequent REST API calls after obtaining credentials still require proper HMAC-SHA2 signature authentication
- Secret keys can be rotated using the standard SymetryML admin tools

## APIs at-a-Glance

Please note that Every REST request that is made starts with the following canonical URL:

```
HTTPVERB /symetry/rest/{cid}
```

where:

* `{cid}` is the customer ID.
* HTTP VERB is a valid HTTP verb such as `GET`, `PUT`, `POST`, or `DELETE`.
