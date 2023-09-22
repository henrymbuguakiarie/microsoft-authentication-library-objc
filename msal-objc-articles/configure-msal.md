# Configuring MSAL

### Adding MSAL to your project

1. Register your app in the [Azure portal](https://aka.ms/MobileAppReg)
2. Make sure you register a redirect URI for your application. It should be in the following format: 

 `msauth.$(PRODUCT_BUNDLE_IDENTIFIER)://auth`

3. Add a new keychain group to your project Capabilities. Keychain group should be `com.microsoft.adalcache` on iOS and `com.microsoft.identity.universalstorage` on macOS. 

![](Images/keychain_example.png)

See more information about [keychain groups](/azure/active-directory/develop/howto-v2-keychain-objc) and [Silent SSO for MSAL](/azure/active-directory/develop/single-sign-on-macos-ios).

#### iOS only steps:

1. Add your application's redirect URI scheme to your `Info.plist` file

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        </array>
    </dict>
</array>
```
2. Add `LSApplicationQueriesSchemes` to allow making call to Microsoft Authenticator if installed.

Note that “msauthv3” scheme is needed when compiling your app with Xcode 11 and later. 

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
	<string>msauthv2</string>
	<string>msauthv3</string>
</array>
```
See more info about [configuring redirect uri for MSAL](/azure/active-directory/develop/reply-url)

3. To handle a callback, add the following to `appDelegate`:

#### Swift
```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
	return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

#### Objective-C
```obj-c
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

**Note, that if you adopted UISceneDelegate on iOS 13+**, MSAL callback needs to be placed into the appropriate delegate method of UISceneDelegate instead of AppDelegate. MSAL `handleMSALResponse:sourceApplication:` must be called only once for each URL. If you support both UISceneDelegate and UIApplicationDelegate for compatibility with older iOS, MSAL callback would need to be placed into both files.

#### Swift
```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### Objective-C
```objective-c
- (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
{
    UIOpenURLContext *context = URLContexts.anyObject;
    NSURL *url = context.URL;
    NSString *sourceApplication = context.options.sourceApplication;
    
    [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
}
```

#### macOS only steps:

1. Make sure your application is signed with a valid development certificate. While MSAL will still work in the unsigned mode, it will behave differently around cache persistence.




### Microsoft Enterprise SSO plug-in for Apple devices

Microsoft has recently released a new plug-in that uses the newly announced Apple feature called [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices). Microsoft Enterprise SSO plug-in for Apple devices offers the following benefits: 

* Comes delivered in Microsoft Authenticator app automatically and can be enabled by any MDM.
* Provides seamless SSO for Active Directory joined accounts across all applications that support Apple's Enterprise Single Sign-On feature.
* COMING SOON: Provides seamless SSO across Safari browsers and applications on the device.

MSAL 1.1.0 and above will use Microsoft Enterprise SSO plug-in automatically instead of the Microsoft Authenticator app when it is active on the device. To use Microsoft Enterprise SSO plug-in in your tenant, you need to enable it in your MDM profile. 

See [more information](/azure/active-directory/develop/apple-sso-plugin) about configuring Microsoft Enterprise SSO plug-in for your device [here](/azure/active-directory/develop/apple-sso-plugin)

### Single Account Mode

If your app needs to support just one signed-in user at a time, MSAL provides a simple way to read the signed in account. This API must be also used when you are building an application to run on devices that are configured as shared devices - meaning that a single corporate device is shared between multiple employees. Employees can sign in to their devices and access customer information quickly. When they are finished with their shift or task, they will be able to sign-out of all apps on the shared device.

Here is a code snippet that shows how you can retrieve current account. You must call API every time when your app comes to foreground or before performing a sensitive operation to detect any signed-in account changes. 

#### Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main
                
application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in
            
	// currentAccount is the currently signed in account
	// previousAccount is the previously signed in account if any
})
```

#### Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();
        
[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
	// currentAccount is the currently signed in account
	// previousAccount is the previously signed in account if any
}];
```

### Multiple Accounts Mode

MSAL also provides a public API to query multiple accounts, granted that they exist in the MSAL cache.

1) Make sure the umbrella header MSAL-umbrella.h is imported (just MSAL for Swift)

2) Create config, then use it to initialize an application object 

3) Also initialize MSALAccountEnumerationParameters object with the account identifier. Each MSALAccount object has a parameter called “identifier”, which represents the unique account identifier associated with the given MSALAccount object. We recommend using it as the primary search criterion. 

4) Then invoke the API "accountsFromDeviceForParameters" from the application object using the enumeration parameter. If you have multiple accounts in MSAL cache, it will return an array containing MSALAccounts that have the account identifier you specified in the previous step. 

5) Once the MSAL account is retrieved, invoke acquire token silent operation

#### Swift

```swift
#import MSAL //Make sure to import MSAL  

let config = MSALPublicClientApplicationConfig(clientId:clientId
                                           	redirectUri:redirectUri
                                            	authority:authority)
guard let application = MSALPublicClientApplication(configuration: config) else { return }

let accountIdentifier = "9f4880d8-80ba-4c40-97bc-f7a23c703084.f645ad92-e38d-4d1a-b510-d1b09a74a8ca"
let parameters = MSALAccountEnumerationParameters(identifier:accountIdentifier)

var scopeArr = ["https://graph.microsoft.com/.default"]

if #available(macOS 10.15, *)
{
	 application.accountsFromDeviceForParameters(with: parameters, completionBlock:{(accounts, error) in
         if let error = error 
         {
            //Handle error
         }
         
         guard let accountObjs = accounts else {return}
         
         let tokenParameters = MSALSilentTokenParameters(scopes:scopeArr, account: accountObjs[0]);
                                                                                                   
         application.acquireTokenSilentWithParameters(with: tokenParameters, completionBlock:{(result, error) in 
                     if let error = error
                     {
                         //handle error
                     }
                                       
                     guard let resp = result else {return} //process result
                                                                                             
         })                                                               
                                                                                                                                                             
   })
  
}
```


#### Objective-C

```objective-c
//import other key libraries  
#import "MSAL-umbrella.h" //Make sure to import umbrella file 

    MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:clientId
     redirectUri:redirectUri
       authority:authority];

    MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&error];
    MSALAccountEnumerationParameters *parameters = [[MSALAccountEnumerationParameters alloc] initWithIdentifier:@"9f4880d8-80ba-4c40-97bc-f7a23c703084.f645ad92-e38d-4d1a-b510-d1b09a74a8ca"]; //init with account identifier

    NSArray<NSString *> *scopeArr = [[NSArray alloc] initWithObjects: @"https://graph.microsoft.com/.default",nil]; //define scope

    if (@available(macOS 10.15, *)) //Currently, this public API requires macOs version 10.15 or greater.
    {
        [application accountsFromDeviceForParameters:parameters
                                     completionBlock:^(NSArray<MSALAccount *> * _Nullable accounts, __unused NSError * _Nullable error)
        {
            if (error)
            {
              //Log error & return 
            }
          
            if (accounts)
            {
                NSLog(@"hi there");
                MSALSilentTokenParameters *tokenParameters = [[MSALSilentTokenParameters alloc] initWithScopes:scopeArr account:accounts[0]];

                [application acquireTokenSilentWithParameters:tokenParameters
                                completionBlock:^(MSALResult * _Nullable result, NSError * _Nullable error)
                 {
                    if (error)
                    {
                        //Log Error & return 
                    }
                    if (result)
                    {
                        //process result
                    }
                }
                 ];
            }
     
        }];
    }
```

### Detect shared device mode

Use following code to read current device configuration, including whether device is configured as shared:

#### Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in
                
	guard let deviceInfo = deviceInformation else {
		return
	}
                
	let isSharedDevice = deviceInfo.deviceMode == .shared
	// Change your app UX if needed
})
```

#### Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
	if (!deviceInformation)
	{
		return;
	}
            
	BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
	// Change your app UX if needed
}];
```

### Implement signout

To signout account from your app, call MSAL's signout API. You can also optionally sign out from the browser. When MSAL is running on a shared device, signout API will signout globally from all apps on user's device.

#### Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
signoutParameters.signoutFromBrowser = false
            
application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in
                
	if let error = error {
		// Signout failed
		return
	}
                
	// Sign out completed successfully
})
```

#### Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;
        
MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = NO;
        
[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
	if (!success)
	{
		// Signout failed
		return;
	}
            
	// Sign out completed successfully
}];
```

