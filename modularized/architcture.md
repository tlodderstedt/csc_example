# Architecture

TBD

## Error Response {#error_response}

### Error responses

#### Authentication required
If the client authentication fails, the authorization server shall return `401 Unauthorized` HTTP error response.

#### Authorization required
If the client is not authorized to perform the authorization request it wanted to post, the authorization server shall return `403 Forbidden` HTTP error response.

#### Invalid request
If the request object received is invalid, the authorization server shall return `400 Bad Request` HTTP error response.

#### Method not allowed
If the request did not use POST, the authorization server shall return `405 Method Not Allowed` HTTP error response.

#### Request entity too large
If the request size was beyond the upper bound that the authorization server allows, the authorization server shall return a `413 Request Entity Too Large` HTTP error response.

#### Too many requests
If the request from the client per a time period goes beyond the number the authorization server allows, the authorization server shall return a `429 Too Many Requests` HTTP error response.

# "request" Parameter {#request_parameter}

Clients MAY use the `request` parameter as defined in JAR to push a request object to the AS. The rules for signing and encryption of the request object as defined in JAR [@!I-D.ietf-oauth-jwsreq] apply.  

Clients MUST NOT combine other authorization request parameters with the `request` parameter at the pushed authorization request endpoint other than the `client_id` parameter which may be a part of the client authentication mechanism.

The following is an example of a request using a signed request object. The client is authenticated using its client secret `BASIC` authorization:

```
  POST /as/par HTTP/1.1
  Host: as.example.com
  Content-Type: application/x-www-form-urlencoded
  Authorization: Basic czZCaGRSa3F0Mzo3RmpmcDBaQnIxS3REUmJuZlZkbUl3

  request=eyJraWQiOiJrMmJkYyIsImFsZyI6IlJTMjU2In0.eyJpc3MiOiJzNkJoZ
  FJrcXQzIiwiYXVkIjoiaHR0cHM6Ly9zZXJ2ZXIuZXhhbXBsZS5jb20iLCJyZXNwb2
  5zZV90eXBlIjoiY29kZSIsImNsaWVudF9pZCI6InM2QmhkUmtxdDMiLCJyZWRpcmV
  jdF91cmkiOiJodHRwczovL2NsaWVudC5leGFtcGxlLm9yZy9jYiIsInNjb3BlIjoi
  YWlzIiwic3RhdGUiOiJhZjBpZmpzbGRraiIsImNvZGVfY2hhbGxlbmdlIjoiSzItb
  HRjODNhY2M0aDBjOXc2RVNDX3JFTVRKM2J3dy11Q0hhb2VLMXQ4VSIsImNvZGVfY2
  hhbGxlbmdlX21ldGhvZCI6IlMyNTYifQ.O49ffUxRPdNkN3TRYDvbEYVr1CeAL64u
  W4FenV3n9WlaFIRHeFblzv-wlEtMm8-tusGxeE9z3ek6FxkhvvLEqEpjthXnyXqqy
  Jfq3k9GSf5ay74ml_0D6lHE1hy-kVWg7SgoPQ-GB1xQ9NRhF3EKS7UZIrUHbFUCF0
  MsRLbmtIvaLYbQH_Ef3UkDLOGiU7exhVFTPeyQUTM9FF-u3K-zX-FO05_brYxNGLh
  VkO1G8MjqQnn2HpAzlBd5179WTzTYhKmhTiwzH-qlBBI_9GLJmE3KOipko9TfSpa2
  6H4JOlMyfZFl0PCJwkByS0xZFJ2sTo3Gkk488RQohhgt1I0onw
```

The AS needs to take the following steps beyond the processing rules defined in (#request):

1. If applicable, the AS decrypts the request object as specified in JAR [@!I-D.ietf-oauth-jwsreq], section 6.1.
1. The AS validates the request object signature as specified in JAR [@!I-D.ietf-oauth-jwsreq], section 6.2.
1. If the client is a confidential client, the authorization server MUST check whether the authenticated `client_id` matches the `client_id` claim in the request object. If they do not match, the authorization server MUST refuse to process the request. It is at the authorization server's discretion to require the `iss` claim to match the `client_id` as well.

## Error responses for Request Object
This section gives the error responses that go beyond the basic (#error_response).

### Authentication Required
If the signature validation fails, the authorization server shall return `401 Unauthorized` HTTP error response. The same applies if the `client_id` or, if applicable, the `iss` claims in the request object do not match the authenticated `client_id`.

# Authorization Request

The client uses the `request_uri` value as returned by the authorization server as authorization request parameter `request_uri`.

```
  GET /authorize?request_uri=
    urn%3Aexample%3Abwc4JK-ESC0w8acc191e-Y1LTC2 HTTP/1.1
```
Clients are encouraged to use the request URI as the only parameter in order to use the integrity and authenticity provided by the pushed authorization request.

# Authorization Server Metadata

If the authorization server has a pushed authorization request endpoint, it SHOULD include the following OAuth/OpenID Provider Metadata parameter in discovery responses:

`pushed_authorization_request_endpoint` : The URL of the pushed authorization request endpoint at which the client can exchange a request object for a request URI.
