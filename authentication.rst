Authentication
==============

Our API uses OAuth 2.0 for authentication, specifically the *client credentials* grant type.  
The authentication process consists of requesting an access token with your organization's 
*API key* and *API secret*, and using this access token in the ``Authentication`` HTTP header 
of any subsequent requests.

Getting your API credentials
----------------------------

To generate API key/secret pairs, go to the `System Settings <https://crewsense.com/Application/ControlPanel/Options/>`_ page, click "Integrations", and click "Generate new API credentials". The credentials will be listed in the table on that page.

If you no longer use the API credentials or you suspect they have been compromised, please delete them, and generate new ones instead, if needed.

Receiving an access token
-------------------------

You use access tokens to authorize any requests made towards our API. To request an access token,
issue a POST request to ``https://api.crewsense.com/oauth/access_token`` like so:

curl
^^^^
::

   curl -v https://api.crewsense.com/oauth/access_token \
        -d "client_id=YOUR_CLIENT_ID" \
        -d "client_secret=YOUR_SECRET_KEY" \
        -d "grant_type=client_credentials"

Substitute your credentials for ``YOUR_CLIENT_ID`` and ``YOUR_SECRET_KEY``.

If the request is successful and you credentials are correct, you should receive a JSON string like this::

    {
        "access_token": "DZs3IeaMP5uEAc2I19kJYl8Tbvsmgq9GaPQPaMjN",
        "token_type": "bearer",
        "expires": 1426274440,
        "expires_in": 86400
    }

The ``token_type`` signifies that you have to use HTTP headers to authorize requests (see the next section).
``expires`` is a UNIX timestamp of the expiration date of the token (after which you have to request a new one). ``expires_in`` shows the expiration length in seconds.

.. note::
    
    Client access tokens currently expire in one week. If you try to use an expired access token, you 
    might receive a response like this::

        {
            "status": 401,
            "error": "unauthorized",
            "error_message": "Access token is not valid"
        }

    In this case, you simply have to request a new access token using the method described above.

Authorizing requests
--------------------

To access protected resources in the API, you have to sign the HTTP requests with an ``Authorization`` header, using the ``access_token`` acquired in the previous step::

    Authorization: Bearer DZs3IeaMP5uEAc2I19kJYl8Tbvsmgq9GaPQPaMjN

curl example
^^^^^^^^^^^^
::

   curl -v https://api.crewsense.com/v1/schedule \
        -H "Authorization: Bearer DZs3IeaMP5uEAc2I19kJYl8Tbvsmgq9GaPQPaMjN"