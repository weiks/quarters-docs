﻿# Quarters Unity SDK - PlayFab Module

PlayFab Module allows transferring quarters from the Quarters app to the users through PlayFab cloud script.
This allows custom logic to be injected to the PlayFab server to verify if transfer request can be authorized.

» **[Download Quarters Unity SDK.](http://downloads.pocketfulofquarters.com/quarters-sdk-latest.unitypackage)**

## Videos
Make sure to check out our step-by-step SDK setup videos on our [Youtube channel](https://www.youtube.com/channel/UCLo7viliyZV7p_GxOZf_MMw/playlists)

## Prerequisites:
- PlayFab SDK must be installed prior to module installation [(download)](https://api.playfab.com/sdks/unity).

- Playfab Title

## Installation:
1.  Import content of `QuartersSDK/Modules/PlayFab/PlayFabModule.unitypackage` to your project
2.  Add `QUARTERS_MODULE_PLAYFAB` scripting define in Player Settings. If you are supporting more
    than one platform add `QUARTERS_MODULE_PLAYFAB` scripting define to each platform tab in Player Settings
3.  Open `QuartersSDK/Modules/PlayFab/QuartersPlayFabModuleServer.txt`,
    copy `SERVER_TOKEN` for your Quarters App,
    copy `APP_ADDRESS` for your Quarters App (found in `Quarters app settings/Edit app`)

Example:

    var SERVER_TOKEN = "xk9q9fdxhbu65lyplqhwqe29klduvtoj9izi0g4wgrdy56b1dd32";
    var APP_ADDRESS = "0x1e2aa0184dcde9502568593d6c5da68966a32712";

4.  Add whatever custom logic you require using JavaScript in QuartersPlayFabModuleServer.txt in the marked section.


```
 /*
    ######## ENTER YOUR CUSTOM LOGIC HERE,
    BY DEFAULT EVERY AWARD REQUEST IS ACCEPTED
*/
```

This step is critical, custom logic MUST be used. Otherwise any client side request will transfer Quarters out from the Quarters app.

5.  Copy content of QuartersPlayFabModuleServer.txt to your existing Playfab Cloud Script. If you don't have any existing Cloud Script code you can simply
    upload QuartersPlayFabModuleServer.txt stright to Automation section in PlayFab console.




AwardQuarters
---
Award quarters server call allows transferring quarters from Quarters app to user. It can be called from client side through the cloud script handler as well as directly from another Cloud Script function when
Quarters should be awarded to the user.



Example 1:

In this example user requests 2 quarters to be awarded to him. Typical "real life" example would be daily reward for login in to the game.
Call is made from the client side to cloud script server. During the AwardQuarters call cloud script checks when last time reward was given to the user.
Authorization is for this transaction is granted or declined by Cloud Script.

***# Client Side***

    using QuartersSDK;
    using PlayFab;
    using PlayFab.ClientModels;


    //login user to playfab title using device id
    LoginWithCustomIDRequest loginRequest = new LoginWithCustomIDRequest();
    loginRequest.CustomId = SystemInfo.deviceUniqueIdentifier;
    loginRequest.CreateAccount = true;


    PlayFabClientAPI.LoginWithCustomID(loginRequest, delegate(LoginResult result) {

        Debug.Log("Playfab user logged in: " + result.PlayFabId);

        //Request 2 quarters from Playfab Cloud build
        Quarters.Instance.AwardQuarters(2, delegate(string transactionHash) {

            Debug.Log("Quarters awarded: " + transactionHash);


        }, delegate (string error) {

            Debug.LogError(error);

        });

    }, delegate (PlayFabError error){
        Debug.LogError(error.ErrorMessage);
    });



***
***# Cloud Script Side***

    var TRANSFER_URL = "https://api.pocketfulofquarters.com";
    var SERVER_TOKEN = "xk9q9fdxhbu65lyplqhwqe29klduvtoj9izi0g4wgrdy56b1ddle21"; //ENTER SERVER TOKEN HERE
    var APP_ADDRESS = "0x1e2aa0184dcde9502568593d6c5da68966a3275adasda"; //ENTER APP ADDRESS HERE


    //PlayFab Cloud script handler
    handlers.AwardQuarters = function (args, context) {
        var result = AwardQuarters(args);
        return result;
    };



    function AwardQuarters (args) {

        var amount = args["amount"];
        var user = args["userId"];

        if (SERVER_TOKEN == "") {
            log.error("Missing SERVER_TOKEN parameter");
            return;
        }
        if (APP_ADDRESS == "") {
            log.error("Missing APP_ADDRESS parameter");
            return;
        }
        if (amount == undefined || amount == null) {
            log.error("Missing amount parameter");
            return;
        }
        if (user == undefined || user == null) {
            log.error("Missing user parameter");
            return;
        }

        log.info("Award " + amount + " quarters to user: " + user);


        var requestAuthorized = true;

        /*
           ######## ENTER YOUR CUSTOM LOGIC HERE,
           BY DEFAULT EVERY AWARD REQUEST IS ACCEPTED


           var shouldReceiveDailyBonus = !DidReceivedBonusToday(userId); //DidReceivedBonusToday could be a custom logic method checking Playfab user data

           if (shouldReceiveDailyBonus == false) {
               log.error("Daily bonus already given today");
               requestAuthorized = false;
           }

           if (amount != DAILY_BONUS_AMOUNT) {
               log.error("Requested daily bonus amount is incorrect");
               requestAuthorized = false;
           }


        */


        if (!requestAuthorized) return { error: "Award Quarters request not authorized by game server" };

        var postData = {
            "amount": amount,
            "user": user
        };

        var url = TRANSFER_URL + "/v1/accounts/" + APP_ADDRESS + "/transfer";

        var headers = {
            'Authorization': 'Bearer ' + SERVER_TOKEN,
            'Content-Type': 'application/json;charset=UTF-8'
        };

        var contentType = "application/json";
        var contentBody = JSON.stringify(postData);
        var response = http.request(url, "post", contentBody, contentType, headers);

        return response;
    }
