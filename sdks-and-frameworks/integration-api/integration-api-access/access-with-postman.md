# Access with Postman

### Setting up access using Postman[​](https://docs.mapsindoors.com/access-with-postman#setting-up-access-using-postman) <a href="#setting-up-access-using-postman" id="setting-up-access-using-postman"></a>

As an alternative to use Swagger, the integration API can be accessed via [Postman](https://www.postman.com/) by importing the swagger definition. Postman is a free of charge tool and can either be [downloaded as a standalone tool](https://www.postman.com/downloads) or you can create a new user (for free) and [use it online](https://web.postman.co/home).

Either way, you will need to do three things before starting using Postman: Importing the REST Mapsindoors Integration API definition, tell postman where to make it's calls and finally set up Autorization for it.

#### Importing to Postman[​](https://docs.mapsindoors.com/access-with-postman#importing-to-postman) <a href="#importing-to-postman" id="importing-to-postman"></a>

The first thing you need before getting started is importing the Mapsindoors Integration API definition to Postman. First click import, paste this link and click import:

> [https://integration.mapsindoors.com/swagger/v1/swagger.json](https://integration.mapsindoors.com/swagger/v1/swagger.json)

<figure><img src="https://docs.mapsindoors.com/img/api/postmanimport.png" alt=""><figcaption></figcaption></figure>

#### Direct Postman to the API.[​](https://docs.mapsindoors.com/access-with-postman#direct-postman-to-the-api) <a href="#direct-postman-to-the-api" id="direct-postman-to-the-api"></a>

Now a new collection called "Integration API" will be created. Select it and change the Variable "baseUrl" to: [https://integration.mapsindoors.com](https://integration.mapsindoors.com/)

> _**NOTE:**_ ⚠️ Remember to save changes! click the floppy disk icon.

!\[Postman BaseVariable]\(\{{ site.url \}}/assets/api/v1/postmanbasevariable.png)

<figure><img src="https://docs.mapsindoors.com/img/api/SwaggerLogin.png" alt=""><figcaption></figcaption></figure>

#### Setup Postman authorization[​](https://docs.mapsindoors.com/access-with-postman#setup-postman-authorization) <a href="#setup-postman-authorization" id="setup-postman-authorization"></a>

Finally we need to set authorization up for it. Go to the Authorization tab and change "Type" from No Auth to OAuth2. Here select Password Credentials as Grant Type.

Now set the:

* "Access Token URL" to `https://auth.mapsindoors.com/connect/token`
* "Client ID" to `client`
* Username to your username used in the [CMS](https://cms.mapsindoors.com/).
* Password to your password
* Scope to `integration`

Click "Get New Access Token" and a postman will log in using your credentials. Be sure to save your changes again by clicking the floppy disk icon.

<figure><img src="https://docs.mapsindoors.com/img/api/postmanAuth.png" alt=""><figcaption></figcaption></figure>

You should now be able to access your mapsindoors data in Postman.

<figure><img src="https://docs.mapsindoors.com/img/api/postmanGetExample.png" alt=""><figcaption></figcaption></figure>
