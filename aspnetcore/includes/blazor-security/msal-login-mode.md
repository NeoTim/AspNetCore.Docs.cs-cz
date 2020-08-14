Rozhraní je ve výchozím nastavení automaticky otevíraná okna přihlášení a přejde zpět na přesměrování režimu přihlášení, pokud nelze otevřít automaticky otevírané okno. Nakonfigurujte MSAL pro použití režimu přesměrování přihlášení nastavením `LoginMode` vlastnosti <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> na `redirect` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

Výchozí nastavení je `popup` a hodnota řetězce nerozlišuje velká a malá písmena.
