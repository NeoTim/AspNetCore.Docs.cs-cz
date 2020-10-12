<span data-ttu-id="82689-101">Při práci s rozhraním API serveru registrovaným v AAD a registrací AAD aplikace se nachází v klientovi, který spoléhá na [neověřenou doménu vydavatele](/azure/active-directory/develop/howto-configure-publisher-domain), identifikátor URI ID aplikace serveru API vaší aplikace není, `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` ale má formát `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` .</span><span class="sxs-lookup"><span data-stu-id="82689-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="82689-102">V takovém případě se výchozí rozsah přístupového tokenu `Program.Main` ( `Program.cs` ) *`Client`* aplikace zobrazí jako následující:</span><span class="sxs-lookup"><span data-stu-id="82689-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="82689-103">Pokud chcete nakonfigurovat aplikaci API serveru pro odpovídající cílovou skupinu, nastavte `Audience` v *`Server`* souboru nastavení aplikace API ( `appsettings.json` ) tak, aby se shodovala s cílovou skupinou aplikace, kterou poskytuje Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="82689-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

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

<span data-ttu-id="82689-104">V předchozí konfiguraci `Audience` nezahrnuje konec hodnoty výchozí **not** obor `/{DEFAULT SCOPE}` .</span><span class="sxs-lookup"><span data-stu-id="82689-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="82689-105">Příklad:</span><span class="sxs-lookup"><span data-stu-id="82689-105">Example:</span></span>

<span data-ttu-id="82689-106">`Program.Main` ( `Program.cs` ) *`Client`* aplikace:</span><span class="sxs-lookup"><span data-stu-id="82689-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="82689-107">Nakonfigurujte *`Server`* soubor nastavení aplikace API ( `appsettings.json` ) s vyhovující cílovou skupinou ( `Audience` ):</span><span class="sxs-lookup"><span data-stu-id="82689-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

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

<span data-ttu-id="82689-108">V předchozím příkladu `Audience` nezahrnuje konec hodnoty výchozí **not** obor `/API.Access` .</span><span class="sxs-lookup"><span data-stu-id="82689-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
