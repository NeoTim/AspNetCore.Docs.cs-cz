<span data-ttu-id="2f306-101">Pokud aplikace vyžaduje několik poskytovatelů, řetězu rozšiřující metody zprostředkovatele za [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span><span class="sxs-lookup"><span data-stu-id="2f306-101">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
