Tato `FetchData` součást ukazuje, jak:

* Zřízení přístupového tokenu
* Pomocí přístupového tokenu v *serverové* aplikaci zavolejte chráněné rozhraní API prostředků.

Tato [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) direktiva označuje Blazor autorizačnímu systému WebAssembly, že uživatel musí mít autorizaci, aby mohl navštívit tuto součást. Přítomnost atributu v *klientské* aplikaci nebrání volání rozhraní API na serveru bez správných přihlašovacích údajů. *Serverová* aplikace musí také použít `[Authorize]` příslušné koncové body k jejich správné ochraně.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>postará o vyžadování přístupového tokenu, který se dá přidat do žádosti o volání rozhraní API. Pokud je token uložen v mezipaměti nebo služba může zřídit nový přístupový token bez zásahu uživatele, požadavek na token je úspěšný. V opačném případě požadavek tokenu selže s objektem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , který je zachycen v příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .

Aby bylo možné získat skutečný token, který se má zahrnout do žádosti, musí aplikace ověřit, jestli žádost proběhla úspěšně, voláním metody [tokenResult. TryGetToken (out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).

Pokud byl požadavek úspěšný, je proměnná tokenu naplněná přístupovým tokenem. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>Vlastnost tokenu zpřístupňuje řetězec literálu, který má být zahrnut v `Authorization` hlavičce požadavku.

Pokud se žádost nezdařila, protože se token nepodařilo zřídit bez zásahu uživatele, výsledek tokenu obsahuje adresu URL pro přesměrování. Když přejdete na tuto adresu URL, uživatel přejde na přihlašovací stránku a vrátí se zpátky na aktuální stránku po úspěšném ověření.

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
