# Cyber Security Base - Course Project I

This project is a simple web application (built using Spring) that contains multiple security vulnerabilities present in
the top 10 most common web vulnerabilities list provided by OWASP. This report outlines how the flaws in the software
can be first identified and then fixed.

## Vulnerabilities

### A3:2017 - Sensitive Data Exposure
**What is:** Many web applications and APIs do not properly protect sensitive data, such as financial,
healthcare, and PII. Attackers may steal or modify such weakly protected data to conduct credit
card fraud, identity theft, or other crimes. Sensitive data may be compromised without extra
protection, such as encryption at rest or in transit, and requires special precautions when
exchanged with the browser.

**Identifying the flaw:** In this application, the users are expected to submit their name and address to the system.
This data, however, can be seen by a man-in-the-middle that is eavesdropping in the network connection. The reason for
this is that the communication is done using the HTTP (insecure) protocol instead of HTTPS. If the requests were
performed using the secure version of HTTP, there would be no easy way for someone listening to the network packets to
decrypt its contents and understand who is making the requests and what is their name and address.

**Fix:** Use HTTPS in all pages (not easy in a development environment, but should happen in production).

###  A5:2017- Broken Access Control
**What is:** Restrictions on what authenticated users are allowed to do are often not properly enforced.
Attackers can exploit these flaws to access unauthorized functionality and/or data, such as access
other users' accounts, view sensitive files, modify other users’ data, change access rights, etc.

**Identifying the flaw:** These steps can be followed to understand that there is a Broken Access Control vulnerability 
present in this application:
1. Access the page /done.html
1. Notice the server reponse: 200
1. Notice that the server thanks you for your registration, assuming that you accessed the page after a form submission.

This is a flaw in the system because that page was intended to be viewed solely by registered participants.

**Fix:** Sessions should be used so that only the users that have filled the registration form have access to the "done"
page. Additionally, only administrators should probably be allowed to view the list of participants and their details,
as their address can be considered private and sensitive information.

### A6:2017 - Security Misconfiguration
**What is:** Security misconfiguration is the most commonly seen issue. This is commonly a result of insecure
default configurations, incomplete or ad hoc configurations, open cloud storage, misconfigured
HTTP headers, and verbose error messages containing sensitive information. Not only must all
operating systems, frameworks, libraries, and applications be securely configured, but they must
be patched and upgraded in a timely fashion.

**Identifying the flaw:** The vulnerability contained in this application allows an intruder to force other people to
register in the event unintentionally. The reason for this is that the registration form does not contain a CSRF
token, and this can be verified by viewing the data that is sent to the server when the form is submitted.

**Fix:** In the SecurityConfiguration.java file enable CSRF protection.

### A7:2017- Cross-Site Scripting (XSS)
XSS flaws occur whenever an application includes untrusted data in a new web page without
proper validation or escaping, or updates an existing web page with user-supplied data using a
browser API that can create HTML or JavaScript. XSS allows attackers to execute scripts in the
victim’s browser which can hijack user sessions, deface web sites, or redirect the user to
malicious sites.

**Identifying the flaw:** Follow those steps to reproduce:
1. In the "name" field write ```<script>alert('oops');</script>```.
1. In the "address" field write anything, as we are using the "name" field for this example (though it could be also
performed using this "address" field).
1. Click "submit".

You will be redirected to a page where your alert message "oops" will be shown to the user, not only to you but to any
other user that accesses the system. This means that XSS exploitation is possible and opens a window for more dangerous
activities.

**Fix:** Properly encode all user-provided content that is to be displayed.

### A10:2017- Insufficient Logging & Monitoring
Insufficient logging and monitoring, coupled with missing or ineffective integration with incident
response, allows attackers to further attack systems, maintain persistence, pivot to more systems,
and tamper, extract, or destroy data. Most breach studies show time to detect a breach is over
200 days, typically detected by external parties rather than internal processes or monitoring. 

**Identifying the flaw:** By analyzing the source code it is visible that there is no logs at all of any activity, so an
intrusion would not be properly monitored and registered.

**Fix:** Add logging functionality to the application code.