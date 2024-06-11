# Client credentials flow

An alternative to login in with a username and password would be using the client credentials flow instead. The main difference from using the user/pass flow is that client credentials flow uses a client secret instead. By doing that your scripts can request an access to Mapsindoors resources using their own credentials, not on behalf of a user.

With this each script can have it's own secret which can be rotated programmatically so scripts does not need to be updates every time you update your password for example.

To use this flow a payload should be send formatted like this:

{% code overflow="wrap" %}
```
grant_type=client_credentials&client_id=your_client&client_secret=your_secret
```
{% endcode %}

If the login is successful, an access token response will be returned formatted in the same way as with user and password.

If you would like to start using client credentials flow, ask us for a `client_id` and a `customerId` for your application clients.

When each script has it's own secret, these can be rotated frequently or even at every login. To do that the script can login using it's current secret, then create a new secret and delete the old one. The next time the script is run, the new secret will be used and then immediately be replaced by the next one.



### Maintaining client secrets

Client secrets can added and deleted via this endpoint:

```
https://auth.mapsindoors.com/api/clientcredentials
```

With this you can use GET, POST and DELETE HTTP commands to get an overview of your secrets, create new and delete secrets respectively.

As this endpoint is contains sensitive information you will need to be authorized. Calling the endpoint it requires a valid access (bearer) token. This can be done using a username/password or a client secret.



#### Creating a secret

To create a secret you will need the POST command with two variables, customerId and an (optional) description of you key:&#x20;

```
customerId=xxx&description=MyNewKey
```

And body content formatted like this:

<pre><code><strong>grant_type=client_credentials&#x26;client_id=xxx&#x26;client_secret=yyy
</strong></code></pre>

This will create a new client secret that will give your script access to you Mapsindoors data. The endpoint will then respond with 200 OK with a json object formatted like this example:

{% code fullWidth="false" %}
```json
{
    "id": "abcdbe9e8084022af463000",
    "description": "MyNewKey",
    "createdAt": "2024-05-27T14:26:36.9981013Z",
    "secret": "Pv2A7iGsui9ys8bMn0m5J4uuhswt2AdMGOr6c0SzWK9zxFR4Cx26Cge2UVZOx3a1K5Wr7j700k8PsJjNz7o2WFGZRSE9z5PJ"
}
```
{% endcode %}

The secret can now be used to log in. Note that MapsPeople does not store secrets so if you lose it you will need to create  a new one instead and delete the old one.



#### Delete a secret

To delete a secret you will need the DELETE command with two variables, customerId and the id of the secret you want to delete

```
customerId=xxx&id=abcdbe9e8084022af463000
```

That's it. After deleting your secret you should receive a 200 OK response



#### Get a list of active secrets

To get a list of your current, active secret(s) you will need the GET command with one variables:  customerId.

```
customerId=xxx&id=abcdbe9e8084022af463000
```

This will return 200 OK with a list of secrets in JSON formatted like this:

```json
[
    {
        "id": "abcdbe9e8084022af463000",
        "description": "MyNewKey",
        "client_id": "xxx",
        "createdAt": "2024-05-27T13:46:31.843Z"
    }
]
```

Note that each secret has a description and a creation time, but the secrets themselves will **not** be available (as these are not stored)
