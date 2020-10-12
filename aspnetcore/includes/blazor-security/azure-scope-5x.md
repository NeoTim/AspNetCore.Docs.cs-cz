Při práci s rozhraním API serveru registrovaným v AAD a registrací AAD aplikace se nachází v klientovi, který spoléhá na [neověřenou doménu vydavatele](/azure/active-directory/develop/howto-configure-publisher-domain), identifikátor URI ID aplikace serveru API vaší aplikace není, `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` ale má formát `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` . V takovém případě se výchozí rozsah přístupového tokenu `Program.Main` ( `Program.cs` ) *`Client`* aplikace zobrazí jako následující:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Pokud chcete nakonfigurovat aplikaci API serveru pro odpovídající cílovou skupinu, nastavte `Audience` v *`Server`* souboru nastavení aplikace API ( `appsettings.json` ) tak, aby se shodovala s cílovou skupinou aplikace, kterou poskytuje Azure Portal:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

V předchozí konfiguraci `Audience` nezahrnuje konec hodnoty výchozí **not** obor `/{DEFAULT SCOPE}` .

Příklad:

`Program.Main` ( `Program.cs` ) *`Client`* aplikace:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Nakonfigurujte *`Server`* soubor nastavení aplikace API ( `appsettings.json` ) s vyhovující cílovou skupinou ( `Audience` ):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

V předchozím příkladu `Audience` nezahrnuje konec hodnoty výchozí **not** obor `/API.Access` .
