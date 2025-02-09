# Cross-Site Scripting

Cross-site scripting is a JavaScript vulnerability. This occurs when a user enters a script in the input fields and the input is processed without being validated. This can lead to untrusted data being saved and executed upon on the client side. To mitigate this vulnerability, you can add input validation or implement a content security policy.

## Stored XSS (Persistent XSS)
The most damaging type of XSS is Stored XSS (Persistent XSS). An attacker uses Stored XSS to inject malicious content (referred to as the payload), most often JavaScript code, into the target application. If there is no input validation, this malicious code is permanently stored (persisted) by the target application, for example within a database. For example, an attacker may enter a malicious script into a user input field such as a blog comment field or in a forum post.
When a victim opens the affected web page in a browser, the XSS attack payload is served to the victim’s browser as part of the HTML code (just like a legitimate comment would). This means that victims will end up executing the malicious script once the page is viewed in their browser.

## Reflected XSS (Non-persistent XSS)
The second and the most common type of XSS is Reflected XSS (Non-persistent XSS). In this case, the attacker’s payload has to be a part of the request that is sent to the web server. It is then reflected back in such a way that the HTTP response includes the payload from the HTTP request. Attackers use malicious links, phishing emails, and other social engineering techniques to lure the victim into making a request to the server. The reflected XSS payload is then executed in the user’s browser.
Reflected XSS is not a persistent attack, so the attacker needs to deliver the payload to each victim. These attacks are often made using social networks.

A simple example of a reflected XSS attack could involve an attacker crafting up a URL that passes a small, malicious script as a query parameter to a website that has a search page vulnerable to XSS:
```
http://vulnerable-website.com/search?search_term=”<script>(bad things happen here)</script>”
```
The attacker then needs to have targets visit this URL from their web browsers. This could be accomplished by sending an email containing the URL (with plausible reason to trick the user into clicking it) or publishing the URL to a public, non-vulnerable website for targets to click.

When a target does click the link, the vulnerable site accepts the query parameter “search_term”, expecting that the value is something the target is interested in searching the vulnerable-website.com site for, when in reality the value is the malicious script.

The search page then, as most website search pages will do when a user is searching for something, displays `“Searching for <seach_term>...”`, but because the vulnerable site didn’t sanitize the search_term value, the malicious script is injected into the webpage that the target’s browser is loading and is then executed by the target’s browser.

## DOM-based XSS

Another type of XSS attack is DOM-based, where the vulnerability exists in the client-side scripts that the site/app always provides to visitors. This attack differs from reflected and persistent XSS attacks in that the site/app doesn’t directly serve up the malicious script to the target’s browser. In a DOM-based XSS attack, the site/app has vulnerable client-side scripts which deliver the malicious script to the target’s browser. It is possible if the web application’s client-side scripts write data provided by the user to the Document Object Model (DOM). The data is subsequently read from the DOM by the web application and outputted to the browser. If the data is incorrectly handled, an attacker can inject a payload, which will be stored as part of the DOM and executed when the data is read back from the DOM.
A DOM-based XSS attack is often a client-side attack and the malicious payload is never sent to the server. This makes it even more difficult to detect for Web Application Firewalls (WAFs) and security engineers who analyze server logs because they will never even see the attack. DOM objects that are most often manipulated include the URL (document.URL), the anchor part of the URL (location.hash), and the Referrer (document.referrer).

A simple example of a DOM-based XSS attack could involve the same setup for the reflected XSS example scenario above. The attacker creates a URL with a malicious script as the “search_term” and solicits it to potential targets.

Once a target clicks the URL, their browser loads the site search page and the vulnerable client-side processing scripts. While the “seach_term” is still provided as a query parameter to the site back end for processing, the site itself does not generate the web page with the injected malicious script.

Instead, the site’s vulnerable client-side scripts are designed to locally (in the target’s browser) dynamically substitute in the search term value (i.e. the malicious script) in the target’s rendered search page, causing the target’s browser to load and execute the attacker’s script.

DOM-based XSS attacks highlight the fact that XSS vulnerabilities aren’t limited to server-side software.

## Prevention

The following suggestions can help safeguard  users against XSS attacks:

#### Sanitize user input:
* Validate to catch potentially malicious user-provided input.
* Encode output to prevent potentially malicious user-provided data from triggering automatic load-and-execute behavior by a browser.

#### Limit use of user-provided data:
* Only use where it’s necessary.

#### Utilize the Content Security Policy:
* Provides additional levels of protection and mitigation against XSS attempts.
