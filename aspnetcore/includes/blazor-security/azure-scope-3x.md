<span data-ttu-id="67e74-101">Azure Portal poskytuje jeden z následujících formátů identifikátoru URI ID aplikace na základě toho, jestli má tenant AAD [ověřenou nebo neověřenou doménu vydavatele](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="67e74-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="67e74-102">Pokud se v klientské aplikaci použije druhá z předchozích identifikátorů URI ID aplikace, aby se vytvořil identifikátor URI oboru a autorizace se nezdařila, zkuste uvést identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="67e74-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="67e74-103">Příklad:</span><span class="sxs-lookup"><span data-stu-id="67e74-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
