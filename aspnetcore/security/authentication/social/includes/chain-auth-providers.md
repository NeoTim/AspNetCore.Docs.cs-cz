## <a name="multiple-authentication-providers"></a>Více poskytovatelů ověřování

Pokud aplikace vyžaduje několik zprostředkovatelů, zřetězení rozšiřující metody zprostředkovatele za [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
