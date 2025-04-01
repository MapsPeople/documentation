---
icon: lock-keyhole
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: true
---

# Authentication

This section explains how to authenticate your application with the MapsIndoors API to use the `@mapsindoors/built-in-map-edits` package. MapsIndoors uses the OAuth 2.0 authorization framework and OpenID Connect (OIDC) for authentication.

***

## ✅ Getting Ready to Connect

To enable your application to connect to MapsIndoors, you need a `client_id`. This identifier is used to register your application with the MapsIndoors authentication service. To obtain your `client_id`, please reach out to MapsPeople support and provide the following details:

* **Application Name & Type**
* **Redirect URIs (must be registered with MapsPeople)**
* **IdP Configuration (if applicable):**
  * **Metadata URL (preferred)**
  * **OR: Issuer, Authorization, Token, and JWKS URLs**
* **Contact Info**

This information allows MapsPeople to configure the authentication service to work with your application, so that your application can securely access the MapsIndoors Platform.

### The Authentication Process

#### **Authentication Flow:**

1. **User Access:** A user attempts to use a feature that requires MapsIndoors data.
2. **Login Redirect:** Your application redirects the user to the MapsIndoors login (or your organization's IdP).
3. **User Authorization:** The user logs in and authorizes your application.
4. **Authorization Code:** MapsIndoors (or IdP) redirects back to your application with an authorization code.
5. **Token Exchange:** Your application exchanges the code for an access token.
6. **API Access:** Your application uses the access token to make MapsIndoors API requests.

Users must authenticate with MapsIndoors, even if they are already logged in to your application. If your organization uses an external Identity Provider (IdP) and the user is already logged in there, this step may happen automatically (single sign-on). The exact user experience during this authentication step depends on your implementation.  For the best user experience, we recommend handling the process in a secondary window to avoid redirecting the user away from your application.

We recommend using [`oidc-client-ts`](https://github.com/authts/oidc-client-ts) to manage this process, as it simplifies authentication and token exchange. For practical implementation guidance, explore the [`oidc-client-ts` sample projects](https://github.com/authts/oidc-client-ts/tree/main/samples/Parcel/src) on GitHub. These samples demonstrate various authentication flows and configurations, providing valuable insights and learning opportunities.

### Sample oidc-client-ts Configuration

Here's a sample configuration for `oidc-client-ts`:

```javascript
const settings = {
    authority: 'https://auth.mapsindoors.com/',
    client_id: '{YOUR_CLIENT_ID}', // Replace with your actual client ID provided by MapsPeople.
    redirect_uri: '{YOUR_REDIRECT_URI}', // Replace with your configured redirect URI provided to MapsPeople.
    response_type: 'code', // Use the authorization code flow.
    scope: 'openid profile manager', // Specify the requested permissions.
};
```

**Important Notes about the Configuration:**

* Replace `{YOUR_CLIENT_ID}` with the actual `client_id` provided by MapsPeople. This is the unique identifier for your application.
* Replace `{YOUR_REDIRECT_URI}` with the redirect URI you configured with MapsPeople. This URL must match the one provided during the `client_id` registration process.
* The `response_type` is set to `code`, indicating that the authorization code flow is being used.
* The `scope` parameter specifies the permissions your application is requesting. `openid` is required for OpenID Connect, `profile` provides access to user profile information, and `manager` provides access to the MapsIndoors management API.&#x20;

***

## 🚀 Using the Access Token

Once the login sequence is successful, you will receive a MapsIndoors access token. This token is used to authenticate your requests to the MapsIndoors API. To use the access token with the `@mapsindoors/built-in-map-edits` package, set the `accessToken` property:

```javascript
MapsIndoorsEditor.accessToken = accessToken;
```

***

## 💡 **Additional Resources**

For more information on OAuth 2.0 and OpenID Connect, please refer to the following resources:

* OAuth 2.0: [oauth.net/2/](https://oauth.net/2/)
* OpenID Connect: [openid.net/](https://openid.net/)
* Recommended JavaScript authentication libraries: [oauth.net/code/javascript/](http://oauth.net/code/javascript/)

