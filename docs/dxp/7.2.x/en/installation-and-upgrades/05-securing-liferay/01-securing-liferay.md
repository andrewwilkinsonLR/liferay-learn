---
header-id: securing-liferay
---

# Securing Liferay

Liferay makes every effort to keep its products secure. By following the OWASP Top 10 (2013) and CWE/SANS Top 25 recommendations, we protect the product against known attacks and security vulnerabilities. Here are some examples: 

- Service Builder protects Liferay Portal's persistence layer by using Hibernate and parameter-based queries. 
- To prevent Cross Site Scripting (XSS), user-submitted values are escaped on output. 
- Liferay Portal includes protection against other common attacks: 
  - CSRF
  - Local File Inclusion
  - Open Redirects
  - Uploading and serving files of dangerous types
  - Content Sniffing
  - Clickjacking
  - Path Traversal
  - Quadratic Blowup XXE
  - Rosetta Flash vulnerability
  - Reflected File Download
  - And more.

!!! qualifier "Note"
    Liferay Portal relies on the application server for sanitizing CRLF in HTTP headers. You should, therefore, make sure this is configured properly in your application server, or you may experience false positives in reports from automatic security verification software such as Veracode. There is one exception to this for Resin, which does not have support for this feature. In this case, Liferay Portal sanitizes HTTP headers.

When setting up Liferay Portal for use, you must configure various security and login features, such as LDAP, single sign-on, Service Access Policies, and more. 

## Authentication 

Users can authenticate in several ways: 

- Form authentication using the Sign In Portlet, an extensible app that by default can check and store credentials in the portal database and LDAP
- Using Single Sign-On (SSO) solutions such as Kerberos, Token-Based Authentication, or OpenID Connect
- Using Liferay's SAML plugin

You can authenticate and authorize applications remotely using [Authentication Verifiers](./04-using-auth-verifiers.md): 

- Password-based HTTP Basic + Digest authentication
- Token-based OAuth 2.0 plugin
- Portal session-based solution for JavaScript applications

User authentication and remote application authentication are [extensible](../../platform/frameworks/authentication-pipelines.md). You can create custom portlets for logging in, extend the default Sign In portlet authentication pipeline, create `AutoLogin` extensions for Single Sign-On, or create custom authentication verifier implementations. 

Once users are authenticated, additional measures protect access to data. 

## Authorization 

Liferay Portal contains several adjustable authorization layers: 

- Remote IP and HTTPS transport check to limit access to Liferay Portal's Java servlets
- Extensible Access Control Policies layer to perform portal service-related authorization checks
- Extensible Role-based permission framework for Liferay assets (stored in the database or elsewhere)
- Portlet container security checks that control portlet access
- Remote IP check for remote API authentication methods
- [Service Access Policies](./03-service-access-policies.md) to control access to remote APIs

Liferay Portal also has a robust permissions system that can be used in any deployed application. 

## Permission Checking

Users can be assigned to Sites, Teams, User Groups, or Organizations. Custom Roles can be created, permissions can be assigned to these Roles, and those Roles can be assigned to users. Roles are scoped to apply only in a specific context, such as a Site, Organization, or globally. See [Roles and Permissions](../user-and-system-management/roles-and-permissions.md) for more information.

There are additional security plugins available from [Liferay Marketplace](https://www.liferay.com/marketplace). For example, an Audit plugin tracks user actions, and an AntiSamy plugin helps clear HTML from XSS vectors. 

## Web Services 

Liferay Portal provides four security layers for web services: 

**IP permission layer:** The IP address from which a web service invocation request originates must be white-listed in the portal properties file. A web service invocation coming from a non-whitelisted IP address automatically fails.

**Service access policy layer:** Methods corresponding to a web service invocation request must be whitelisted by each service access policy that's in effect. You can use wildcards to reduce the number of service classes and methods that must be explicitly whitelisted.

**Authentication/verification layer (browser-only):** If a web service invocation request comes from a browser, the request must include an authentication token. This authentication token is the value of the `p_auth` URL parameter. The token is generated by @product@ and associated with your browser session. The `p_auth` parameter is automatically supplied when you invoke a @product@ web service via the JSON web services API page or via JavaScript using `Liferay.Service(...)`. If @product@ cannot associate the caller's authentication token with a User, the web service invocation request fails.

**User permission layer:** Properly implemented web services have permission checks. The user invoking a web service must have permission to invoke the service.

![Figure 1: To get to a service, a request must pass through the door lock of user permissions, the padlock of the verification layer, the brick wall of service access policies, and finally the safe of predefined IP permissions.](./images/service-access-policies-security-layers.png) 

## Fine-Tuning Security

There are many ways to fine-tune or disable various security features: 

- Disable the Sign In portlet's *Create Account* link
- Configure Liferay Portal's HTTPS web server address
- Configure the list of allowed servers to which users can be redirected
- Configure the list of portlets that can be accessed from any page
- Configure the file types allowed to be uploaded and downloaded. 

Please see [portal properties](https://docs.liferay.com/portal/7.2-latest/propertiesdoc/portal.properties.html) for more information. 

## Secure Configuration and Run Recommendations

Liferay Portal's philosophy is "secure by default." You shouldn't disable built-in protections or allow all values in security white-lists. Such acts may lead to security misconfiguration and an insecure deployment. 

For more information about securing a Liferay Portal installation, please see [our security statement](https://www.liferay.com/security), [the community security team](https://portal.liferay.dev/people/community-security-team), and the resources listed on those pages.

Customers are advised to deploy security patches when they are released. For community and CE deployments, stay secure by always using the latest community version, which contains all previous security patches. 