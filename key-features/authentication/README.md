---
cover: ../../.gitbook/assets/sso (1).jpg
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Authentication

MapsIndoors Auth is handled in two ways:

* API keys - This is how apps built on top of the SDKs authorize by default,
* MapsIndoors Auth server - This is how the MapsIndoors CMS authorizes, as well as apps accessing secured solutions.

The MapsIndoors Auth server is located at [https://auth.mapsindoors.com](https://auth.mapsindoors.com/) - including [SSO page](https://auth.mapsindoors.com/login) and [OIDC metadata](https://auth.mapsindoors.com/.well-known/openid-configuration). The server is an [IdentityServer4](https://identityserver4.readthedocs.io/en/3.1.0/) implementation - with support for OAauth 2 and OIDC protocols.

It stores all users that are managed through the MapsIndoors CMS, as well as configurations for authentication providers. Based on these users and authentication providers, it can authenticate and authorize users in order to access the MapsIndoors CMS and secured MapsIndoors solutions.

This guide covers the different aspects of user authentication and authorization in the MapsIndoors JavaScript SDK.

Usually, access to the services behind the MapsIndoors SDK is restricted with API keys. However, as an additional layer of security and control, access can be restricted to users of a specific tenant. A MapsIndoors dataset can only be subject to user authentication and authorization if an integration with an identity provider exists. Current examples of such identity providers are _Google_ and _Microsoft_. These providers and more can be added and integrated to your MapsIndoors project by request.

We recommend using a library such as AppAuth to handle verification and response to get a token to use in the MapsIndoors SDK.



## Android v4

{% hint style="info" %}
If you are looking for documentation on Android SDK v3, please [see here](https://docs-legacy.mapsindoors.com/content/legacy/android\_v3/).
{% endhint %}

In order to utilize an OAuth2 login flow for your MapsIndoors project, you will need to provide some details to the OAuth2 client, like the _issuer URL_, _client id_, _scopes_ and possibly a _preferred identity provider_ if there are more than one option. These details can be fetched using `MapsIndoors.getAuthenticationDetails`

This can be called from MapsIndoors like this:

{% tabs %}
{% tab title="Java" %}
```java
MapsIndoors.getAuthenticationDetails("apikey", authDetails -> {
    //Check if authdetails is not null and auth is required for the api
    if (authDetails != null && authDetails.isAuthRequired) {
        //Through the authDetails you can retrieve the necessary data to create a auth request. Here is an example through the appAuth library
        AuthorizationServiceConfiguration.fetchFromIssuer(Uri.parse(authDetails.getAuthIssuer()), (serviceConfiguration, ex) -> {
            if (serviceConfiguration != null) {
                authorizationRequest = new AuthorizationRequest.Builder(serviceConfiguration, authDetails.getAuthClients().get(0).getClientId(), ResponseTypeValues.CODE, Uri.parse("redirectUri"))
                            .setAdditionalParameters(Collections.singletonMap("acr_values", "idp:" + authDetails.getAuthClients().get(0).getPreferredIDPs().get(0)))
                            .setScope("openid profile account client-apis").build();
                authorizationService = new AuthorizationService(this);
                Intent authIntent = authorizationService.getAuthorizationRequestIntent(authorizationRequest);
                startActivity(authIntent);
            }
        }
    }
}
```

You will also need to provide a _redirect url_, but this is not provided by MapsIndoors. The callback url may be a [Android app link](https://developer.android.com/studio/write/app-link-indexing).

> Note that the redirect link must be known to MapsIndoors and white-listed for your identity provider integration. You must inform us about all the links that you need for your application, both for development and production use so they can be white-listed.

After you have made the request if using the library you will have to react on the recieved intent from the user logging in.

With that you response you will create the token exchange request. The token exchange request will respond with a token if succesful that can be set through the `MapsIndoors` class by calling `setAuthToken`. The set access token is used by the MapsIndoors SDK, for remaining lifespan of the SDK. If the SDK is initialized again, a token will need to be set again.

```java
//Continuing the example of using the appAuth library together with MapsIndoors
@Override
protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    final Uri data = intent.getData();
    AuthorizationResponse authorizationResponse = new AuthorizationResponse.Builder(authorizationRequest).fromUri(data).build();
    authorizationService.performTokenRequest(authorizationResponse.createTokenExchangeRequest(), (response, ex1) -> {
        if (response != null) {
            if (response.accessToken != null) {
                MapsIndoors.setAuthToken(response.accessToken);
            }
        }
    });
}
```

You can now validate that you have set up the token correctly by calling `MPApiKeyValidatorService.checkAuthToken` with your solution key like this:

```java
private void checkApiKeyValidityAndInitializeSDK() {
    MPApiKeyValidatorService.checkAuthToken("apikey", error -> {
        if (error != null) {
            //An error happened, authentication was not succesful.
        }else {
            //You have now succesfully gotten access to a solution that requires authentication
            MapsIndoors.load(getApplicationContext(), "apikey", null);
        }
    }
}
```

The SDK will ensure all subsequent performed data requests will include the set access token.

> Note that the access token obtained from a MapsIndoors Single Sign-on flow cannot be used as access token for the [Booking service](https://docs.mapsindoors.com/user-authenticated-booking/). Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.
{% endtab %}

{% tab title="Kotlin" %}


In order to utilize an OAuth2 login flow for your MapsIndoors project, you will need to provide some details to the OAuth2 client, like the _issuer url_, _client id_, _scopes_ and possibly a _preferred identity provider_ if there are more than one option. These details can be fetched using `MapsIndoors.getAuthenticationDetails`

This can be called from MapsIndoors like this:

```kotlin
MapsIndoors.getAuthenticationDetails("d2540acd09f241d187994eec") { authDetails: MPAuthDetails? ->
    //Check if authdetails is not null and auth is required for the api
    if (authDetails != null && authDetails.isAuthRequired) {
        //Through the authDetails you can retrieve the necessary data to create a auth request. Here is an example through the appAuth library
        AuthorizationServiceConfiguration.fetchFromIssuer(Uri.parse(authDetails.authIssuer)) { serviceConfiguration: AuthorizationServiceConfiguration?, _: AuthorizationException? ->
            authorizationRequest = AuthorizationRequest.Builder(
                serviceConfiguration!!,
                authDetails.authClients[0].clientId,
                ResponseTypeValues.CODE,
                Uri.parse("mapsindoorsapp://"))
                .setAdditionalParameters(Collections.singletonMap("acr_values", "idp:" + authDetails.authClients[0].preferredIDPs[0]))
                .setScope("openid profile account client-apis").build()
            authorizationService = AuthorizationService(this)
            val authIntent = authorizationService!!.getAuthorizationRequestIntent(authorizationRequest!!)
            startActivity(authIntent)
        }
    }
}
```

You will also need to provide a _redirect url_, but this is not provided by MapsIndoors. The callback url may be a [Android app link](https://developer.android.com/studio/write/app-link-indexing).

> Note that the redirect link must be known to MapsIndoors and white-listed for your identity provider integration. You must inform us about all the links that you need for your application, both for development and production use so they can be white-listed.

After you have made the request if using the library you will have to react on the recieved intent from the user logging in.

With that you response you will create the token exchange request. The token exchange request will respond with a token if succesful that can be set through the `MapsIndoors` class by calling `setAuthToken`. The set access token is used by the MapsIndoors SDK, for remaining lifespan of the SDK. If the SDK is initialized again, a token will need to be set again.

```kotlin
//Continuing the example of using the appAuth library together with MapsIndoors
override fun onNewIntent(intent: Intent?) {
    super.onNewIntent(intent)
    val data = intent!!.data
    val authorizationResponse = AuthorizationResponse.Builder(authorizationRequest!!).fromUri(data!!).build()
    authorizationService!!.performTokenRequest(authorizationResponse.createTokenExchangeRequest()) { response: TokenResponse?, _: AuthorizationException? ->
        if (response != null) {
            if (response.accessToken != null) {
                MapsIndoors.setAuthToken(response.accessToken!!)
            }
        }
    }
}
```

You can now validate that you have set up the token correctly by calling `MPApiKeyValidatorService.checkAuthToken` with your solution key like this:

```kotlin
fun checkApiKeyValidityAndInitializeSDK() {
    MPApiKeyValidatorService.checkAuthToken("apikey") {
        if (it != null) {
            //An error happened, authentication was not succesful.
        }else {
            //You have now succesfully gotten access to a solution that requires authentication
            MapsIndoors.load(applicationContext, "apikey", null)
        }
    }
}
```

The SDK will ensure all subsequent performed data requests will include the set access token.

> Note that the access token obtained from a MapsIndoors Single Sign-on flow cannot be used as access token for the [Booking service](https://docs.mapsindoors.com/user-authenticated-booking/). Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.
{% endtab %}
{% endtabs %}

## iOS v3

{% hint style="danger" %}
**KNOWN IOS ISSUES**

1. Developing on the new Arm-based Apple Silicon (M1) Macs requires building and running on a physical iOS device or using an iOS simulator running iOS 13.7, e.g. iPhone 11. This is a temporary limitation in Google Maps SDK for iOS, and as such also a limitation in MapsIndoors, due to the dependency to Google Maps.
2. Note: Due to [a bug in CocoaPods](https://github.com/CocoaPods/CocoaPods/issues/7155) it is necessary to include the `post_install` hook in your Podfile described in the [PodFile post\_install](https://github.com/MapsIndoors/MapsIndoorsIOS/wiki/Podfile-post\_install) wiki.
{% endhint %}

In order to utilize an OAuth2 login flow for your MapsIndoors project, you will need to provide some details to the OAuth2 client, like the _issuer url_, _client id_, _scopes_ and possibly a _preferred identity provider_ if there are more than one option. These details can be fetched using `MapsIndoors.fetchAuthenticationDetails()`.

```swift
MapsIndoors.fetchAuthenticationDetails { details, error in
    if let authDetails = details {
        print("Issuer url: \(authDetails.authIssuer)")
        if let client = authDetails.authClients.first {
            print("Client ID: \(client.clientID)")
            if let idp = client.preferredIDPS.first {
                print("acr_values=idp:\(idp)")
            }
        }
        print("Scope: \(authDetails.authScope)")
    }
}
```

You will also need to provide a _redirect url_, but this is not provided by MapsIndoors. The callback url may be a [app url scheme](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app) or a [universal link](https://developer.apple.com/ios/universal-links/).

> Note that the redirect link must be known to MapsIndoors and white-listed for your identity provider integration. You must inform us about all the links that you need for your application, both for development and production use so they can be white-listed.

How to apply the authentication details is varying from each OAuth2 client, but you can see below how they are applied in a login flow using the [OAuth2 client library from Open Id](https://github.com/openid/AppAuth-iOS)

```swift
class SingleSignOnHelper {
    var authState: OIDAuthState?
    func openLoginFlow ( for controller:UIViewController, completion: @escaping () -> Void ) {
        MapsIndoors.fetchAuthenticationDetails { details, error in
            guard let authDetails = details else { return }
            guard let client = authDetails.authClients.first else { return }
            let issuer = URL(string: authDetails.authIssuer)!
            let redirectUrl = URL(string: "mapsindoorsdemo://auth")!
            OIDAuthorizationService.discoverConfiguration(forIssuer: issuer) { configuration, error in
                guard let config = configuration else {
                    return
                }
                var additionalParams:[String:String]? = nil
                if let idp = client.preferredIDPS.first {
                    additionalParams = ["acr_values":"idp:\(idp)"]
                }
                let request = OIDAuthorizationRequest(configuration: config,
                                                      clientId: client.clientID,
                                                      scopes: [OIDScopeOpenID, OIDScopeProfile, authDetails.authScope],
                                                      redirectURL: redirectUrl,
                                                      responseType: OIDResponseTypeCode,
                                                      additionalParameters: additionalParams)
                let appDelegate = UIApplication.shared.delegate as! AppDelegate
                appDelegate.currentAuthorizationFlow =
                OIDAuthState.authState(byPresenting: request, presenting: controller) { [self] authState, error in
                    if let authState = authState {
                        self.authState = authState
                        print("Got authorization tokens. Access token: " +
                              "\(authState.lastTokenResponse?.accessToken ?? "nil")")
                    } else {
                        print("Authorization error: \(error?.localizedDescription ?? "Unknown error")")
                        self.authState = nil
                    }
                    completion()
                }
            }
        }
    }
}
```

The above login flow can then be executed whenever needed in your application, typically in a View Controller in the beginning of your apps lifetime. If the login succeeds, an _access token_ will be issued from MapsIndoors, which should then be passed to the MapsIndoors SDK.

```swift
let authHelper = SingleSignOnHelper.init()
authHelper.openLoginFlow(for: self) {
    MapsIndoors.accessToken = authHelper.authState?.lastTokenResponse?.accessToken
}
```

The SDK will then make sure that all requests for data is performed using this access token.

> Note that the access token obtained from a MapsIndoors Single Sign-on flow cannot be used as access token for the [Booking Service](https://docs.mapsindoors.com/booking/). Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.



## Web v4

To utilize an OAuth2 login flow for your MapsIndoors project, you will need to provide some details to the OAuth2 client, like the _issuer url_, _client id_, _scopes_ and possibly a _preferred identity provider_ if there is more than one option. These details are available as arguments in the `MapsIndoors.onAuthRequired` callback.

```javascript
mapsindoors.MapsIndoors.onAuthRequired = async ({ authClients = [], authIssuer = '' }) => {
...
})
```

You are required to provide a _redirect\_url_. The authorization server will redirect the user back to the application using this URL after successful authorization.

> Note that the redirect link must be known to MapsIndoors and white-listed for your identity provider integration. You must inform us about all the links that you need for your application, both for development and production use so they can be white-listed. How to apply the authentication details is varying from each OAuth2 client, but you can see below how they are applied in a login flow using the [OAuth2 client library from Open Id](https://github.com/openid/AppAuth-JS)

```javascript
import { AuthorizationRequest, AuthorizationNotifier, BaseTokenRequestHandler, RedirectRequestHandler, AuthorizationServiceConfiguration, FetchRequestor, TokenRequest, GRANT_TYPE_AUTHORIZATION_CODE } from "@openid/appauth";
const requestor = new FetchRequestor();
const authorizationNotifier = new AuthorizationNotifier();
const authorizationHandler = new RedirectRequestHandler();
mapsindoors.MapsIndoors.onAuthRequired = async ({ authClients = [], authIssuer = '' }) => {
    //Fetch the service configuration.
    const config = await AuthorizationServiceConfiguration.fetchFromIssuer(authIssuer, requestor);
    //Check if the URL contains code and state in the hash. They will only be present after the authorization is done.
    if (window.location.hash.includes('code') && window.location.hash.includes('state')) {
        //Next we need to exchange the code to an access token.
        authorizationHandler.setAuthorizationNotifier(authorizationNotifier);
        authorizationNotifier.setAuthorizationListener(async (request, response, error) => {
            if (response) {
                const tokenHandler = new BaseTokenRequestHandler(requestor);
                //Build the token request.
                const tokenRequest = new TokenRequest({
                    client_id: request.clientId,
                    redirect_uri: `${window.location.origin}${window.location.pathname}`,
                    grant_type: GRANT_TYPE_AUTHORIZATION_CODE,
                    code: response.code,
                    state: '',
                    extras: { code_verifier: request?.internal?.code_verifier }
                });
                //Send the token request.
                tokenHandler.performTokenRequest(config, tokenRequest).then(response => {
                    //Assign the access to ken to MapsIndoors.
                    mapsindoors.MapsIndoors.setAuthToken(response.accessToken);
                });
            }
        });
        await authorizationHandler.completeAuthorizationRequestIfPossible();
    } else {
        const authClient = authClients[0];
        const preferredIDP = authClient.preferredIDPs && authClient.preferredIDPs.length > 0 ? authClient.preferredIDPs[0] : '';
        //Build to authorization request.
        const request = new AuthorizationRequest({
            client_id: authClient.clientId,
            redirect_uri: `${window.location.origin}${window.location.pathname}`,
            scope: 'openid profile account client-apis',
            response_type: AuthorizationRequest.RESPONSE_TYPE_CODE,
            extras: { 'acr_values': `idp:${preferredIDP}`, 'response_mode': 'fragment' }
        });
        //Send the authorization request.
        authorizationHandler.performAuthorizationRequest(config, request);
    }
    //Clean up the url when the authentication is done.
    history.replaceState(null, '', `${window.location.origin}${window.location.pathname}${window.location.search}`);
})
```

The above login flow is executed by the SDK if authentication is needed.

The SDK will then make sure that all requests for data are performed using this access token.

For a full example, please [see here](https://github.com/MapsPeople/JS-SDK-Examples/tree/main/single-sign-on).

> Note that the access token obtained from a MapsIndoors Single Sign-on flow cannot be used as access token for the Booking Service. Single Sign-on access tokens are issued by MapsIndoors and not the underlying tenant. You need to login directly on your Booking tenant to get an access token that can be used for working with the Booking Service as an authenticated user.
