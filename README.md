# What is Cross Site Request ForgeryCSRF)?
Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform.
Cross-Site Request Forgery (CSRF) is an attack that forces an end user to execute unwanted actions on a web application in which they’re currently authenticated. With a little help from social engineering (such as sending a link via email or chat), an attacker may trick the users of a web application into executing actions of the attacker’s choosing.

![cross-site request forgery](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/62cf876e-5700-4091-84e1-616f14b3154e)

# How does CSRF work?

**For a CSRF attack to be possible, three key conditions must be in place:**

**1. A relevant action:** 
* There is an action within the application that the attacker has a reason to induce. This might be a privileged action (such as modifying permissions for other users) or any action on user-specific data (such as changing the user's own password).

**2. Cookie-based session handling:** 
* Performing the action involves issuing one or more HTTP requests, and the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.

**3. No unpredictable request parameters:** 
* The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess. For example, when causing a user to change their password, the function is not vulnerable if an attacker needs to know the value of the existing password.

**For example, suppose an application contains a function that lets the user change the email address on their account. When a user performs this action, they make an HTTP request like the following:** 

![csrf1](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/bb8b32e1-1c25-4698-ba1d-423321fd5524)

**This meets the conditions required for CSRF:**
* The action of changing the email address on a user's account is of interest to an attacker. Following this action, the attacker will typically be able to trigger a password reset and take full control of the user's account.
* The application uses a session cookie to identify which user issued the request. There are no other tokens or mechanisms in place to track user sessions.
* The attacker can easily determine the values of the request parameters that are needed to perform the action.

With these conditions in place, the attacker can construct a web page containing the following HTML: 

![csrf2](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/a112a236-5a04-4c7f-842d-742f894f9b0d)

**If a victim user visits the attacker's web page, the following will happen:**

* The attacker's page will trigger an HTTP request to the vulnerable web site.
* If the user is logged in to the vulnerable web site, their browser will automatically include their session cookie in the request (assuming SameSite cookies are not being used).
* The vulnerable web site will process the request in the normal way, treat it as having been made by the victim user, and change their email address.

# Common defences against CSRF

**Nowadays, successfully finding and exploiting CSRF vulnerabilities often involves bypassing anti-CSRF measures deployed by the target website, the victim's browser, or both. The most common defenses you'll encounter are as follows:**

**1. CSRF tokens:**

* A CSRF token is a unique, secret, and unpredictable value that is generated by the server-side application and shared with the client. When attempting to perform a sensitive action, such as submitting a form, the client must include the correct CSRF token in the request. This makes it very difficult for an attacker to construct a valid request on behalf of the victim.

**2. SameSite cookies:**

* SameSite is a browser security mechanism that determines when a website's cookies are included in requests originating from other websites. As requests to perform sensitive actions typically require an authenticated session cookie, the appropriate SameSite restrictions may prevent an attacker from triggering these actions cross-site.

**SameSite=None:**

* If a cookie is set with the SameSite=None attribute, this effectively disables SameSite restrictions altogether, regardless of the browser. As a result, browsers will send this cookie in all requests to the site that issued it, even those that were triggered by completely unrelated third-party sites. 

**SameSite=Lax:**

*  Lax SameSite restrictions mean that browsers will send the cookie in cross-site requests, but only if both of the following conditions are met:

* The request uses the GET method.

* The request resulted from a top-level navigation by the user, such as clicking on a link.

* This means that the cookie is not included in cross-site POST requests, for example. As POST requests are generally used to perform actions that modify data or state (at least according to best practice), they are much more likely to be the target of CSRF attacks. 

**SameSite=Strict:**

* If a cookie is set with the SameSite=Strict attribute, browsers will not send it in any cross-site requests. In simple terms, this means that if the target site for the request does not match the site currently shown in the browser's address bar, it will not include the cookie. 

**3. Referer-based validation:** 

* Some applications make use of the HTTP Referer header to attempt to defend against CSRF attacks, normally by verifying that the request originated from the application's own domain. This is generally less effective than CSRF token validation.

# What is the Impact of CSRF Attacks?

* In a successful CSRF attack, the attacker causes the victim user to carry out an action unintentionally. For example, this might be to change the email address on their account, to change their password, or to make a funds transfer. Depending on the nature of the action, the attacker might be able to gain full control over the user's account. If the compromised user has a privileged role within the application, then the attacker might be able to take full control of all the application's data and functionality. 

# Bypassing CSRF token validation

# 1. Common flaws in CSRF token validation

* Some applications correctly validate the token when the request uses the POST method but skip the validation when the GET method is used.
* In this situation, the attacker can switch to the GET method to bypass the validation.

![csrf3](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/f8b414f7-d128-40bd-8603-fb109f68891f)

**Testing CSRF**

1. Interect with functionality and intercept the request.
2. Send this requets to repeater and right click change request method
4. generate csrf poc and Edit according to your preference.
5. Deliver to the victim..

# 2. Validation of CSRF token depends on token being present

* Some applications correctly validate the token when it is present but skip the validation if the token is omitted.
* In this situation, the attacker can remove the entire parameter containing the token (not just its value) to bypass the validation.

![csrf4](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/ff8305ff-f3b4-4f0e-8981-4412dda4cae1)

**Testing CSRF**

1. Interect with functionality and intercept the request.
2. Send this requets to the repeater.
3. Remove the csrf entire parameter and right click to generate csrf poc
4. Edit according to your preference.
5. Deliver to the victim.

# 3. CSRF token is not tied to the user session

**Token Not Tied to User Session:**

* In some applications, the CSRF token is not specifically linked to the user's session. Instead, the application maintains a global pool of tokens. Any token from this pool is considered valid, regardless of the user session it is associated with.

**Attacker Obtaining a Valid Token:**

* An attacker, who has their own account on the application, can log in and obtain a valid CSRF token assigned to their session. Since the application accepts any valid token from its global pool, the attacker can then trick a victim user into submitting a request with this token.

**Testing CSRF**

1. Interect with functionality and intercept the request.
2. copy valied csrf token and Drop the request.
3. Right click generate csrf poc.
4. Edit according to your preference.
5. Deliver to the victim.

# 4. CSRF Token Tied to a Non-Session Cookie

* In some applications, the CSRF token is linked to a specific cookie, but not the same cookie used for tracking user sessions. This means that the token is associated with a separate cookie, often used for CSRF protection, distinct from the cookie that tracks user sessions.

***Example Request:**

* In the provided example HTTP request, the application uses two cookies: **one for session tracking (session cookie) and another for CSRF protection (csrfKey cookie). These cookies are sent with a request to change the email address (/email/change).**

![csrf5](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/59082034-84f6-431d-9990-b680c067afed)

**Vulnerability Explanation:**

* This scenario is harder to exploit compared to a situation where the CSRF token is not tied to any cookie at all, but it is still vulnerable. If the application has a mechanism that allows an attacker to set a cookie in a victim's browser, an attack becomes possible.

**Exploiting the Vulnerability:**

* The attacker, after logging into the application with their own account, obtains a valid CSRF token associated with the CSRF-specific cookie (csrfKey). They can then take advantage of any behavior in the application that allows them to set a cookie in the victim's browser. This could be a cookie-setting behavior that doesn't require any special permissions.

* The attacker sets their CSRF-specific cookie (csrfKey) in the victim's browser and then triggers a CSRF attack by feeding the valid CSRF token to the victim. Since the victim's browser now has the attacker's CSRF-specific cookie, the malicious request appears legitimate.

**Testing CSRF**

1. Interect with functionality and intercept the request.
2. Right click generate csrf poc.
3. Remove any cookie value.
4. Edit according to your preference.
5. Deliver to the victim.

# 5. CSRF token is simply duplicated in a cookie

* In a further variation on the preceding vulnerability, some applications do not maintain any server-side record of tokens that have been issued, but instead duplicate each token within a cookie and a request parameter. When the subsequent request is validated, the application simply verifies that the token submitted in the request parameter matches the value submitted in the cookie. This is sometimes called the "double submit" defense against CSRF, and is advocated because it is simple to implement and avoids the need for any server-side state.

![csrf6](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/c8e51fb7-8786-471a-8aa0-aa48220bd614)

* In this situation, the attacker can again perform a CSRF attack if the web site contains any cookie setting functionality. Here, the attacker doesn't need to obtain a valid token of their own. They simply invent a token (perhaps in the required format, if that is being checked), leverage the cookie-setting behavior to place their cookie into the victim's browser, and feed their token to the victim in their CSRF attack.

# 6. Bypassing Referer-based CSRF defenses

* Aside from defenses that employ CSRF tokens, some applications make use of the HTTP Referer header to attempt to defend against CSRF attacks, normally by verifying that the request originated from the application's own domain. This approach is generally less effective and is often subject to bypasses.
* 
**Validation of Referer can be circumvented**

* Some applications validate the Referer header in a naive way that can be bypassed. For example, if the application validates that the domain in the Referer starts with the expected value, then the attacker can place this as a subdomain of their own domain:
* http://vulnerable-website.com.attacker-website.com/csrf-attack
* Likewise, if the application simply validates that the Referer contains its own domain name, then the attacker can place the required value elsewhere in the URL:
* http://attacker-website.com/csrf-attack?vulnerable-website.com

**Testing CSRF**

1. Intercept the request and try changing referer to some other domain.
2. If that didn't work then you will have to suppress the refere header.
3. you can use `<meta name="referrer" content="no-referrer">` or any other technique.
4. Done, Make a normal POC with that technique.

# 7. Send null value in csrf token.

**Testing CSRF**

1. Interect with functionality and intercept the request.
2. Send this requets to repeater.
3. change the csrf token to add a null value and generate csrf poc.
4. Edit according to your preference,For example:
5. Done send this to victim.

# 8. Change CSRF value and add same lenght string

**Testing CSRF**

1. Interact with the functionality and intercept the request.
2. Send this request to the repeater.
3. Change the CSRF token value and add a string of the same length, then generate a CSRF proof of concept (POC).
4. Edit it according to your preference.
5. Once done, send this to the victim.

# CSRF Bypass tips

* Change Request Method [POST => GET]
* Remove Total Token Parameter
* Remove The Token, And Give a Blank Parameter
* Copy a Unused Valid Token , By Dropping The Request and Use That Token
* Use Own CSRF Token To Feed it to Victim
* Replace Value With Of A Token of Same Length 
* Reverse Engineer The Token
* Extract Token via HTML injection
* Switch From Non-Form `Content-Type: application/json` or `Content-Type: application/x-url-encoded` To `Content-Type: form-multipart`
* Change/delete the last or frist character from the token
* Change referrer to Referrer

![csrf1](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/d7273a09-bcbb-49e7-a031-99e60c1110d8)

# How to prevent CSRF vulnerabilities

* The SameSite attribute can be used to control whether and how cookies are submitted in cross-site requests. By setting the attribute on session cookies, an application can prevent the default browser behavior of automatically adding cookies to requests regardless of where they originate.

* The SameSite attribute is added to the Set-Cookie response header when the server issues a cookie, and the attribute can be given two values, Strict or Lax. For example:
SetCookie: SessionId=sYMnfCUrAlmqVVZn9dqevxyFpKZt30NN; SameSite=Strict;
SetCookie: SessionId=sYMnfCUrAlmqVVZn9dqevxyFpKZt30NN; SameSite=Lax;

* If the SameSite attribute is set to Strict, then the browser will not include the cookie in any requests that originate from another site. This is the most defensive option, but it can impair the user experience, because if a logged-in user follows a third-party link to a site, then they will appear not to be logged in, and will need to log in again before interacting with the site in the normal way.

* If the SameSite attribute is set to Lax, then the browser will include the cookie in requests that originate from another site but only if two conditions are met:

1. The request uses the GET method. Requests with other methods, such as POST, will not include the cookie.
2. The request resulted from a top-level navigation by the user, such as clicking a link. Other requests, such as those initiated by scripts, will not include the cookie.

* Using SameSite cookies in Lax mode does then provide a partial defense against CSRF attacks, because user actions that are targets for CSRF attacks are often implemented using the POST method. Two important caveats here are:

1. Some applications do implement sensitive actions using GET requests.
2. Many applications and frameworks are tolerant of different HTTP methods. In this situation, even if the application itself employs the POST method by design, it will in fact accept requests that are switched to use the GET method.

For the reasons described, it is not recommended to rely solely on SameSite cookies as a defense against CSRF attacks. Used in conjunction with CSRF Token however, SameSite cookies can provide an additional layer of defense that might mitigate any defects in the token-based defenses.

![csrf2](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/1ef0907e-2ad4-467a-8690-9792e1d022a4)
















