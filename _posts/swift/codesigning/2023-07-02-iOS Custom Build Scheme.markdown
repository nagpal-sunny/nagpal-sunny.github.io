---
layout: post
author: Saurav Nagpal
title: "Distribute iOS Beta Build (Scheme & Configurations)"
pin: true
date: 2023-07-02 17:01:10 +0530
categories: [iOS, iOS Beta Build]
tags: [iOS scheme, iOS build config]
published: true
sitemap: true
content: true
---
<style type="text/css">
  body{
  font-family:Roboto;
  font-size: 14pt;
}
</style>

##  <img src="/Assest/common/overview.png" width="30" height="30"> Overview

Having multiple build configurations during the application's development phase is a good practice. Customers can ask for both debug and release configuration builds. Beta and Alpha testing is an important phase of the application life cycle. Beta and Alpha builds might have a different configurations from the release build. This blog explains one of the ways to handle this through an XCode scheme.

<br>

## <img src="/Assest/common/problem.png" width="30" height="30"> Problem Statement

Suppose you are working on an e-commerce application `ShopTime`. In the development phase, multiple backend environments need to support (Dev, QA, UAT, Release). To differentiate between the application build (Development, QA, Beta builds etc) separate application icons are provided by the customer. These builds also have a unique bundle-id. The launch screens should also be different for each build so that users can easily identify the build. Configuration can support multiple features however for this blog we will focus on the following:

- Bundle Identifier
- App Icon
- Launch Screen
- Backend URL

<br>
## <img src="/Assest/common/implementation.png" width="30" height="30"> Implementation

XCode support multiple configurations in a single target. By default, XCode provides debug and release configuration. Let's start step by step the process:

### Step 1 Add a new configuration (Beta configuration)

####  i) Navigate to the project setting
<br>
<img src="/Assest/iOS/betaconfig/betaConfigStart.png">

<br>
####  ii) Tap on the "+" button in the configuration section and duplicate the debug configuration(you can choose duplicate of release based on requirement). 

<br>
<img src="/Assest/iOS/betaconfig/duplicateDebugConfig.png">

<br>
####  iii) Enter the name of the configuration (BetaDebug). 
<br>
<img src="/Assest/iOS/betaconfig/nameBetaDebug.png">

<br>
### Step 2 Change product bundleid for BetaDebug

####  i) Move to Target-> Build Setting-> Search(Product Bundle Identifier)

<img src="/Assest/iOS/betaconfig/productBundleIdentifier.png">

<br>
####  ii) Set the bundle identifier as per your requirement. 

<img src="/Assest/iOS/betaconfig/renameProductBundleId.png">

<br>
### Step 3 Set separate application icon for BetaDebug 

####  i) Add a new AppIcon asset in Assets with a specific name(AppIconBeta).
<br>
<img src="/Assest/iOS/betaconfig/betaAppIcon.png">

<br>
####  ii) Add AppIconBeta in the BetaDebug configuration.
<img src="/Assest/iOS/betaconfig/configBetaDebug.png">

<br>
####  iii) Finally!, a separate application icon is set for BetaDebug.
<img src="/Assest/iOS/betaconfig/appIconBetaConfigured.png">

<br>
### Step 4 Set separate Launch screen for BetaDebug 
<br>

####  i) Add a new launch screen for Beta config. 
<br>
<img src="/Assest/iOS/betaconfig/betaLaunchOption.png">

<br>
####  ii) Edit launch screen configuration for BetaDebug.
<img src="/Assest/iOS/betaconfig/configBetaLaunchDebug.png">

<br>
####  iii) Finally!, a separate Launch screen is set for BetaDebug.
<img src="/Assest/iOS/betaconfig/launchSetupConfigDebug.png">

<br>
### Step 4 Add a user setting for the backend URL

####  i) Add a new user-defined setting in the target setting. 
<br>
<img src="/Assest/iOS/betaconfig/addUserDefined.png">
 
<br>
####  ii) Name the configuration "Backend_URL"(choose your own name) and change the URL string for BetaConfig.
<img src="/Assest/iOS/betaconfig/updateForBetaConfig.png">
 
<br>
####  iii) Add the entry in `info.plist` for the backend URL.
<img src="/Assest/iOS/betaconfig/editInfoPlist.png">

<br>
####  iv) Fetch the user-defined setting in the code.
{% highlight swift %}
if let urlString = Bundle.main.object(forInfoDictionaryKey: "Backend URL") as? String {
    print(urlString)
}
{% endhighlight %}
