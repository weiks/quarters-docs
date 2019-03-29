# Quarters Unity SDK - PlayFab In App Purchases Module

PlayFab Module allows very simple way for the user to purchase Quarters using platform native In App Purchases process

---

## Videos
Make sure to check out our step-by-step SDK setup videos on our [Youtube channel](https://www.youtube.com/channel/UCLo7viliyZV7p_GxOZf_MMw/playlists)

**Supported platforms**: iOS

---

##Prerequisites:
* `QuartersPlayFabModule` correctly configured and PlayFab Title configured. If you haven't done this step install `QuartersSDK/Modules/PlayFab/PlayFabModule.unitypackage` and follow the documentation.
* `Unity IAP` Package https://docs.unity3d.com/Manual/UnityIAPSettingUp.html
* For iOS:
    - iTunes Connect account with valid "Paid Applications" agreements. This step is critical for testing and validating purchases
    - iTunes Connect App setup with added IAP Consumable products


---


##Installation:
1.  Import content of `QuartersSDK/Modules/PlayFab/PlayFabIAPModule.unitypackage` to your project
2.  Add `Unity IAP` package to your project
3.  Add content of `QuartersSDK/Modules/PlayFabIAP/QuartersPlayFabIAPServer.txt` to your existing PlayFab Cloud Script.
4.  Setup IAP Products on `iTunesConnect/<YourApp>/Features`
    Requirements for Quarters IAP products (critical, make sure this is configured correctly)

    Example:
    ```
    Reference Name: 4 Quarters
    Type: Consumable
    Product ID: 4Quarters
    ```

    **Note:** All quarters IAP product IDs MUST follow this naming convention:

        <numberOfQuarters>Quarters

        examples:
            4Quarters   
            8Quarters
            ..

    To test IAP Purchases with iOS you don't have to submit Products to review. If "Paid Applications" agreements is valid and product is in "Ready to Submit" status testing purchases
    with Apples sandbox server will work. For more information how to test Apple IAP follow this link: https://developer.apple.com/apple-pay/sandbox-testing/



---


Buying Quarters through PlayFab In App Purchases Module


1.  Authorize user with Quarters
2.  Call `Quarters.Instance.GetUserDetails`
3.  Login PlayFab user with PlayFab.

    Example:

        //login user to playfab title using device id
        LoginWithCustomIDRequest loginRequest = new LoginWithCustomIDRequest();
        loginRequest.CustomId = SystemInfo.deviceUniqueIdentifier;
        loginRequest.CreateAccount = true;


        PlayFabClientAPI.LoginWithCustomID(loginRequest, delegate(LoginResult result) {

            Debug.Log("Playfab user logged in: " + result.PlayFabId);

        }, delegate (PlayFabError error){
            Debug.LogError(error.ErrorMessage);
        });


3. Call `QuartersIAP.Initialize` - All products data will be pulled from store server

    Example:

        QuartersIAP.Instance.Initialize(testProducts, delegate(Product[] products) {

        //products loaded

        }, delegate(InitializationFailureReason reason) {
            Debug.LogError(reason.ToString());
        });



4. You can now call QuartersIAP.Instance.BuyProduct - Native popup will be presented to the user to confirm the purchase. After successful payment Playfab IAP module will validate transaction and
   automatically transfer quarters to user account.

    Example:

        QuartersIAP.Instance.BuyProduct(QuartersIAP.Instance.products[0], (Product product, string txId) => {

            Debug.Log("Purchase complete");

        },(string error) => {
            Debug.LogError("Purchase error: " + error);

        });



For another example look at `Assets/QuartersSDK/Example/Example scene`
