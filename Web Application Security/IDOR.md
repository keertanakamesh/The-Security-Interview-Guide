# Insecure direct object references (IDOR)

Insecure direct object references (IDOR) are a type of access control vulnerability that arises when an application uses user-supplied input to access objects directly. It is caused due to mistakes in the implementation of access control and can lead to access controls being circumvented. IDOR vulnerabilities are most commonly associated with horizontal privilege escalation, but they can also arise in relation to vertical privilege escalation.

## IDOR examples
There are many examples of access control vulnerabilities where user-controlled parameter values are used to access resources or functions directly.

### IDOR vulnerability with direct reference to database objects
Consider a website that uses the following URL to access the customer account page, by retrieving information from the back-end database:

`https://insecure-website.com/customer_account?customer_number=132355`

Here, the customer number is used directly as a record index in queries that are performed on the back-end database. If no other controls are in place, an attacker can simply modify the `customer_number` value, bypassing access controls to view the records of other customers. This is an example of an IDOR vulnerability leading to horizontal privilege escalation.

An attacker might be able to perform horizontal and vertical privilege escalation by altering the user to one with additional privileges while bypassing access controls. Other possibilities include exploiting password leakage or modifying parameters once the attacker has landed in the user's accounts page, for example.

### IDOR vulnerability with direct reference to static files
IDOR vulnerabilities often arise when sensitive resources are located in static files on the server-side filesystem. For example, a website might save chat message transcripts to disk using an incrementing filename, and allow users to retrieve these by visiting a URL like the following:

`https://insecure-website.com/static/12144.txt`

In this situation, an attacker can simply modify the filename to retrieve a transcript created by another user and potentially obtain user credentials and other sensitive data.

## Prevention

1. Enforce Proper Access Control (Authorization)

Implement strict role-based access control (RBAC) or attribute-based access control (ABAC) to verify who can access which resources.
NEVER trust user-controlled input for sensitive object references.

2. Use Indirect References (Object Indirection)

Instead of exposing incremental numeric IDs (e.g., user IDs, order IDs), use UUIDs, hashes, or tokens.
Example:
```
import uuid
user_id = str(uuid.uuid4())  # Generates a unique ID like '550e8400-e29b-41d4-a716-446655440000'
```
This prevents attackers from guessing object references.

3. Implement Secure Session-Based Validation

Ensure users can only access objects they own. This prevents users from accessing others' data.

4. Validate & Sanitize User Input

Prevent ID tampering by strictly validating incoming requests.
Avoid exposing object references in hidden fields, URLs, or cookies.

5. Implement Logging and Monitoring

* Log all access attempts to sensitive resources.
* Detect and alert on unusual object access patterns.
* Set up alerts for excessive 403 Forbidden responses or suspicious parameter changes.

6. Secure API Endpoints with Proper Authorization

Use JWT tokens or OAuth for API authentication. This ensures only authorized users access their data.
