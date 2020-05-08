## <a name="multiple-authentication-providers"></a><span data-ttu-id="596e1-101">Zprostředkovatelé více ověřování</span><span class="sxs-lookup"><span data-stu-id="596e1-101">Multiple authentication providers</span></span>

<span data-ttu-id="596e1-102">Když aplikace vyžaduje víc poskytovatelů, nařetězení rozšiřujících metod poskytovatele za [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span><span class="sxs-lookup"><span data-stu-id="596e1-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
