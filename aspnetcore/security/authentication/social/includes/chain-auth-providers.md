## <a name="multiple-authentication-providers"></a><span data-ttu-id="51e5e-101">Více poskytovatelů ověřování</span><span class="sxs-lookup"><span data-stu-id="51e5e-101">Multiple authentication providers</span></span>

<span data-ttu-id="51e5e-102">Pokud aplikace vyžaduje několik zprostředkovatelů, zřetězení rozšiřující metody zprostředkovatele za [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span><span class="sxs-lookup"><span data-stu-id="51e5e-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
