# What is Cross Site Request ForgeryCSRF)?
Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform.
Cross-Site Request Forgery (CSRF) is an attack that forces an end user to execute unwanted actions on a web application in which they’re currently authenticated. With a little help from social engineering (such as sending a link via email or chat), an attacker may trick the users of a web application into executing actions of the attacker’s choosing.

# How does CSRF work?

**For a CSRF attack to be possible, three key conditions must be in place:**

**1. A relevant action:** 
* There is an action within the application that the attacker has a reason to induce. This might be a privileged action (such as modifying permissions for other users) or any action on user-specific data (such as changing the user's own password).

**2. Cookie-based session handling:** 
* Performing the action involves issuing one or more HTTP requests, and the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.

**3. No unpredictable request parameters:** 
* The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess. For example, when causing a user to change their password, the function is not vulnerable if an attacker needs to know the value of the existing password.

For example, suppose an application contains a function that lets the user change the email address on their account. When a user performs this action, they make an HTTP request like the following: 

POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 30
Cookie: session=yvthwsztyeQkAPzeQ5gHgTvlyxHfsAfE

email=wiener@normal-user.com

# How does Cross-Site Request Forgery work?
An attacker’s aim for carrying out a CSRF attack is to force the user to submit a state-changing request. Examples include:

* Submitting or deleting a record.
* Submitting a transaction.
* Purchasing a product.
* Changing a password.
* Sending a message.

![0 UHwQJ56O21Il8MI0](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/537d6ce9-fa1e-4d2e-9148-6e08430b916e)

Social engineering platforms are often used by attackers to launch a CSRF attack. This tricks the victim into clicking a URL that contains a maliciously crafted, unauthorized request for a particular Web application. The user’s browser then sends this maliciously crafted request to a targeted Web application. The request also includes any credentials related to the particular website (e.g., user session cookies). If the user is in an active session with a targeted Web application, the application treats this new request as an authorized request submitted by the user. Thus, the attacker succeeds in exploiting the Web application’s CSRF vulnerability.

# What is the Impact of CSRF Attacks?

* When a website sends a data request to another website on behalf of a user along with the user’s session cookie, an attacker can launch a Cross-Site Request Forgery Attack, which abuses a trustful relationship between the victim’s browser and the webserver.

* In some cases, depending on the type of action, the attacker can gain full control of the user’s account. If the compromised user has a privileged role within the application, the attacker might be able to take full control of all the application’s functionality and data, which is devastating to both the business and the user. The result can be data theft, unauthorized fund transfers, damaged client relationships, changed passwords and many more.

# Testing CSRF

* Remove CSRF token from requests and/or put a blank space.
* Change POST to GET.
* Replace the CSRF token with a random value (for example 1).
* Replace the CSRF token with a random token of the same restraints.
* Extract token with HTML injection.
* Use a CSRF token that has been used before.
* Remove referer header.
* Request a CSRF by executing the call manually and use that token for the request.
 
**Base Steps:**

1. Select a request anywhere in Burp Suite Professional that you want to test or exploit.
2. From the right-click context menu, select Engagement tools / Generate CSRF PoC.
3. Burp Suite will generate some HTML that will trigger the selected request (minus cookies, which will be added automatically by the victim's browser).
4. You can tweak various options in the CSRF PoC generator to fine-tune aspects of the attack. You might need to do this in some unusual situations to deal with quirky features of requests.
5. Copy the generated HTML into a web page, view it in a browser that is logged in to the vulnerable web site, and test whether the intended request is issued successfully and the desired action occurs.

# Bypass Method -1 : Change the request method POST → GET

**Test Case: Validation of CSRF token depends on request method**

1. Interect with functionality and intercept the request.
2. Send this requets to repeater and right click change request method
3. Remove any csrf param and genrate csrf poc
4. Edit according to your preference,For example
5. Done send this to victim.

# Bypass Method - 2: Remove csrf param from POST request.

**Test Case: Validation of CSRF token depends on token being present**

1. Interect with functionality and intercept the request.
2. Send this requets to repeater.
3. Remove any csrf param and generate csrf poc
4. Edit according to your preference,For example:
5. Done send this to victim.

# Bypass Method - 3: Feed your own account generated CSRF token in attack.

**Test Case: CSRF token is not tied to the user session.**

1. Interect with functionality and intercept the request.
2. Right click generate csrf poc.
3. Copy the code in a file.html remove any session token
4. Drop the request.
5. Send the file.html to victim.

# Bypass Method - 4 : Chain any other vulnerability to add your cookie for example XSS, CRLF → CSRF

**Test Case - 1 : CSRF token is tied to a non-session cookie, when we have two csrf token one in cookie and other in the functionality this is due to presence of two framework one for session handling and one for CSRF protection, which are not integrated together.**

The cookie-setting behavior does not even need to exist within the same web application as the CSRF vulnerability. Any other application within the same overall DNS domain can potentially be leveraged to set cookies in the application that is being targeted, if the cookie that is controlled has suitable scope. For example, a cookie-setting function on staging.demo.normal-website.com could be leveraged to place a cookie that is submitted to secure.normal-website.com.

1. Find any vulnerability which allow you to inject something in the cookie of victim.
2. Test if CSRF token is tied to session id (try changing session id keeping everything as it is))
3. Check if the your csrf token works when replaced in victims request
4. Lastly check if you can inject CRLF and change csrf cookie value
5. Done now make a csrf poc with xss payload which execute crlf and send this poc to victim

**Test Case - 2 : CSRF token is simply duplicated in a cookie, here csrf token value can be anything just need to be same in cookie as wells as param.**

1. Intercept and action and try changing csrf token in both cookie and param
2. Make similar poc as above but this time put same csrf token in crlf payload and request param.
3. Done, Send it to victim.

# Bypass Method - 5 : Delete the Referrer Header Completely or Suppress it.

**Test Case: CSRF where Referer validation depends on header being present.**

1. Intercept the request and try changing referer to some other domain.
2. If that didn't work then you will have to suppress the refere header.
3. you can use `<meta name="referrer" content="no-referrer">` or any other technique.
4. Done, Make a normal POC with that technique.

# Bypass Method - 6 : Try attacker.com or similar payload in referer header. (Validation of Referer can be circumvented).

**Test case: CSRF with broken Referer validation**

1. Intercept the request and try changing referer to some other domain. (Check all cases how it is been verified)
2. Now Generate a normal POC and include any JavaScript in the script block to alter the URL and Referer
3. Done Send it to victim.

# Bypass Method - 7: Send null value in csrf token.

**Test Case: Validation of CSRF token depends on token value being**

1. Interect with functionality and intercept the request.
2. Send this requets to repeater.
3. Add null csrf param and generate csrf poc
4. Edit according to your preference,For example:
5. Done send this to victim.

![csrf1](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/d7273a09-bcbb-49e7-a031-99e60c1110d8)

# Defending against CSRF with SameSite cookies

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

# Using CSRF Token

* The most robust way to defend against CSRF attacks is to include a CSRF Token within relevant requests. The token should be:

1. Unpredictable with high entropy, as for session tokens in general.
2. Tied to the user's session.
3. Strictly validated in every case before the relevant action is executed.

# CSRF Bypass

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

![csrf2](https://github.com/InfoSecExplorer/Cross-Site-Request-Forgery-CSRF-/assets/145893728/1ef0907e-2ad4-467a-8690-9792e1d022a4)
















