# Cross-Origin Resource Sharing (CORS)

With CORS, browsers have a work-around in order to get data from servers. It is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading of resources. CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource, in order to check that the server will permit the actual request. In that preflight, the browser sends headers that indicate the HTTP method and headers that will be used in the actual request. After the servers review the headers, the server will either accept the request and comply or reject the request and decline.

It extends and adds flexibility to the same-origin policy (SOP). However, it also provides potential for cross-domain based attacks, if a website's CORS policy is poorly configured and implemented. CORS is not a protection against cross-origin attacks such as cross-site request forgery (CSRF).

## Types of CORS

There are two valid request types for CORS requests – Simple Request and Preflight Request.  Most normal CORS requests will fall under the Simple Request category, consisting of typical HTTP headers and actions.  Preflight Requests, however, due to their atypical nature that falls outside the normal trusted scope of a Simple Request, require additional validation against the server.

### Simple Request
If the request contains one of the methods of GET, POST, or HEAD, and the message type is set by the Content-Type HTTP header to either application/x-www-form-urlencoded, multipart/form-data, or text/plain, this request is considered a CORS Simple Request and sent directly to the server.  The server will signify whether it accepts the CORS request by the returned Access-Control-Allow-Origin HTTP header.  If the server accepts, the response will be processed by the client.  There are also some additional HTTP headers that can be sent in the request that directly apply to the CORS request, including Accept (content types to accept), Accept-Language (languages the browser accepts), and Content-Language (the language the request is being sent in).

Let us evaluate an example CORS Simple Request and the server’s response.  An example request may look as follows:

```
GET / HTTP/1.1
Host: cors.example.com
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en,en-US;q=0.5
Origin: http://www.acceptmeplease.com
Connection: keep-alive
```

The server response would look like something similar to this:

```
HTTP/1.1 200 OK
Date: Sun, 24 Apr 2016 12:43:39 GMT
Server: Apache
Access-Control-Allow-Origin: http://www.acceptmeplease.com
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: application/xml
Content-Length: 423

<?xml version="1.0" encoding="UTF-8"?>
...
```

In the request, we perform our CORS Simple Request against cors.example.com from our site of http://www.acceptmeplease.com, also designating this as our origin.  The server responds approving our origin, thereby allowing the browser to continue with the request with Same-origin Policy domain restrictions relaxed.
