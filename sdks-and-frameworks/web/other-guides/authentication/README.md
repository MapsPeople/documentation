---
description: Web v4
---

# Authentication

MapsIndoors Auth is handled in two ways:

* API keys - This is how apps built on top of the SDKs are authorized by default,
* MapsIndoors Auth server - This is how the MapsIndoors CMS authorizes, as well as apps to access secured solutions.

The MapsIndoors Auth server is located at [https://auth.mapsindoors.com](https://auth.mapsindoors.com/) - including [SSO page](https://auth.mapsindoors.com/login) and [OIDC metadata](https://auth.mapsindoors.com/.well-known/openid-configuration). The server is an [IdentityServer4](https://identityserver4.readthedocs.io/en/3.1.0/) implementation - with support for OAauth 2 and OIDC protocols.

It stores all users that are managed through the MapsIndoors CMS, as well as configurations for authentication providers. Based on these users and authentication providers, it can authenticate and authorize users in order to access the MapsIndoors CMS and secured MapsIndoors solutions.

This guide covers the different aspects of user authentication and authorization in the MapsIndoors JavaScript SDK.

Usually, access to the services behind the MapsIndoors SDK is restricted with API keys. However, as an additional layer of security and control, access can be restricted to users of a specific tenant. A MapsIndoors dataset can only be subject to user authentication and authorization if integration with an identity provider exists. Current examples of such identity providers are _Google_ and _Microsoft_. These providers and more can be added and integrated to your MapsIndoors project by request.

We recommend using a library such as AppAuth to handle verification and response to get a token to use in the MapsIndoors SDK.

***

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
