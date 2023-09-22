# Installing MSAL
 
### Using CocoaPods

#### Installing CocoaPods
[CocoaPods](https://cocoapods.org) is a dependency manager for Swift and Objective-C Cocoa projects.
It can be installed as a ruby gem, by using the following command.
```
$ sudo gem install cocoapods
```
> Depending on your Ruby setup, `sudo` may or may not be needed.

#### Include MSAL in Podfile
Create a new `Podfile` or add to the existing podfile, include `pod 'MSAL'` under `target. 

An example Podfile:
```
target "your-target-here" do
    pod 'MSAL'
end
```

Note: if you're checking out a particular branch or tag of MSAL, you will need to add the :submodules => true flag to your podfile, so that CocoaPods finds MSAL dependencies

```
pod 'MSAL', :git => 'https://github.com/AzureAD/microsoft-authentication-library-for-objc', :branch => 'master', :submodules => true
```

You then you can run either `pod install` (if it's a new PodFile) or `pod update` (if it's an existing PodFile) to get the latest version of MSAL. 

Subsequent calls to `pod update` will update to the latest released version of MSAL as well.

See [CocoaPods](https://guides.cocoapods.org/using/the-podfile.html) for more information on setting up a PodFile

### Using Carthage

[Carthage](https://github.com/Carthage/Carthage) is another popular dependency manager MSAL supports. 
The following is a sample using Carthage.

##### If you're building for iOS, tvOS, or watchOS

1. Install Carthage on your Mac using a download from their website or if using Homebrew `brew install carthage`.
1. You must create a `Cartfile` that lists the MSAL library for this project on Github. 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```
Note: this will point Carthage to the master branch, so you'll always get the latest official release. If you would like to use a particular version, you can do the following:

```
github "AzureAD/microsoft-authentication-library-for-objc" == <latest_released_version>
```

1. Run `carthage update`. This will fetch dependencies into a `Carthage/Checkouts` folder, then build the MSAL library.
1. On your application targets’ “General” settings tab, in the “Linked Frameworks and Libraries” section, drag and drop the `MSAL.framework` from the `Carthage/Build` folder on disk.
1. On your application targets’ “Build Phases” settings tab, click the “+” icon and choose “New Run Script Phase”. Create a Run Script in which you specify your shell (ex: `/bin/sh`), add the following contents to the script area below the shell:

  ```sh
  /usr/local/bin/carthage copy-frameworks
  ```

  and add the paths to the frameworks you want to use under “Input Files”, e.g.:

  ```
  $(SRCROOT)/Carthage/Build/iOS/MSAL.framework
  ```
  This script works around an [App Store submission bug](http://www.openradar.me/radar?id=6409498411401216) triggered by universal binaries and ensures that necessary bitcode-related files and dSYMs are copied when archiving.

With the debug information copied into the built products directory, Xcode will be able to symbolicate the stack trace whenever you stop at a breakpoint. This will also enable you to step through third-party code in the debugger.

When archiving your application for submission to the App Store or TestFlight, Xcode will also copy these files into the dSYMs subdirectory of your application’s `.xcarchive` bundle.


### Using Swift Packages

You can add `MSAL` as a [swift package dependency](https://developer.apple.com/documentation/swift_packages/distributing_binary_frameworks_as_swift_packages).
For MSAL version 1.1.14 and above, distribution of MSAL binary framework as a Swift package is available.

1. For your project in Xcode, click File → Swift Packages → Add Package Dependency...
2. Choose project to add dependency in
3. Enter : https://github.com/AzureAD/microsoft-authentication-library-for-objc as the package repository URL
4. Choose package options with :
    1. Rules → Branch : main (For latest MSAL release)
    2. Rules → Version → Exact : [release version >= 1.1.14] (For a particular release version)

For any issues, please check if there is an outstanding SPM/Xcode bug.
Workarounds for some bugs we encountered :
* If you have a plugin in your project you might encounter [CFBundleIdentifier collision. Each bundle must have a unique bundle identifier](https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues/737#issuecomment-767311138) error. [Workaround](https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues/737#issuecomment-767990771)
* While archiving, error : “IPA processing failed” UserInfo={NSLocalizedDescription=IPA processing failed}. [Workaround](https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues/737#issuecomment-767990771)
* For a macOS app, “Command CodeSign failed with a nonzero exit code” error. [Workaround](https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues/737#issuecomment-770056675)

### Manually

1. Check out the repository using the following command.
```
git clone https://github.com/AzureAD/microsoft-authentication-library-for-objc.git --recursive
```

2. In your project, drag and add `microsoft-authentication-library-for-objc/MSAL/MSAL.xcodeproj`
3. In your project settings, for the target application, select `Build Phases` and add MSAL in `Target Dependencies`.

### Using Git Submodule

If your project is managed in a git repository you can include MSAL as a git submodule. First check the GitHub Releases Page for the latest release tag. Replace <latest_release_tag> with that version.

* `git submodule add https://github.com/AzureAD/microsoft-authentication-library-for-objc msal`
* `cd msal`
* `git checkout tags/<latest_release_tag>`
* `git submodule update --init --recursive`
* `cd ..`
* `git add msal`
* `git commit -m "Use MSAL git submodule at <latest_release_tag>"`
* `git push`

