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

## APIs at-a-Glance

Please note that Every REST request that is made starts with the following canonical URL:

```
HTTPVERB /symetry/rest/{cid}
```

where:

* `{cid}` is the customer ID.
* HTTP VERB is a valid HTTP verb such as `GET`, `PUT`, `POST`, or `DELETE`.
