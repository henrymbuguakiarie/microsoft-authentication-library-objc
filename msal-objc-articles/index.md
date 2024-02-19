---
title: Microsoft Authentication Library for iOS and macOS
description: Learn about the Microsoft Authentication Library for iOS and macOS
services: active-directory
author: Dickson-Mwendia
manager: CelesteDG

ms.service: msal
ms.subservice: msal-ios-mac
ms.topic: conceptual
ms.date: 02/19/2024
ms.author: dmwendia
ms.reviewer: oldalton, brianmel
ms.custom: aaddev
---

# Microsoft Authentication Library for iOS and macOS

The MSAL library for iOS and macOS gives your app the ability to begin using the [Microsoft Identity platform](https://aka.ms/aaddev) by supporting [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) and [Microsoft Accounts](https://account.microsoft.com) in a converged experienceThe library also supports [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) for those using our hosted identity management service.

The Objective-C Microsoft Authentication Library (MSAL) is an Auth SDK that can be used to seamlessly integrate authentication into your app  using industry standard OAuth2 and OpenID Connect. 

This platform allows you to easily target several identities including Azure AD (Work and School accounts), Microsoft account (Outlook.com, hotmail.com, and several others), or Azure AD B2C (Social and Local accounts).  Your app will then have access to the entire set of Microsoft cloud APIs including the Microsoft Graph, Office 365, Azure, and any API you want to protect.  

## Getting started with MSAL for iOS and macOS conceptual documentation

The MSAL documentation covers common patterns, error handling and debugging best practices, extra library functionality (such as logging, telemetry), and any active bugs or common issues with known mitigations. If you're looking for more help getting started with Azure AD, Microsoft Accounts, or Azure AD B2C, check out the full [Microsoft identity platform docs](https://aka.ms/aaddev). If you're looking for more info about the Microsoft Graph API, check out the [Microsoft Graph docs](https://graph.microsoft.io).

1. Learn the differences between MSAL for iOS and macOS(./getting-started/scenarios.md).
1. You will need to [register your app](/azure/active-directory/develop/quickstart-register-app) with Microsoft Entra
1. Learn how to install MSAL
1. Learn how to configure MSAL [acquiring tokens](acquiring-tokens/overview.md) to access a protected API.
1. Learn how to acquire  tokens


## Supported Versions

**iOS** - MSAL supports iOS 14 and above.

**macOS** - MSAL supports macOS (OSX) 10.13 and above.


## Key differences between the Microsoft Authentication Library for iOS and macOS

This section explains the differences in functionality between the Microsoft Authentication Library (MSAL) for iOS and macOS.

> [!NOTE]
> On the Mac, MSAL only supports macOS apps.

## General differences

MSAL for macOS is a subset of the functionality available for iOS.

MSAL for macOS doesn't support:

- different browser types such as `ASWebAuthenticationSession`, `SFAuthenticationSession`, `SFSafariViewController`.
- brokered authentication through Microsoft Authenticator app is not supported for macOS.

Keychain sharing between apps from the same publisher is more limited on macOS 10.14 and earlier. Use [access control lists](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) to specify the paths to the apps that should share the keychain. User may see additional keychain prompts.

On macOS 10.15+, MSAL's behavior is the same between iOS and macOS. MSAL uses [keychain access groups](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) for keychain sharing.

### Conditional Access authentication

For Conditional Access scenarios, there will be fewer user prompts when you use MSAL for iOS. This is because iOS uses the broker app (Microsoft Authenticator) which negates the need to prompt the user in some cases.

### Project setup 

**macOS**

- When you set up your project on macOS, ensure that your application is signed with a valid development or production certificate. MSAL still works in the unsigned mode, but it will behave differently with regards to cache persistence. The app should only be run unsigned for debugging purposes. If you distribute the app unsigned, it will:
1. On 10.14 and earlier, MSAL will prompt the user for a keychain password every time they restart the app.
2. On 10.15+, MSAL will prompt user for credentials for every token acquisition.

- macOS apps don't need to implement the AppDelegate call.

**iOS**

- There are additional steps to set up your project to support authentication broker flow. The steps are called out in the tutorial.
- iOS projects need to register custom schemes in the info.plist. This isn't required on macOS.


## Migration from Azure Active Directory Authentication Library (ADAL)

Microsoft Authentication Library (MSAL) for iOS and macOS is the supported library that can be used for authentication token acquisition. If you or your organization are using the Azure Active Directory Authentication Library (ADAL), you should [migrate to MSAL](/azure/active-directory/develop/msal-migration). ADAL was deprecated on **June 30, 2023**.

## Samples

See [our comprehensive sample list](/azure/active-directory/develop/active-directory-v2-code-samples).

## Get help with the SDK

If you have a question about MSAL SDK, found an error in the documentation or need a recommendation on, please create a [Github issue](https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues) with the details.