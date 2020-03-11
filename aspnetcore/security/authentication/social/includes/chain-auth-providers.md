## <a name="multiple-authentication-providers"></a>Zprostředkovatelé více ověřování

Když aplikace vyžaduje víc poskytovatelů, nařetězení rozšiřujících metod poskytovatele za [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
