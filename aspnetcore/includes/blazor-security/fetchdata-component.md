<span data-ttu-id="47be2-101">Komponenta `FetchData` ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="47be2-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="47be2-102">Zřízení přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="47be2-102">Provision an access token.</span></span>
* <span data-ttu-id="47be2-103">Pomocí přístupového tokenu můžete volat rozhraní API chráněného prostředku v aplikaci *Server.*</span><span class="sxs-lookup"><span data-stu-id="47be2-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="47be2-104">Směrnice `@attribute [Authorize]` označuje systému autorizace Blazor WebAssembly, že uživatel musí být oprávněn k návštěvě této součásti.</span><span class="sxs-lookup"><span data-stu-id="47be2-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="47be2-105">Přítomnost atributu v *klientské* aplikaci nezabrání volání rozhraní API na serveru bez správných přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="47be2-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="47be2-106">Server *Server* ová aplikace `[Authorize]` také musí použít na příslušné koncové body správně chránit.</span><span class="sxs-lookup"><span data-stu-id="47be2-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="47be2-107">`AuthenticationService.RequestAccessToken();`postará o vyžádání přístupového tokenu, který lze přidat do požadavku na volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="47be2-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="47be2-108">Pokud je token uložen do mezipaměti nebo služba je schopna zřídit nový přístupový token bez interakce s uživatelem, požadavek na token uspěje.</span><span class="sxs-lookup"><span data-stu-id="47be2-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="47be2-109">V opačném případě se nezdaří požadavek tokenu.</span><span class="sxs-lookup"><span data-stu-id="47be2-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="47be2-110">Chcete-li získat skutečný token zahrnout do požadavku, aplikace musí zkontrolovat, `tokenResult.TryGetToken(out var token)`zda požadavek proběhl úspěšně voláním .</span><span class="sxs-lookup"><span data-stu-id="47be2-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="47be2-111">Pokud byl požadavek úspěšný, proměnná tokenu je naplněna přístupovým tokenem.</span><span class="sxs-lookup"><span data-stu-id="47be2-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="47be2-112">Vlastnost `Value` tokenu zpřístupňuje literál řetězec `Authorization` zahrnout do hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="47be2-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="47be2-113">Pokud se požadavek nezdařil, protože token nelze zřídit bez zásahu uživatele, výsledek tokenu obsahuje adresu URL přesměrování.</span><span class="sxs-lookup"><span data-stu-id="47be2-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="47be2-114">Přechod na tuto adresu URL přenese uživatele na přihlašovací stránku a po úspěšném ověření se vrátí na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="47be2-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
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
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="47be2-115">Další informace najdete [v tématu Uložení stavu aplikace před ověřovací operací](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="47be2-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
