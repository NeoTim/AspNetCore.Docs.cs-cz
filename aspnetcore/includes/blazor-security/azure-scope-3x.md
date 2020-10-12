Azure Portal poskytuje jeden z následujících formátů identifikátoru URI ID aplikace na základě toho, jestli má tenant AAD [ověřenou nebo neověřenou doménu vydavatele](/azure/active-directory/develop/howto-configure-publisher-domain):

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Pokud se v klientské aplikaci použije druhá z předchozích identifikátorů URI ID aplikace, aby se vytvořil identifikátor URI oboru a autorizace se nezdařila, zkuste uvést identifikátor URI oboru bez schématu a hostitele:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Příklad:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
