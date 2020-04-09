## <a name="multiple-authentication-providers"></a><span data-ttu-id="17d64-101">Více poskytovatelů ověřování</span><span class="sxs-lookup"><span data-stu-id="17d64-101">Multiple authentication providers</span></span>

<span data-ttu-id="17d64-102">Když aplikace vyžaduje více poskytovatelů, řetěz metody rozšíření zprostředkovatele za [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span><span class="sxs-lookup"><span data-stu-id="17d64-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
