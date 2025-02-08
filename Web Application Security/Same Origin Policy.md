# Same Origin Policy

The same-origin policy is a critical security mechanism that restricts how a document or script loaded by one origin can interact with a resource from another origin.  It restricts scripts on one origin from accessing data from another origin.

The same-origin policy aims to prevent websites from attacking each other and helps isolate potentially malicious documents, reducing possible attack vectors. 

An origin consists of a URI scheme, domain and port number. \
Ex: http://www.example.com:8080/\
Here `http` is the scheme, `www.example.com` is the hostname or domain, and `8080` is the port number. 

* When no port is specified, the browser defaults to port 80 for http and port 443 for https. 
* The endpoints (such as /index.html/messages) are not part of the origin. 

Two URLs have the same origin if the protocol, port (if specified), and host are the same for both. The following table gives examples of origin comparisons with the URL http://store.company.com/dir/page.html:

| URL |	Outcome	| Reason |
|-----|---------|--------|
|http://store.company.com/dir2/other.html|Same origin|Only the path differs|
|http://store.company.com/dir/inner/another.html|	Same origin	|Only the path differs|
|https://store.company.com/page.html|	Failure|	Different protocol|
|http://store.company.com:81/dir/page.html|	Failure|	Different port (http:// is port 80 by default)|
|http://news.company.com/dir/page.html|	Failure	|Different host|



### Why is the same-origin policy necessary?
When a browser sends an HTTP request from one origin to another, any cookies, including authentication session cookies, relevant to the other domain are also sent as part of the request. This means that the response will be generated within the user's session, and include any relevant data that is specific to the user. Without the same-origin policy, if you visited a malicious website, it would be able to read your emails from Gmail, private messages  from Facebook, etc. 
Now if websites could not interact with each other, then the internet would be rather dull. How could any site interact with other sites ? How could google or facebook interact with each other and share data? This is where CORS comes into play.

### What is permitted and what is blocked?
Generally, embedding a cross-origin resource is permitted, while reading a cross-origin resource is blocked.

* iframes -	Cross-origin embedding is usually permitted (depending on the X-Frame-Options directive), but cross-origin reading (such as using JavaScript to access a document in an iframe) isn't.
* CSS	- Cross-origin CSS can be embedded using a <link> element or an @import in a CSS file. The correct Content-Type header may be required.
* forms - Cross-origin URLs can be used as the action attribute value of form elements. A web application can write form data to a cross-origin destination.
* images - Embedding cross-origin images is permitted. However, reading cross-origin image data (such as retrieving binary data from a cross-origin image using JavaScript) is blocked.
* multimedia - Cross-origin video and audio can be embedded using <video> and <audio> elements.
script	Cross-origin scripts can be embedded; however, access to certain APIs (such as cross-origin fetch requests) might be blocked.

### How to prevent Clickjacking
An attack called "clickjacking" embeds a site in an iframe and overlays transparent buttons which link to a different destination. Users are tricked into thinking they are accessing your application while sending data to attackers.

To block other sites from embedding your site in an iframe, add a content security policy with `frame-ancestors` directive to the HTTP headers.

Alternatively, you can add X-Frame-Options to the HTTP headers.

### JavaScript Setting: document.domain
Sometimes the strict rules of Same-origin Policy can cause problems when sharing between sites under the same base domain name.  For example, if we have login.example.com, games.example.com, and calendar.example.com, how would we communicate between them when the full domain paths do not match?  It is possible to relax the Same-origin Policy a little for such a case.

Thanks to the the `document.domain` JavaScript setting, we can expand our domain restriction to allow everything up to the base domain.  If we set the following in our code …

`document.domain = "example.com";`

… then this tells the browser that everything up to example.com is considered within the same origin now, including login.example.com, games.example.com, and calendar.example.com.  Placing this in your JavaScript code is mandatory if these sites which to share their resources with one another.

However, as a caveat, this does not immediately mean that the login.example.com site can access the DOM of example.com.  In order to allow this, the site on example.com must also declare the same document.domain setting of “example.com”.

The document.domain JavaScript setting can relax Same-origin Policy restrictions on hostname (the sub-domain elements of the domain), however port and schema restrictions remain the same (but as mentioned previously, port does not apply to Internet Explorer).  If we use the example of iframes again, we can demonstrate this when attempting to reach the DOM of the iframe.

One crucial element to highlight here is that a domain cannot set its origin to a different domain.  For example, login.example.com cannot set its origin as example2.com.  The setting must match where it actually comes from.

## Same-origin Policy vs. Web 2.0
Same-origin Policy is only the basic construct since the earliest days of the world wide web.  Ever since then, the internet we know today has exploded into a rich ecosystem of cross-domain content, Content Delivery Networks, single-page design, liking and sharing, and more.  In order to support all this diversity and change, Same-origin Policy had to also expand and adapt. Simple DOM and JavaScript namespace control was no longer enough, and required expansion with XmlHTTPRequest, JSONP, XDomainRequest, and CORS.

#### XmlHTTPRequest
XmlHTTPRequest is an HTTP communication method (or API) that can be set on HTTP calls to make web applications richer.  It enables asynchronous communication between resources to avoid having to load the page new each time. In most cases where the content of the page you are viewing changes without loading a new page, such as while scrolling down – think like Facebook or Twitter – this is most often done via what is called an Asynchronous JavaScript and XML (AJAX) request using the X-Requested-With HTTP header set to XmlHTTPRequest. Given the potential dangers this can yield, XmlHTTPRequest is also an area where Same-origin Policy rules must be strictly applied.

As such, since the creation of the XmlHTTPRequest HTTP API, the requirements of Same-origin Policy apply in full.  Therefore:

* An XmlHTTPRequest call can be sent to a site in a different origin, but the reply cannot be read (It is important to note that since an XmlHTTPRequest call does allow data to be sent to a different origin, this does allow for the potential of Cross-Site Request Forgery attacks.)
* Responses can be read if the request URL is in the same origin
* Custom headers can be added only to a request made to the same origin

Obviously, the XmlHTTPRequest Same-origin Policy will pose a problem when using resources from different origins, just as it does with normal DOM access.  However, as we will demonstrate, this default behavior can be changed.

#### JSON Padding (JSONP)
Although you can make an asynchronous request to a different origin by using the XmlHTTPRequest object, you cannot read its response. So, how can we use the web services that make the internet so rich? How can we show the currency rates, weather forecasts, album lists, and many other things received from other sites by using asynchronous requests?

Under the principles of the Same-origin Policy, we know that scripts work in the context of the site on which the scripts were loaded. The only criteria to do this is that the file loaded should be a valid script file. When we combine this information and go back to the early 2000s where the JSON technologies developed, we run into JSONP.

JSON, or Javascript Object Notation, is both a data type and an output considered as valid executable JavaScript code.  If the calls we made to third parties return a result in the form of JSON dynamically, we can circumvent the Same-origin Policy limitation.

However, it’s not enough to have a response with the content-type application/json returned from the service. These results must be bound with a callback function named by the caller. For example, let’s say we have JavaScript code that makes a call to the following URL: `http://www.example.com/getAlbums?callback=foobarbaz`

The response returned may look something like this:

`foobarbaz([{"artist": "Michael Jackson", "album": "Black or White"}{"artist": "Beatles, The", "album": "Revolution"}]);`

Let us break this down a little bit.

1. We make a call to the resource `http://www.example.com/getAlbums` and set a callback name in the query string of foobarbaz
2. When the resource returns its results as a JSON object, they are encapsulated in a function named foobarbaz, as was defined in the query string callback setting

The values within this returned result are now available within the origin of the calling script.  Therefore, if this JavaScript call was made from `http://www.example2.com`, it is able to use this data even though the resource is in a different domain.

This does, of course, pose a security risk.  When the JSONP request is executed, JavaScript will assume that anything returned from that resource is trustworthy.  Therefore, you should validate that the site to which you make JSONP request is a trusted one. Do not forget that all code returned from that site will work in your users’ browser under your web site’s context.  Additionally, with the strong importance of trust of the JSONP endpoint, making requests through a secure HTTPS channel is also important to prevent manipulation that could occur in transmission. This would also require the whole web page also be under HTTPS, due to JavaScript’s heightened strictness on mixed content.

#### XDomainRequest and JSONP vs. CORS

With JSONP and XmlHTTPRequest, one may think we have all our bases covered.  We can perform asynchronous calls to other resources, and we can also do dynamic calls to external resources on a different domain.  So why, then, do we need XDomainRequest or CORS? And what are they?

If you only intend to support the simplicity of browsers before IE9, Opera 12, Firefox 12, and so forth, you can continue to use JSONP just fine.  However, with the possibilities Web 2.0 offers, JSONP has started to become incapable of living up to some aspirations that arose from developers and browser vendors to make Same-origin Policy’s cross-domain restriction a little more relaxed still, yet secure.  The most essential reason was that cross-domain requests made with JSONP are only one-way, read only. Requests with the opportunity to write were still prevented by Same-origin Policy applied to JSONP. JSONP was also sort of a hacked solution to prior problems, so a more refined and properly designed approach was needed.

Vendors took into account these aspirations created a solution, albeit in two different ways. Microsoft had its own idea, and with Internet Explorer 8 and 9, XDomainRequest became their solution.  Whereas with Chrome, Firefox, and other browsers, they implemented a more popular alternative feature known as Cross-Origin Resource Sharing (CORS). Microsoft realized the potential and popularity of CORS and later adopted it in IE10 and beyond.  Even though there are two variations, the good news is that with only a few minor differences, CORS and XDomainRequest implementations are almost the same.

If we outline a CORS request, when a site in origin A wants to make a request to a site in origin B, first origin A must declare its origin in the request by setting a custom HTTP header named Origin. The site in origin B then returns a response with an HTTP header that defines the origins from which it allows CORS requests.  This header is the Access-Control-Allow-Origin HTTP header.

By Access-Control-Allow-Origin, it is possible to allow only one site. You can also use this to allow sub-domains, for example sub.example.com. Note that it is only possible to allow one FQDN at a time:
```
Access-Control-Allow-Origin: www.example.com
```

Permission can also be granted to all domains on the internet:
```
Access-Control-Allow-Origin: *
```
