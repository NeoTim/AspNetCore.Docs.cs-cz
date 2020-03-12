<span data-ttu-id="83525-101">Komponenta `FetchData` ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="83525-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="83525-102">Zřízení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="83525-102">Provision an access token.</span></span>
* <span data-ttu-id="83525-103">Pomocí přístupového tokenu v *serverové* aplikaci zavolejte chráněné rozhraní API prostředků.</span><span class="sxs-lookup"><span data-stu-id="83525-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="83525-104">Direktiva `@attribute [Authorize]` označuje systém autorizace Blazor WebAssembly, který musí být uživatel autorizovaný, aby bylo možné navštívit tuto součást.</span><span class="sxs-lookup"><span data-stu-id="83525-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="83525-105">Přítomnost atributu v *klientské* aplikaci nebrání volání rozhraní API na serveru bez správných přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="83525-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="83525-106">*Serverová* aplikace musí také použít `[Authorize]` na příslušných koncových bodech k jejich správné ochraně.</span><span class="sxs-lookup"><span data-stu-id="83525-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="83525-107">`AuthenticationService.RequestAccessToken();` se postará o vyžadování přístupového tokenu, který se dá přidat do žádosti o volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="83525-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="83525-108">Pokud je token uložen v mezipaměti nebo služba může zřídit nový přístupový token bez zásahu uživatele, požadavek na token je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="83525-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="83525-109">V opačném případě se požadavek na token nezdařil.</span><span class="sxs-lookup"><span data-stu-id="83525-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="83525-110">Aby bylo možné získat skutečný token, který se má zahrnout do žádosti, musí aplikace ověřit, jestli žádost proběhla úspěšně, voláním `tokenResult.TryGetToken(out var token)`.</span><span class="sxs-lookup"><span data-stu-id="83525-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="83525-111">Pokud byl požadavek úspěšný, je proměnná tokenu naplněná přístupovým tokenem.</span><span class="sxs-lookup"><span data-stu-id="83525-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="83525-112">Vlastnost `Value` tokenu zpřístupňuje řetězec literálu, který se má zahrnout do hlavičky žádosti `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="83525-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="83525-113">Pokud se žádost nezdařila, protože se token nepodařilo zřídit bez zásahu uživatele, výsledek tokenu obsahuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="83525-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="83525-114">Když přejdete na tuto adresu URL, uživatel přejde na přihlašovací stránku a vrátí se zpátky na aktuální stránku po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="83525-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
...
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="83525-115">Další informace najdete v tématu [uložení stavu aplikace před operací ověřování](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="83525-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
