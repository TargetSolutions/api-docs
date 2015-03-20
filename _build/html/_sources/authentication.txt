Authentication
==============

Our API uses OAuth 2.0 for authentication, specifically the *client credentials* grant type.  
The authentication process consists of requesting an access token with your organization's 
*API key* and *API secret*, and using this access token in the ``Authentication`` HTTP header 
of any subsequent requests.

Receiving an access token
-------------------------

You use access tokens to authorize any requests made towards our API. To request an access token,
issue a POST request to ``https://callbackstaffing.com/api/oauth/access_token`` like so:

curl
^^^^
::

   curl -v https://callbackstaffing.com/api/oauth/access_token \
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

   curl -v https://callbackstaffing.com/api/v1/schedule \
        -H "Authorization: Bearer DZs3IeaMP5uEAc2I19kJYl8Tbvsmgq9GaPQPaMjN"