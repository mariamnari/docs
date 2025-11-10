---
title: Get Messages
description: >-
  This will show you how to authenticate, lookup a person, and get all
  communications for a person.
hidden: false
recipe:
  color: '#ffbf3f'
  icon: 🦉
---
```csharp C#
/*
1. Authenticate

You will need to get a JWT from Auth0 and pass it in the Authentication Header to each call to the rest services.
*/
var auth_client_url = "https://hl-mbp-uat.wexdev.auth0app.com/oauth/";
var benefits_platform_client_url = "https://benefits-platform.uat.wexglobal.com";
var clientId = "<your client id>";
var clientSecret = "<your client secret>"; 

Console.Out.WriteLine("Get a Token from Auth0");
var token = string.Empty;
using (var client = new RestClient(auth_client_url))
{
	var request = new RestRequest("token", Method.Post);
	request.AddHeader("content-type", "application/json");
	request.AddParameter("application/json", $"{{\"client_id\":\"{clientId}\",\"client_secret\":\"{clientSecret}\",\"audience\":\"https://benefits-platform.wexglobal.com\",\"grant_type\":\"client_credentials\"}}", ParameterType.RequestBody);
	var response = client.Execute(request);
	token = (String)((Newtonsoft.Json.Linq.JObject)JsonConvert.DeserializeObject(response.Content))["access_token"];
}

if (string.IsNullOrWhiteSpace(token))
{
	Console.Out.WriteLine("Something went wrong with authentication and you didn't get a token.");
	return;
}

/*
2. Lookup Person GUID
The WEX Benefits Platform uses GUIDs to identify resources. If this is the first time you're trying to access a resource and you don't have a GUID to use, you will need to go to the Identity Service to lookup the GUID with the information you have.
*/
Console.Out.WriteLine("Lookup the Person GUID with the details we have.");
var personGuid = string.Empty;
using (var client = new RestClient(benefits_platform_client_url))
{
	var personLookupBody = @"{
  ""employer_code"": ""CDEF"",
  ""administrator_code"": ""QTP"",
  ""consumer_identifier"": ""2290388"",
  ""consumer_identifier_type"": ""EmployeeNumber"",
  ""type"": ""Consumer""
}";

	var request = new RestRequest("/identity/v1/persons/lookup", Method.Post);

	// add the authentication token to the headers
	request.AddHeader("Authorization", $"Bearer {token}");
	request.AddHeader("content-type", "application/json");
	request.AddParameter("application/json", personLookupBody, ParameterType.RequestBody);

	var response = client.Execute(request);

	personGuid = (((Newtonsoft.Json.Linq.JObject)JsonConvert.DeserializeObject(response.Content))["items"][0])["id"].ToString();
	Console.Out.WriteLine($"Person GUID [{personGuid}]");
}

if (string.IsNullOrWhiteSpace(personGuid))
{
	Console.Out.WriteLine("Person not found.");
	return;
}

/*
3. Get Messages with Person GUID
Take the Person GUID you just looked up and use it in the GET REST call to the communications service to get all the messages for that person with paging.
*/
Console.Out.WriteLine("Lookup messages for a Person.");
using (var client = new RestClient(benefits_platform_client_url))
{

	var request = new RestRequest($"communications/v1/persons/{personGuid}/messages/", Method.Get);

	// add the authentication token to the headers
	request.AddHeader("Authorization", $"Bearer {token}");

	var response = client.Execute(request);

	Console.Out.WriteLine(((Newtonsoft.Json.Linq.JObject)JsonConvert.DeserializeObject(response.Content)));
}
```

```json Response Example
{"success":true}
```

# Authenticate

<!-- csharp@6-26 -->

You will need to get a JWT from Auth0 and pass it in the Authentication Header to each call to the rest services.

# Lookup Person

<!-- csharp@33-55 -->

The WEX Benefits Platform uses GUIDs to identify resources. If this is the first time you're trying to access a resource and you don't have a GUID to use, you will need to go to the Identity Service to lookup the GUID with the information you have.

# Get Messages with Person GUID

<!-- csharp@68-79 -->

Take the Person GUID you just looked up and use it in the GET REST call to the claims service to get all the claims for that person with paging.