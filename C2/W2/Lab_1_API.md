 The Spotify Web API is a RESTful API to which you can send requests to access music artists, albums and tracks directly from the Spotify data catalog. 
 
 Each specific item of data, such as a playlist, artist, or album is called a **resource** that you can access by sending an HTTP request to the endpoint representing that resource. 
 
 There are different types of HTTP requests, but the common ones are 
 - GET, 
 - PUT, 
 - POST, and 
 - DELETE. 
 
 GET allows you to retrieve a resource, POST allows you to create a resource, PUT allows you to change and or replace resources, and DELETE allows you to delete resources. 
 
 For example, in the API documentation of Spotify, if you click under Albums, you can see all the requests that you can use to interact with this resource.

For instance, you can perform a GET request to get Spotify catalog information about an album's tracks using this endpoint. You can also use a GET request to retrieve a list of new album releases featured in Spotify using this endpoint. If the request is successful, it returns a response in a JSON format containing information about the requested resource. For example, here's a response sample to a GET album tracks request, and here's another response sample to a GET new releases request. If the request is not successful, it returns an error object containing a status code that explains why the request was not successful. For example, a code of 400 means a bad request that could be due to malformed syntax, and a code of 404 means the requested resource could not be found. You can find in the documentation here a list of status codes and the meaning of each. 

When you make a request to Spotify Web API, you need to specify the endpoint to the resource as well as an access token. The access token is a string that contains the credentials and permissions that you use to access a given resource. To get the access token, you will need to create a Spotify account first, and from your account you will be able to get a client ID and a client secret which can be used in the authorization process and the generation of the access token. The access token is valid for one hour. After that time, the token expires and you need to request a new one. In the lab, you'll be provided with a code that you can use to request an access token using your client ID and the client secret. To learn more about the authorization process with Spotify, you can check out the documentation.


---

# Batch Data Processing from an API

In this lab: 
- you will learn how to interact with the Spotify API and extract data from the API **in a batch way**. 
- You will explore what **pagination** means 
- and how to **send an API request that requires authorization**.


## Prerequisites

#### 1 - Create a Sotify APP
#### 2 - Understand the Basics of APIs
Several packages in Python allow you to request data from an API; in this lab, you will use the `requests` package, which is a popular and versatile library to perform HTTP requests. It provides a simple and easy-to-use way to interact with web services and APIs. Let's load the required packages:

```Python
import os
from typing import Dict, Any, Callable

from dotenv import load_dotenv
import json
import requests 
```


**2.1 - Get Token**
The first step when working with an API is to **understand the authentication process.** For that, the Spotify APP generates a **Client ID** and a **Client secret** that you will use to generate an "**access token**". 

> 	*The access token is a string that contains the credentials and permissions that you can use to access a given resource. You can find more about it in the [API documentation](https://developer.spotify.com/documentation/web-api/concepts/access-token).* 

Since each API is developed with a particular purpose, it is necessary for you to always read and understand the nuances of each API so you can access the data responsibly. Throughout this lab, you will be provided with several links to the documentation and you are encouraged to read them. (During the lab session, you may quickly skim through the links, but you can always check them in more details after the lab session).

The `get_token` function below takes a Client ID, Client secret and a URL as input, and performs a POST request to that URL to obtain an access token using the client credentials. Run the following cell to get the access token.

```Python
def get_token(client_id: str, client_secret: str, url: str) -> Dict[Any, Any]:
    """Allows to perform a POST request to obtain an access token 

    Args:
        client_id (str): App client id
        client_secret (str): App client secret
        url (str): URL to perform the post request

    Returns:
        Dict[Any, Any]: Dictionary containing the access token
    """
        
    headers = {        
        "Content-Type": "application/x-www-form-urlencoded"            
    }
    
    payload = {
                "grant_type": "client_credentials", 
                "client_id": client_id, 
                "client_secret": client_secret
               }
    
    try: 
        response = requests.post(url=url, headers=headers, data=payload)
        print(type(response))
        response.raise_for_status()
        response_json = json.loads(response.content)
        
        return response_json
        
    except Exception as err:
        print(f"Error: {err}")
        return {}

URL_TOKEN="https://accounts.spotify.com/api/token"
token = get_token(client_id=CLIENT_ID, client_secret=CLIENT_SECRET, url=URL_TOKEN)

print(token)
```

You can see that you are provided with a temporary access token. The `expires_in` field tells you the duration of this token in seconds. When this token expires, your requests will fail and an error object will be returned to you holding a status code of 401. This status code means that the request is unauthorized.

Whenever you send an API request to the spotify API, you need to include in the request the access token, as an authorization header following a certain format. You are provided with the function `get_auth_header`. This function expects the access token and returns the authorization header that can be included in the API request. 

Make sure to run the following cell to declare the function `get_auth_header`, which you will use throughout this lab.

```Python
def get_auth_header(access_token: str) -> Dict[str, str]:
    return {"Authorization": f"Bearer {access_token}"}
```


#### 3 - Pagination

Although there is a total of 100 available items to be returned, only 20 were returned. This is established by the `limit` parameter and those were the 20 items you just counted before. This limit on the number of elements returned is a common feature of several APIs and although in some cases you can modify such a limit, a good practice is to use it with **pagination** to get all the elements that can be returned. 

Each API handles pagination differently. 

For Spotify, the requests response provides you with two fields that allow you to query the different pages of your request: `previous` and `next`. These two fields will return the URL to the previous or next page respectively and they are based on the `offset` and `limit` parameters. In this case, there are two ways for you to explore the rest of the data:

- you can use the value from the next parameter to get the direct URL for the next page of requests, or 
- you can build the URL for the next page from scratch using the offset and limit parameters (make sure to update the offset parameter for the request). 

For the sake of learning, you will use method 2 to build the URL yourself. Then you will also compare it with the result from using the first method just to check that you created the URL correctly.

Before creating a function that will allow you to paginate, let's try to do it manually. If you compare the URLs provided by the `href` and `next` fields, you can see that while the `limit` parameter remains the same, the `offset` parameter has increased with the same value as the one stored in `limit`.

```json
{
...,
'href': 'https://api.spotify.com/v1/browse/new-releases?offset=0&limit=20',
...,
'next': 'https://api.spotify.com/v1/browse/new-releases?offset=20&limit=20',
...
}
```

