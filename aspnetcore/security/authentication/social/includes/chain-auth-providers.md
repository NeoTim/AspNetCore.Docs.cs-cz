## <a name="multiple-authentication-providers"></a>Více poskytovatelů ověřování

Když aplikace vyžaduje více poskytovatelů, řetěz metody rozšíření zprostředkovatele za [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
