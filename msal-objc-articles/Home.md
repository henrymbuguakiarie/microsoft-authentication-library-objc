The Objective-C Microsoft Authentication Library (MSAL) is an Auth SDK that can be used to seamlessly integrate authentication into your app and give access to the entire Microsoft ecosystem. 

This platform allows you to easily target several identities including Azure AD (Work and School accounts), Microsoft account (Outlook.com, hotmail.com, and several others), or Azure AD B2C (Social and Local accounts). 
Your app will then have access to the entire set of Microsoft cloud APIs including the Microsoft Graph, Office 365, Azure, and any API you want to protect.  

The wiki is intended to document common patterns, error handling & debugging best practices, extra library functionality (e.g. logging, telemetry), and any active bugs or common issues with known mitigations. If you're looking for more help getting started with Azure AD, Microsoft Accounts, or Azure AD B2C, check out the full [Microsoft identity platform docs](https://aka.ms/aaddev). If you're looking for more info about the Microsoft Graph API, check out the [Microsoft Graph docs](https://graph.microsoft.io).

This wiki will be evolving over time. If you have a question about MSAL SDK, found an error in the documentation or need a recommendation on, please create a [Github issue](https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues) with the details!

### Getting started with MSAL SDK
- [Home](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Register your app](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-v2-register-an-app)
- [Getting Started](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-v2-ios) 
- [MSAL Code Sample](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-v2-ios#step-2-download-the-sample-project)
- [Single Sign-on (SSO)](https://docs.microsoft.com/en-us/azure/active-directory/develop/single-sign-on-macos-ios)
- [Error Handling](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-handling-exceptions#msal-for-ios-and-macos-errors)

### Configure, Build, Test, Deploy
- [Installation](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)
- [Configuring your app](https://github.com/AzureAD/microsoft-authentication-library-for-objc/tree/master#configuring-msal)
- [Targeting different identity types](https://docs.microsoft.com/en-us/azure/active-directory/develop/config-authority)

### Advanced Topics
- [Customizing Browsers and WebViews](https://docs.microsoft.com/en-us/azure/active-directory/develop/customize-webviews)
- [Logging](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-logging#logging-in-msal-for-ios-and-macos)
- [Sovereign clouds](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-national-cloud)
- [B2C](https://docs.microsoft.com/en-us/azure/active-directory/develop/config-authority#b2c)

### Getting Help, Common Issues, and FAQ

- [Redirect URIs](https://docs.microsoft.com/en-us/azure/active-directory/develop/redirect-uris-ios)
- [Requesting individual claims](https://docs.microsoft.com/en-us/azure/active-directory/develop/request-custom-claims)
- [Keychain cache](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-v2-keychain-objc)
- [SSL issues](https://docs.microsoft.com/en-us/azure/active-directory/develop/ssl-issues)
- [iOS 13 and macOS 10.15 support](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/iOS-13-and-macOS-10.15-support-in-MSAL)

### Migrating
- [ADAL to MSAL](https://docs.microsoft.com/en-us/azure/active-directory/develop/migrate-objc-adal-msal)

### News
- [Releases](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)