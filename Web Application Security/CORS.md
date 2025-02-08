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

In the request, we perform our CORS Simple Request against cors.example.com from our site of `http://www.acceptmeplease.com`, also designating this as our origin.  The server responds approving our origin, thereby allowing the browser to continue with the request with Same-origin Policy domain restrictions relaxed.

### Preflight Request
A CORS request that does not fall under the restrictions for a Simple Request are considered Preflight Requests.  What this means is if the request method is not GET, POST, or HEAD; or if the request is POST but the Content-Type header is not one of application/x-www-form-urlencoded, multipart/form-data, or text/plain; or if a custom HTTP header is added to the request, then it must be validated against the server first.  Before the actual CORS request can be sent to the server, the browser sends a pre-flight check request using the OPTIONS method.  This is more expensive than a normal CORS Simple Request because two HTTP calls must be executed instead of just one.  While this additional cost may sound burdensome, it is necessary for the additional benefits CORS Preflight Requests provide that work around core behaviors of web calls, such as working in additional methods and headers.

To better understand this, let us evaluate an example CORS Preflight Request and server response.  Say we wish to send a normal CORS Simple Request-type POST request, but we are adding an additional HTTP header of X-Token-ID and a Content-Type header of application/xml.  This makes the request become a Preflight Request.  Thus, our CORS request sends an initial pre-flight check request to validate that this is acceptable to the server.  That pre-flight looks as follows:

```
OPTIONS /resources/post-here/ HTTP/1.1
Host: cors.example.com
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Connection: keep-alive
Origin: http://www.acceptmeplase.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-TOKEN-ID
```

The parts that are crucial to this CORS Preflight Request are - 
1. Notice first that the request method is OPTIONS, which asks the server if the HTTP headers sent are acceptable.
2. Next, we declare our origin, but there are now two additional headers defined: Access-Control-Request-Method and Access-Control-Request-Headers.  These are important because they tell the intentions of the browser in its following request, to send a POST method with an additional HTTP header defined.

The server, if accepting this OPTIONS request, would respond similar to this:
```
HTTP/1.1 200 OKDate: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache
Access-Control-Allow-Origin: http://www.acceptmeplease.com
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-TOKEN-ID
Access-Control-Max-Age: 86400
Vary: Accept-Encoding, Origin
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```
Important parts here are - first and foremost, the server must return a 200 response code, otherwise the Preflight Request is considered unacceptable.  Second, we have three additional HTTP headers that are returned on top of the normal Access-Control-Allow-Origin header:
1. Access-Control-Allow-Methods – These are the allowed request methods the browser may send.
2. Access-Control-Allow-Headers – These are the approved additional HTTP headers the browser may send.
3. Access-Control-Max-Age – If this HTTP header is set in the response, the server wants the browser to cache this OPTIONS result for the same kind of request.  This allows the browser to make similar requests to resources within this maximum age setting without needing to make an OPTIONS request before each of them.
   
The Access-Control-Max-Age is an important HTTP response header to be aware of.  In the example above, this value was set as 86,400 seconds, or 24 hours.  Each browser defines a maximum value for this field. If the maximum age limit is exceeded for the browser, it will ignore this value and instead substitute its maximum allowed value.  For example, this value in Chrome browsers is at most 10 minutes.  A glance at the Chrome source code explains that this was implemented to prevent cache poisoning.

Now that our pre-flight check has been completed via the OPTIONS request, our CORS request can continue as it would with a Simple Request.  The key differences, however, are that the Content-Type HTTP header change is now allowed, and the additional X-Token-ID HTTP header is also allowed.  Our follow-up CORS request from our browser will then look like the following:
```
POST /resources/post-here/ HTTP/1.1
Host: cors.example.com
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Connection: keep-alive
X-Token-ID: aabbccddeeff0011223344556677889900
Content-Type: application/xml; charset=UTF-8
Content-Length: 55
Origin: http://www.acceptmeplease.com
Pragma: no-cache
Cache-Control: no-cache

<?xml version="1.0"?>
...
```

Notice the two HTTP request headers X-Token-ID and Content-Type.  The X-Token-ID HTTP header is allowed to be sent, as well as the change to Content-Type.  The server should now respond similarly as it would with a normal CORS Simple Request.

#### Cookies
Thus far, we have demonstrated Cross-Origin Resource Sharing with various HTTP headers, but not the Cookie HTTP header – an element we may want to share.  By default, credentials used in the browser (including cookies, authentications, and certificates) are not sent along with a CORS request, Simple or Preflight.  It should also be noted that under XDomainRequest in IE8 and IE9, under no circumstance could any of these credential data be sent.

If, for example, we wish to pass along the cookie data accessible under our origin, we can simply pass along the contents of the cookie (without the metadata of domain, expiration, etc.) using the Cookie HTTP header in our CORS request.  This can be added to a Preflight Request OPTIONS request, but is not required.  In response, the server also must accept and acknowledge this credentialed request by returning the Access-Control-Allow-Credentials HTTP header, set to a ‘true’ value.  If this HTTP header is not received, the browser considers the entire request as failed.

### Implementations
We have explored many of the core concepts of Cross-Origin Resource Sharing, but only the raw HTTP requests and responses.  Obviously the browser will not be interacting with the web server on such a low level, and vice-versa. So how is CORS actually implemented?

On the client side, there is little necessary to implement a CORS request, Simple or Preflight.  This can be done via a simple JavaScript built-in library known as XMLHttpRequest.  It is named the same as the Same-origin Policy implementation we mentioned earlier, but it does also support CORS requests.  Here is a basic example:

```
// Declare the XMLHttpRequest object
var invocation = new XMLHttpRequest();

// We wish to open a POST method request
invocation.open('POST', 'http://cors.example.com/sendData, true);

// If we set this option, then in-browser credentials (cookies,
// authentication, certificates) will be sent along with the
// request
invocation.withCredentials = true;

// If we set the following two headers, as described previously,
// this will automatically become a CORS Preflight Request, and
// an OPTIONS method pre-flight check request will be done in
// the background, unless a matching one has already been done
// and was within the site's (and browser's) maximum age setting
invocation.setRequestHeader('X-TOKEN-ID', 'aabbccddeeff00112233');
invocation.setRequestHeader('Content-Type', 'application/xml');

// When the response is returned from the server, we must
// process it via a callback function
invocation.onreadystatechange = function(){ … };

// Send the POST content and initiate the request
invocation.send('…');
```

As you can see, in all actuality in the browser, a CORS request is quite simple.

On the server side, however, this can become far more complex, mainly due to the fact that there are a wide variety of languages, applications, and frameworks to choose from for servers.  For one excellent implementation example, though, you can refer to the Server Side Access Control document prepared by the Mozilla Developer Network.

### CORS on Security
The “Sharing” part of Cross-Origin Resource Sharing poses a security risk, both to the browser and the server.  For instance, the Access-Control-Allow-Origin HTTP header should never be set to * (all origins) unless the resource is truly intended to be publicly accessible.  Further, the server should take precaution when setting this HTTP header appropriately.  All too often, servers simply repeat back whatever the requesting browser set as its Origin HTTP header.  This level of blind trust by the server can pose a security risk.  Rather, if Access-Control-Allow-Origin is intended to be restrictive, then this should never be blindly trusted from the browser, and instead some server-side access control policy should be applied.

Origin headers cannot be changed after the fact in the browser (such as via JavaScript), however if the request is not made via a TLS-encrypted connection, it is possible to change these parameters via a man-in-the-middle attack.  Origin should not be a sole indicator of trust, and authentication should not be made by taking only origin into consideration. CORS requests, especially credentialed ones, should always be made via a TLS-encrypted connections. This will also prevent any potential mixed-content vulnerabilities (such as if your origin is over a TLS connection, but your CORS request is against a plain HTTP connection).

Even when the external origin is intended to be trusted, that trust scope should always be as highly restrictive as possible.  All activity here should be carefully filtered, as it could all too easily introduce a cross-site scripting (XSS) attack.  HTTP headers returned should also be scrutinized and given only as much trust as necessary.


