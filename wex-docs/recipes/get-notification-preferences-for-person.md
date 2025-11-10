---
title: Get Notification Preferences for Person
description: Recipe Description
hidden: false
recipe:
  color: '#018FF4'
  icon: 🦉
---
```csharp C#
<Query Kind="Statements">
  <NuGetReference>Newtonsoft.Json</NuGetReference>
  <NuGetReference>RestSharp</NuGetReference>
  <Namespace>RestSharp</Namespace>
  <Namespace>Newtonsoft.Json</Namespace>
</Query>

/*
1. Authenticate

You will need to get a JWT from Auth0 and pass it in the Authentication Header to each call to the rest services.
*/
var auth_client_url = "https://hl-mbp-uat.wexdev.auth0app.com/oauth/";
var benefits_platform_client_url = "https://benefits-platform.uat.wexglobal.com";
var clientId = "<Your Auth0 Client ID>";
var clientSecret = "<Your Auth0 Client Secret>";

Console.Out.WriteLine("Get a Token from Auth0");
var token = string.Empty;
using (var client = new RestClient(auth_client_url))
{
	var request = new RestRequest("token", Method.Post);
	request.AddHeader("content-type", "application/json");
	request.AddParameter("application/json", $"{{\"client_id\":\"{clientId}\",\"client_secret\":\"{clientSecret}\",\"audience\":\"https://mbp.com\",\"grant_type\":\"client_credentials\"}}", ParameterType.RequestBody);
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
  ""administrator_code"": ""<Admin 3 Letter Code"",
  ""consumer_identifier"": ""<Consumer id>"",
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
3. Get Notification Preferences with Person GUID
Take the Person GUID you just looked up and use it in the GET REST call to the communications service to get the person's notification preferences.
*/
Console.Out.WriteLine("Lookup Notification preferences for a Person.");
using (var client = new RestClient(benefits_platform_client_url))
{

	var request = new RestRequest($"communications/v1/persons/{personGuid}/notification-preferences/", Method.Get);

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

<!-- csharp@13-33 -->

You will need to get a JWT from Auth0 and pass it in the Authentication Header to each call to the rest services.

Thanks again for reading the tutorial :) 

# Lookup Person GUID

<!-- csharp@39-68 -->

The WEX Benefits Platform uses GUIDs to identify resources. If this is the first time you're trying to access a resource and you don't have a GUID to use, you will need to go to the Identity Service to lookup the GUID with the information you have.

# Get Notification Preferences with Person GUID

<!-- csharp@74-86 -->

Take the Person GUID you just looked up and use it in the GET REST call to the communications service to get the person's notification preferences.
