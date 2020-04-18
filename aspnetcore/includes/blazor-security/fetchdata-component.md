Komponenta `FetchData` ukazuje, jak:

* Zřízení přístupového tokenu.
* Pomocí přístupového tokenu můžete volat rozhraní API chráněného prostředku v aplikaci *Server.*

Směrnice `@attribute [Authorize]` označuje systému autorizace Blazor WebAssembly, že uživatel musí být oprávněn k návštěvě této součásti. Přítomnost atributu v *klientské* aplikaci nezabrání volání rozhraní API na serveru bez správných přihlašovacích údajů. Server *Server* ová aplikace `[Authorize]` také musí použít na příslušné koncové body správně chránit.

`AuthenticationService.RequestAccessToken();`postará o vyžádání přístupového tokenu, který lze přidat do požadavku na volání rozhraní API. Pokud je token uložen do mezipaměti nebo služba je schopna zřídit nový přístupový token bez interakce s uživatelem, požadavek na token uspěje. V opačném případě se nezdaří požadavek tokenu.

Chcete-li získat skutečný token zahrnout do požadavku, aplikace musí zkontrolovat, `tokenResult.TryGetToken(out var token)`zda požadavek proběhl úspěšně voláním . 

Pokud byl požadavek úspěšný, proměnná tokenu je naplněna přístupovým tokenem. Vlastnost `Value` tokenu zpřístupňuje literál řetězec `Authorization` zahrnout do hlavičky požadavku.

Pokud se požadavek nezdařil, protože token nelze zřídit bez zásahu uživatele, výsledek tokenu obsahuje adresu URL přesměrování. Přechod na tuto adresu URL přenese uživatele na přihlašovací stránku a po úspěšném ověření se vrátí na aktuální stránku.

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

Další informace najdete [v tématu Uložení stavu aplikace před ověřovací operací](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
