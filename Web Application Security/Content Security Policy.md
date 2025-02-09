# Content Security Policy (CSP)

Content Security Policy (CSP) is an added layer of security that helps to detect and mitigate certain types of attacks, including Cross-Site Scripting (XSS) and data injection attacks. These attacks are used for everything from data theft, to site defacement, to malware distribution.

It consists of a series of instructions from a website to a browser, which instruct the browser to place restrictions on the things that the code comprising the site is allowed to do.

The primary use case for CSP is to control which resources, in particular JavaScript resources, a document is allowed to load. This is mainly used as a defense against cross-site scripting (XSS) attacks, in which an attacker is able to inject malicious code into the victim's site. A CSP can have other purposes as well, including defending against clickjacking and helping to ensure that a site's pages will be loaded over HTTPS.

CSP is designed to be fully backward compatible. Browsers that don't support it still work with servers that implement it, and vice-versa. If the site doesn't offer the CSP header, browsers likewise use the standard same-origin policy.

To enable CSP, you need to configure your web server to return the Content-Security-Policy HTTP response header. It should be set on all responses to all requests, not just the main document. You can also specify it using the http-equiv attribute of your document's <meta> element, and this is a useful option for some use cases, such as a client-side-rendered single page app which has only static resources, because you can then avoid relying on any server infrastructure. However, this option does not support all CSP features.
For example:

`Content-Security-Policy: policy` \
OR\
`<meta http-equiv="Content-Security-Policy"
      content="default-src 'self'; img-src https://*; child-src 'none';">`


The policy is specified as a series of directives, separated by semi-colons. Each directive controls a different aspect of the security policy. Each directive has a name, followed by a space, followed by a value. Different directives can have different syntaxes.

For example, consider the following CSP:
```
Content-Security-Policy: default-src 'self'; img-src 'self' example.com
```

It sets two directives:
* the default-src directive is set to 'self'
* the img-src directive is set to 'self' example.com.

The first directive, default-src, tells the browser to load only resources that are same-origin with the document, unless other more specific directives set a different policy for other resource types. The second, img-src, tells the browser to load images that are same-origin or that are served from example.com.

To block a resource type entirely, use the 'none' keyword. For example, the following directive blocks all <object> and <embed> resources:

```
Content-Security-Policy: object-src 'none'
```
Note that 'none' cannot be combined with any other method in a particular directive: in practice, if any other source expressions are given alongside 'none', then they are ignored.

https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP#controlling_resource_loading


### Mitigating cross-site scripting
A primary goal of CSP is to mitigate and report XSS attacks. XSS attacks exploit the browser's trust in the content received from the server. Malicious scripts are executed by the victim's browser because the browser trusts the source of the content, even when it's not coming from where it seems to be coming from.

CSP makes it possible for server administrators to reduce or eliminate the vectors by which XSS can occur by specifying the domains that the browser should consider to be valid sources of executable scripts. A CSP compatible browser will then only execute scripts loaded in source files received from those allowed domains, ignoring all other scripts (including inline scripts and event-handling HTML attributes).

As an ultimate form of protection, sites that want to never allow scripts to be executed can opt to globally disallow script execution.

### Mitigating packet sniffing attacks
In addition to restricting the domains from which content can be loaded, the server can specify which protocols are allowed to be used; for example (and ideally, from a security standpoint), a server can specify that all content must be loaded using HTTPS. A complete data transmission security strategy includes not only enforcing HTTPS for data transfer, but also marking all cookies with the secure attribute and providing automatic redirects from HTTP pages to their HTTPS counterparts. Sites may also use the Strict-Transport-Security HTTP header to ensure that browsers connect to them only over an encrypted channel.

### Using CSP
Configuring Content Security Policy involves adding the Content-Security-Policy HTTP header to a web page and giving it values to control what resources the user agent is allowed to load for that page. For example, a page that uploads and displays images could allow images from anywhere, but restrict a form action to a specific endpoint. A properly designed Content Security Policy helps protect a page against a cross-site scripting attack.
