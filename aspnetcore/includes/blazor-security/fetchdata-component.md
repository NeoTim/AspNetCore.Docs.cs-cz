<span data-ttu-id="45620-101">Tato `FetchData` součást ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="45620-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="45620-102">Zřízení přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="45620-102">Provision an access token.</span></span>
* <span data-ttu-id="45620-103">Pomocí přístupového tokenu v *serverové* aplikaci zavolejte chráněné rozhraní API prostředků.</span><span class="sxs-lookup"><span data-stu-id="45620-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="45620-104">Tato `@attribute [Authorize]` direktiva označuje Blazor autorizačnímu systému WebAssembly, že uživatel musí mít autorizaci, aby mohl navštívit tuto součást.</span><span class="sxs-lookup"><span data-stu-id="45620-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="45620-105">Přítomnost atributu v *klientské* aplikaci nebrání volání rozhraní API na serveru bez správných přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="45620-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="45620-106">*Serverová* aplikace musí také použít `[Authorize]` příslušné koncové body k jejich správné ochraně.</span><span class="sxs-lookup"><span data-stu-id="45620-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="45620-107">`IAccessTokenProvider.RequestAccessToken();`postará o vyžadování přístupového tokenu, který se dá přidat do žádosti o volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="45620-107">`IAccessTokenProvider.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="45620-108">Pokud je token uložen v mezipaměti nebo služba může zřídit nový přístupový token bez zásahu uživatele, požadavek na token je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="45620-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="45620-109">V opačném případě požadavek na token selže `AccessTokenNotAvailableException` s chybou, která je zachycena v `try-catch` příkazu.</span><span class="sxs-lookup"><span data-stu-id="45620-109">Otherwise, the token request fails with an `AccessTokenNotAvailableException` error, which is caught in a `try-catch` statement.</span></span>

<span data-ttu-id="45620-110">Aby bylo možné získat skutečný token, který se má zahrnout do žádosti, musí aplikace ověřit, jestli žádost proběhla úspěšně `tokenResult.TryGetToken(out var token)`voláním.</span><span class="sxs-lookup"><span data-stu-id="45620-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="45620-111">Pokud byl požadavek úspěšný, je proměnná tokenu naplněná přístupovým tokenem.</span><span class="sxs-lookup"><span data-stu-id="45620-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="45620-112">`Value` Vlastnost tokenu zpřístupňuje řetězec literálu, který má být zahrnut `Authorization` v hlavičce požadavku.</span><span class="sxs-lookup"><span data-stu-id="45620-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="45620-113">Pokud se žádost nezdařila, protože se token nepodařilo zřídit bez zásahu uživatele, výsledek tokenu obsahuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="45620-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="45620-114">Když přejdete na tuto adresu URL, uživatel přejde na přihlašovací stránku a vrátí se zpátky na aktuální stránku po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="45620-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
