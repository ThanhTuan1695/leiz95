---
layout: post
title: Secure code guideline and Detail implementation User Management.
subtitle: Secure Code Guideline
thumbnail-img: /assets/img/oauthandoidc.png
tags: [research, code guideline, 0Auth, User, Accounting]
# comments: true
---


There is the recommendation, best practice and implementation code secure guideline to make sure the application securely as much as possible. 

There is the basic guideline that can apply for all Web Application.

# Secure code guideline basic 

Depends on your specific business requirement, you can define the code guideline which can apply to your project. 

Here is the basic step that i think need to care when doing the consultant: 

- Input Validation of ALL types of input such as your database, URL parameters and your database... Validate all the input on both sides include server and front-end. One Key here:  Don't trust any user input  

- Output Encoding is required for all output. anywhere that you show on the view. 

- Parameterized Queries are mandatory, inline SQL is forbidden. 

- All 3rd party code and components must be verified which is not contain known vulnerabilities. 

- Every security header should be used. 

- Do not cache sensitive page data. 

- Use the identity and session management features available to you in your framework or from your network or cloud provider. 

- Take every possible precaution when performing file uploads, including scanning it for vulnerabilities. 

- the uploading function should be checked include: 

  * file extension 

  * content-type 

  * file's size 

  * file's signiture 

  * file's content 

- Sensitive or decision-making information should never be stored in URL parameters. Sensitive data should be stored in secure cookies, and all available security features for cookies should be used, including the 'secure' flags as alway. the 'httpOnly' is depended on the context. Example, if you use SPA, it should not set but you need to change the place to stored session such as LocalStorage, SessionStorage. 

- All errors should be caught, handled, logged, and, if appropriate, alerted upon. Never log sensitive or PII information. 

- Use the Authorization and Authentication and other features in your framework , do not write your own. 

- HTTPS only. Never HTTP. Use only unbroken and industry standard protocols and algorithms. 

- Keep programming  framework(s) and dependencies up to date. 

# Logic Level For User Account Management. 

The following chapters provide a best practice collection of common security related use cases. 

## User account: Change E-Mail 

Requirements: 

- The new address must be entered twice. 

- Optional: The password must be entered. 

- If the e-mail is already in use by another account, this may not be printed out as a message (Information Disclosure). After successfully changing the address, an e-mail will be sent to the old and the new address. In the email to the new address, there is a link to confirm the operation. The mail to the old address contains instructions (like contact the service hotline) for the case that the customer has not made this change by himself. 

## User account: Change password 

Requirements: 

- The old password must be entered (and checked!) 

- The new password must be entered twice. 

- The new password must be checked against the password policy. 

## User account: Registration 

Requirements: 

- Anti automation: automated creation of new user accounts should be prevented. Available countermeasures: 

    * Captcha 

    * SMS-OTP (send a OTP to mobile number of user) 

    * Javascript-based challenges time consuming calculation on browser side (check the resulting value based on a value given by the application) 

    * Last exit: IP-locking 

- User ID Enumeration: The registration validation should not give information whether the entered user ID (like an email address) is already registered in the system. Instead, a general success message should be given and an email should be sent with further instructions. If the user ID already exists, the e-mail explains that a new registration request came in and how to reset the password. Otherwise the e-mail contains a link to confirm the registration. 

## User account: Login 

Requirements: 

- If an unknown user name or an incorrect password is entered, a  generic error message that states that the data entered is incorrect must be returned. 
Do not give information like "wrong user ID", ... 

- Lock account after x failed attempts for protecting your user accounts against brute force attacks. 
- Downside:If the user ID is easy to guess (E-Mail, easy pattern, etc.), an automated locking of all possible user accounts is possible. 
Soft lock-out as an alternative: When the user enters a wrong password three times, the application could lock down the account for a minute, then five minutes,etc. 

- Anti automation: automated user ID/password checks must be prevented. Available countermeasures: 

    * Captcha 

    * Ask for additional secret 

    * Javascript-based challenges 
time consuming calculation on browser side (check the resulting value based on a entry value given by the application) 

    * Last exit: IP-locking 

## User account: Password forgotten 

The user enters his account E-Mail address and the application sends an e-mail with a link to a public accessible password reset form. 

Requirements: 

- Anti automation: Add a captcha or Javascript-based challenge to the function 

- Optional additional secret: If a additional secret is implemented, the set of possible answers must be at least as large as the number of possible passwords. In addition the answer may not be searched in social networks. 

- If the e-mail address is not registered in the system, don't give a hint like "unknown e-mail address..." (User ID Enumeration). 
  

- If the e-mail address is registered in the system, an e-mail is sent containing a link secured by a long random string. The link leads to a public password reset form. 

    * The link will be invalid after one hour. 

    * The random string is valid for only one reset. 

## User account: Password strength & storing passwords 

Requirements: 

- Store password as bcrypt-Hash. 

- A password should have: 

    * at least 8 (better10) character 

    * at least 1 lowercase, 1 uppercase, 1 digit and 1 special character 

    * should not be found in a dictionary 

 