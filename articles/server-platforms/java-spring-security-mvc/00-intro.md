---
title: Introduction
description: This tutorial will show you how to use the Auth0 Java Spring Security MVC SDK to add authentication and authorization to your web app, using Spring Boot.
---

This multi-step quickstart will guide you through the process of managing authentication in your Java Spring Security Web Application with Auth0.

Auth0 provides and manages a [Spring Security MVC SDK](https://github.com/auth0/auth0-spring-security-mvc). This SDK allows you to use Auth0 with Java for server-side Spring Security web apps, leveraging Spring Boot dependencies.

__NOTE:__ You can find a listing of all our Java offerings and several sample projects in [Java technologies and Auth0 libraries](/java-overview). 


## Seed &amp; Samples

There are two options to follow along this quickstart. You can either download the [seed project](https://github.com/auth0-samples/auth0-spring-security-mvc-sample/tree/master/00-Starter-Seed) or the samples provided at each page of this quickstart. 

The seed is a regular Java Spring Security app, with all the Auth0 dependencies set, but nothing more. It's an empty canvas meant to be filled as you follow along the steps of this quickstart. If you prefer this option download the seed from our [GitHub repository](https://github.com/auth0-samples/auth0-spring-security-mvc-sample/tree/master/00-Starter-Seed) and follow along.

Instead you can choose to follow the samples that are included in each step. Each sample uses the [seed project](https://github.com/auth0-samples/auth0-spring-security-mvc-sample/tree/master/00-Starter-Seed) as a starting point and applies to it the configuration of each step, so for example the Login sample would be the [seed project](https://github.com/auth0-samples/auth0-spring-security-mvc-sample/tree/master/00-Starter-Seed) plus the configuration required to implement login functionality. If you choose to follow this approach continue reading, the rest of this document will guide you through setting up the required prerequisites.


### Seed project structure

Let's take some time and explain how our [seed project](https://github.com/auth0-samples/auth0-spring-security-mvc-sample/tree/master/00-Starter-Seed) is structured. 


```
- src
-- main
---- java
------ com
-------- auth0
---------- example
------------ App.java
------------ AppConfig.java
------------ ErrorController.java
------------ HomeController.java
------------ LoginController.java
------------ LogoutController.java
---- resources
------ application.properties
------ auth0.properties
---- webapp
------ WEB-INF
-------- jsp
---------- home.jsp
---------- login.jsp
-------- web.xml
- pom.xml
```

The project contains two JSP: the `login.jsp` that will handle the user login, and the `home.jsp` which will display user information after a successful login and provide the option to logout.

The project contains also the following files:
- `application.properties`: Configuration file that holds information such as the port your app will use, the logging level, etc.
- `auth0.properties`: Configuration file that holds Auth0 related information, like the client id, client secret, etc. You can find more detailed information in the [Configure your Java Spring app](#configure-your-java-spring-app) section of this document.
- `App.java`: A Java class that uses Spring Boot’s `SpringApplication.run()` method to launch the application.
- `AppConfig.java`: The security configuration class. By subclassing, and overriding `authorizeRequests` method, you can define the endpoint security configuration (authentication and authorization) suitable for your own needs.
- `ErrorController.java`: The controller that handles the errors.
- `HomeController.java`: The controller that handles landing page requests.
- `LoginController.java`: The controller that handles the login.
- `LogoutController.java`: The controller that handles the logouts.


## Create a Client

<%= include('../../_includes/_java_new_app') %>

![Client Dashboard](/media/articles/java/app_dashboard.png)


## Configure callback URLs

Callback URLs are URLs that Auth0 invokes after the authentication process. Auth0 routes your application back to this URL and attaches some details to it including a token. Callback URLs can be manipulated on the fly and that could be harmful. For security reasons, you will need to add your application's URL in the client's `Allowed Callback URLs`. This will enable Auth0 to recognize the URLs as valid. If omitted, authentication will not be successful for the app instance.

![Callback error](/media/articles/java/callback_error.png)

If you follow our seed project or the samples based on it, the values you must configure are:
- Allowed Callback URL: `http://localhost:3099/callback`
- Allowed Logout URLs: `http://localhost:3099/logout`


## Setup dependencies

To integrate your Spring Security application with Auth0 you need to have the following dependencies set:

- [auth0-spring-security-mvc](https://github.com/auth0/auth0-spring-security-mvc): is the Java library that allows you to use Auth0 with Spring Security for server-side MVC web apps. It validates the [JWT](/jwt) from Auth0 in every API call to assert authentication according to configuration.

If you are using maven, add the dependency to your `pom.xml`:

${snippet(meta.snippets.dependencies)}

If you are using Gradle, add it to the dependencies block:

${snippet(meta.snippets.dependenciesGradle)}


## Configure your Spring Security app

Your Spring Security app needs some information in order to authenticate against your Auth0 account. We have created a file for you but you need to update some of the entries with the valid values for your Client. The file is `/src/main/resources/auth0.properties` and it contains the following:

${snippet(meta.snippets.setup)}

Let's see what each attribute means.

| Attribute | Description|
| --- | --- |
| `auth0.domain` | Your auth0 domain. You can find the correct value on the Settings tab of your client on the [dashboard](${manage_url}/#/applications). * |
| `auth0.issuer` | The issuer of the JWT Token. This is typically your auth0 domain with a `https://` prefix and a `/` suffix. For example, if your `auth0.domain` is `example.auth0.com` then the `auth0.issuer` should be set to `https://example.auth0.com/` (the trailing slash is important). |
| `auth0.clientId` | The unique identifier for your client. You can find the correct value on the Settings tab of your client on the [dashboard](${manage_url}/#/applications). * |
| `auth0.clientSecret` | The secret used to sign and validate the tokens that will be used in the different authentication flows. You can find the correct value on the Settings tab of your client on the [dashboard](${manage_url}/#/applications). * |
| `auth0.onLogoutRedirectTo` | The page that users of your site are redirected to on logout. Should start with `/`. |
| `auth0.securedRoute` | The URL pattern that should map to the URL endpoint you wish to secure. You should replace its value with the correct value for your implementation. It should start with `/`. * |
| `auth0.loginCallback` | The URL context path for the login callback endpoint. Should start with `/`. |
| `auth0.loginRedirectOnSuccess` | The landing page URL context path for a successful authentication. Should start with `/`. |
| `auth0.loginRedirectOnFail` | The URL context path for the page to redirect to upon failure. Should start with `/`. |
| `auth0.base64EncodedSecret` | A boolean value indicating whether the Secret used to verify the JWT is `base64` encoded. Default is `true`. |
| `auth0.authorityStrategy` | Indicates whether authorization claims against the Principal shall be `GROUPS`, `ROLES` or `SCOPE` based. Default is `ROLES`. |
| `auth0.servletFilterEnabled` | A boolean value that switches having an authentication filter enabled or not. |
| `auth0.defaultAuth0ApiSecurityEnabled` | A boolean value that switches having the default config enabled. It should be set to `false`. |

**NOTE**: If you download the seed using our **Download Sample** button then the `domain`, `clientId` and `clientSecret` attributes will be populated for you, unless you are not logged in or you do not have at least one registered client. In any case you should verify that the values are correct if you have multiple clients in your account and you might want to use another than the one we set the information for. Do not forget to manually set the `issuer` attribute!

**NOTE**: If you are using the default library configuration (not overriding with your own) which just secures a single, specific context path then the `auth0.securedRoute` value is important. However, if you are building an application which may have several different secured endpoints, or you don't want to specify an explicit configuration value in this `.properties` file then just set `auth0.securedRoute` to something that signifies this. Perhaps `auth0.securedRoute: UNUSED`. Then just ignore the `securedRoute` entirely when you specify your own configuration. See the section [Extending Auth0SecurityConfig](https://github.com/auth0/auth0-spring-security-api#extending-auth0securityconfig) for further info. This property value is a convenience for the developer to configure an endpoint by context path (.eg all URLS with `/api/v1/` in their context path), but there is no obligation to actually reference this property in your own `HttpSecurity` configuration.

**NOTE**: We have created a [sample application](https://github.com/auth0-samples/auth0-spring-security-mvc-sso-sample) that demonstrates using Auth0 with Spring Boot and Spring Security to create two traditional server-side MVC web apps that are configured for Single Sign On with one another. The [sample application](https://github.com/auth0-samples/auth0-spring-security-mvc-sso-sample) contains  two demo websites that have been developed to demonstrate the Auth0 SSO capabilities between a main "portal" website and a Partner website that depends on the main "portal" site for SSO authentication. Fell free to check this out.


You are now ready to continue with the next tutorial in order to implement basic login using [Lock](/libraries/lock)!


