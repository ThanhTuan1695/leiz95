---
layout: post
title: 0Auth2 and Open ID Connect
subtitle: What is the 0Auth2 and OIDC?
thumbnail-img: /assets/img/oauthandoidc.png
tags: [research, OIDC, 0Auth, 0Auth2, Keycloak]
# comments: true
---
There is a lot of article writing about 0Auth2 and OIDC. Just list out what knowing so far.

    Identity, Authentication + 0Auth = Open ID Connect. 

This post is purpose for security research relate OIDC topic later.

# Table of Content
    1. What is 0Auth2? 
    2. What is Open ID connect (OIDC)? 
    3. What is the Open ID Connect provider (OP)? 

## 1. What is 0Auth2? 

#### 1.1. Definition
The OAuth 2.0 authorization framework enables a third-party application to obtain limited access to an HTTP service, either on behalf of a resource owner by orchestrating an approval interaction between the resource owner and the HTTP service, or by allowing the third-party application to obtain access on its own behalf.

#### 1.2. Basic Knownledge
you can take a lot at this page. This is very detail in [Understanding OAuth2](http://www.bubblecode.net/en/2016/01/22/understanding-oauth2/)


    
## 2. What is Open ID Connect (OIDC)? 

#### 2.1. Definition
OpenID Connect 1.0 is a simple identity layer on top of the OAuth 2.0 protocol. It allows Clients to verify the identity of the End-User based on the authentication performed by an Authorization Server, as well as to obtain basic profile information about the End-User in an interoperable and REST-like manner.

OpenID Connect allows clients of all types, including Web-based, mobile, and JavaScript clients, to request and receive information about authenticated sessions and end-users. The specification suite is extensible, allowing participants to use optional features such as encryption of identity data, discovery of OpenID Providers, and session management, when it makes sense for them. Since the OAuth framework only describes an authorization method and does not provide any details about the user, OpenID Connect fills this gap by describing an authentication and session management mechanism.

## 3. What is the Open ID Connect Provider (OP)?

There is a lot of OP on the internet. You can search and check it out on the internet. I will list all the Provider as i known so far. These provider is most popular.

#### 3.1. Keycloak
Providing and Maintance by Red Hat.
Keycloak is an open source indentity and access management solution aimed at modern Application and services. It makes it easy to secure applications and services with  little to no code. 
Currently the keycloak release version 12.0.1 with a lot of bug fix and refactor. You can check it [here](https://www.keycloak.org/). In the next post, i will update how to use the keycloak and config it. 
##### 3.2. Amazon Cognito
Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily. Amazon Cognito scales to millions of users and supports sign-in with social identity providers, such as Facebook, Google, and Amazon, and enterprise identity providers via SAML 2.0. Check it [Here](https://aws.amazon.com/cognito/).

These two provider that was used by me so far.  If you have any OIDC provider, please let me know and will put it in this post.

In the next post, i am going to deeper in the keycloak provider. 

## Reference
[Real life OIDC](https://security.lauritz-holtmann.de/post/sso-security-responsible-disclosure/)
[The 0Auth2 Framwork](https://tools.ietf.org/html/rfc6749)
[Thread Model and Security Considerations](https://tools.ietf.org/html/rfc6819)
[0Auth2 Spec](https://www.oauth.com/oauth2-servers/map-oauth-2-0-specs/)
[Open ID Connect](https://openid.net/connect/)
[Open ID Connect course](https://curity.io/resources/courses/openid-connect-in-detail/overview-of-openid-connect)