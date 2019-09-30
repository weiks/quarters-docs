# Quarters Unity SDK - v0.50.0

**Supported versions**: Unity 2017+

**Supported platforms**: `iOS` and `Android`.

» **[Download Quarters Unity SDK.](http://downloads.pocketfulofquarters.com/quarters-sdk-latest.unitypackage)**

## Videos
Make sure to check out our step-by-step SDK setup videos on our [Youtube channel](https://www.youtube.com/channel/UCLo7viliyZV7p_GxOZf_MMw/playlists)

## Example
[A sample project](https://github.com/weiks/q-slots) to play slots on mobile with Quarters.

## Configuration
Follow [Getting Started guide](/getting-started) to create your Quarter App.

## Unity integration
Import `Quarters SDK` from `Unity Asset Store`, add `QuartersSDK/Prefab/QuartersInit.prefab` to your first scene, then copy your `APP_ID` and `APP_KEY` to QuartersInit inspector.


## Platform specific setup

### iOS
No platform specific setup is needed for iOS. Deep linking setup is handled automatically by a post process.

[Click here](https://www.youtube.com/watch?v=YJU192xItO0&list=PL0TyRhVVizdBsWYGCcXJ-h43ybYBYm_Gu) to watch a video on how to get started with Quarters SDK for iOS in under 3 mins!

---

### Android
In Unity top menu go `Quarters/Android/Generate Android Manifest`. Android Manifest will automatically generate enabling deep linking.

If your project contain Android Manifest already, manual manifest will be required.

[Click here](https://www.youtube.com/watch?v=YJU192xItO0&list=PL0TyRhVVizdBk-apKTf6lBUAMkEc7E4Y7) to watch a video on how to get started with Quarters SDK for Android!


Authorization
----
Before you can use Quarters API you must authorize users session. From version 0.20.0 Quarters Unity SDK support guest mode. This allows to authorize user as guest and give full access
to Quarters features. Guest users can be converted to email users through Quarters.SignUp method. This workflow is required by Apple to conform to their user guidelines.

Example:

```
using QuartersSDK;

Quarters.Instance.AuthorizeGuest(OnAuthorizationSuccess, OnAuthorizationFailed);

 public void OnAuthorizationSuccess() {
      Debug.Log("OnAuthorizationSuccess");
}
   public void OnAuthorizationFailed(string error) { 
      Debug.LogError("OnAuthorizationFailed: " + error);  
}
```

----
From this point user is able to buy, spend and get rewarded with Quarters. At some point user should be prompted by the app for signing up to create password protected Quarters account.
Only fully signed up users are able to use Quarters account between apps.

Example of converting guest user to signed up account:

```
Quarters.Instance.SignUp(OnAuthorizationSuccess, OnAuthorizationFailed);

public void OnAuthorizationSuccess() {
    Debug.Log("OnAuthorizationSuccess");
}

public void OnAuthorizationFailed(string error) {
    Debug.LogError("OnAuthorizationFailed: " + error);
} 
```

Get User Details
----
Get user details can be only called after successful authorization. Success delegate returns User object with basic user details.


Example:

```
using QuartersSDK;

Quarters.Instance.GetUserDetails(
    delegate(User user) { 
        Debug.Log("User loaded");  
    }, delegate (string error) { 
        Debug.LogError("Cannot load the user details: " + error);  
    }
);  
```

Get accounts
----
Get account can be only called after successful authorization. On success delegate will be called with a list of user accounts. (Note: only one user account is currently supported by the API)


Example:
----

```
using QuartersSDK;

Quarters.Instance.GetAccounts(
    delegate (List<User.Account> accounts) { 
        //success 
    }, delegate (string error) { 
        //failed  
    }
);
```

Get Account Balance
----
Get account balance can be only called after successful authorization. On success delegate is fired with User.Account.Balance object


Example:

```
using QuartersSDK;

Quarters.Instance.GetAccountBalance(
    delegate (User.Account.Balance balance) {  },
    delegate (string error) {   }
); 
```

Transfer
----
Transfer request can be only called after successful authorization. This call allows to charge user quarters that will be transferred to your Quarters app. Transfer request is done through external browser, similar to Authorize call. On Success transaction hash is returned. Please note it can take up to 3 minutes for your transfer to appear.


Example:

```
using QuartersSDK;

TransferAPIRequest request = new TransferAPIRequest(
    int.Parse(tokensInput.text),
    descriptionInput.text,
    delegate (string transactionHash) {             
        //success  
    }, delegate (string error) {
        //failed 
    }
); 
transfer Quarters.Instance.CreateTransfer(request); //start
```


*************
RELEASE NOTES
*************

General notes:
- only one account is currently supported by API, hence Get Accounts and Account balance calls are working with first returned account

#####
0.20.2
- fixed transfer issues when using guest account
#####


#####
0.20.1
- fixed issue with missing callback when using SignUp
#####


#####
0.20.0
- added guest mode for frictionless authentication
#####


#####
0.12.0
- easier to integrate iOS and Android In App Purchases
#####


#####
0.11.0
- Added Playfab IAP module
- Added In App Purchases support for iOS
#####


#####
0.10.0
- Web view support
- Authorize and Transfer requests can now be done within the app through webview, not external browser (Supported platforms, iOS, Android, Unity Editor on MacOS)
- Optional forceExternalBrowser added to Authorize and Transfer calls (false by default)
false
#####


#####
0.9.0
- Sessions persistance added. User will wont have to authorize quarters on every login anymore, only first time.
- Added deauthorize functionality to log user out of quarters
#####


#####
0.8.1
- Fixed out of range exception when calling GetAccountBalance right after GetUserDetails
#####


#####
0.8.0
- Added PlayFab module
- Supporting Playfab server side Quarters apps to user transfers
#####


#####
0.7.0
- Quarters UI added
- Editor scripts are now inheriting from UnityEditor.Editor to avoid potential conflicts with custom Editor namespace
#####


#####
0.6.0
- Added automated Android Manifest Generation in Quarters/Android/Generate Android Manifest
- Android package name can now contain uppercase characters
#####


#####
0.5.0
- Deep linking is now dynamic. Multiple apps using Quarters SDK are now supported on single Android or iOS device
#####


#####
0.4.0
- Added URIParser to better deep link parameters handling
- Full Transfer support on iOS and Android
- Added automated iOS post process builds
- iOS deep linking
#####


#####
0.3.0
- added seamless support for GetAccounts call
- added seamless support for GetAcdountBalance
- basic one way support for Transfering quarters added

known issues:
- no deep linking with parameters support for TransferRequest. Doe to lack of API support for this functionality. Its coming in next release.
#####


#####
0.2.0
- Added Android deep linking. Quarters SDK will now automatically handle URL schemes
- Android support added for oauth calls outside the app
- Basic Android Manifest added for handling linking, intents and permissions
- Android support for Authorize call
- Example - added simple on screen console
#####


#####
0.1.0
- Added simple authorization in editor
- Refresh and access token are handled during Authorization
- Added GetUserDetails API call with refreshing access token if expired
#####
