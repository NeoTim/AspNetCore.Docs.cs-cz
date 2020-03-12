Komponenta `FetchData` ukazuje, jak:

* Zřízení přístupového tokenu
* Pomocí přístupového tokenu v *serverové* aplikaci zavolejte chráněné rozhraní API prostředků.

Direktiva `@attribute [Authorize]` označuje systém autorizace Blazor WebAssembly, který musí být uživatel autorizovaný, aby bylo možné navštívit tuto součást. Přítomnost atributu v *klientské* aplikaci nebrání volání rozhraní API na serveru bez správných přihlašovacích údajů. *Serverová* aplikace musí také použít `[Authorize]` na příslušných koncových bodech k jejich správné ochraně.

`AuthenticationService.RequestAccessToken();` se postará o vyžadování přístupového tokenu, který se dá přidat do žádosti o volání rozhraní API. Pokud je token uložen v mezipaměti nebo služba může zřídit nový přístupový token bez zásahu uživatele, požadavek na token je úspěšný. V opačném případě se požadavek na token nezdařil.

Aby bylo možné získat skutečný token, který se má zahrnout do žádosti, musí aplikace ověřit, jestli žádost proběhla úspěšně, voláním `tokenResult.TryGetToken(out var token)`. 

Pokud byl požadavek úspěšný, je proměnná tokenu naplněná přístupovým tokenem. Vlastnost `Value` tokenu zpřístupňuje řetězec literálu, který se má zahrnout do hlavičky žádosti `Authorization`.

Pokud se žádost nezdařila, protože se token nepodařilo zřídit bez zásahu uživatele, výsledek tokenu obsahuje adresu URL pro přesměrování. Když přejdete na tuto adresu URL, uživatel přejde na přihlašovací stránku a vrátí se zpátky na aktuální stránku po úspěšném ověření.

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

Další informace najdete v tématu [uložení stavu aplikace před operací ověřování](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
