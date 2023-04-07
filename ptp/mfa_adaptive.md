**Breadcrumb**

[Home](../home.md) > Pathway to Production > [MFA & Adaptive Authentication](mfa_adapative.md)

# MFA & Adaptive Authentication

The CloudPRO prototype utilizes passwordless authentication for both Surgeons & Patients. In a production environment, care needs to be taken to enhance end user security. Although passwordless is a major improvement to utilizing passwords, it is not fail-proof. Biometrics can be spoofed, one time passwords can be intercepted, and hardware tokens can be stolen.

Adding MFA to the authentication process will increase the security of end users. The following extension to the prototype can be made:

1) Require two forms of identification for a user (IE Email & Telephone or Email & Duo Authenticator)
2) Allow user to set their preferred primary mode of authentication. This is the method that their OTP for passwordless login flow will be sent.
3) Secondary form of identification is utilized for a MFA code.

Consider additional improvements for general QOL and security of end users. Adaptive (Behavioral) Authentication for the system can be explored. Adapative Authentication learns general user login behaviors. If a user's login is deemed outside of typical behaviors, a MFA requirement will be presented and the user will be required to enter their MFA authentication code. From a security perspective, adaptive can also flag a login as high-risk (IE attempted bruteforce or flagged geolocation) and block requests.