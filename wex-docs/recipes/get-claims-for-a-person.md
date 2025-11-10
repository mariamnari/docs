---
title: Get Claims for a Person
description: >-
  This recipe will show you how to authenticate, lookup the Person identifier,
  and then get the claims for that person with the identifier you just looked
  up.
hidden: true
recipe:
  color: '#a2ddf8'
  icon: 🦉
---
```csharp C#
using RestSharp;
using Newtonsoft.Json;

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


Console.Out.WriteLine("Lookup the Person GUID with the details we have.");
var personGuid = string.Empty;
using (var client = new RestClient(benefits_platform_client_url))
{
	var personLookupBody = @"{
  ""employer_code"": ""<an employer code>"",
  ""administrator_code"": ""<an admin code>"",
  ""consumer_identifier"": ""<a consumer id>"",
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

Console.Out.WriteLine("Lookup claims for a Person.");
using (var client = new RestClient(benefits_platform_client_url))
{

	var request = new RestRequest($"claims/v1/persons/{personGuid}/claims/", Method.Get);

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

<!-- csharp@4-18 -->

You will need to get a JWT from Auth0 and pass it in the `Authentication` Header to each call to the rest services.

# Lookup Person GUID

<!-- csharp@28-50 -->

The WEX Benefits Platform uses GUIDs to identify resources. If this is the first time you're trying to access a resource and you don't have a GUID to use, you will need to go to the Identity Service to lookup the GUID with the information you have.

# Get Claims with Person GUID

<!-- csharp@59-70 -->

Take the Person GUID you just looked up and use it in the GET REST call to the claims service to get all the claims for that person with paging.