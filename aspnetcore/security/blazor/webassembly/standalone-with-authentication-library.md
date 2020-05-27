---
title: ' zabezpečení Blazor samostatné aplikace ASP.NET Core WebAssembly s knihovnou ověřování ' Autor: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí knihovny ověřování

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

*V případě Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Podívejte se na témata AAD a AAD B2C v tomto uzlu obsah.*

Chcete-li vytvořit Blazor samostatnou aplikaci WebAssembly, která používá knihovnu [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Library, spusťte v příkazovém prostředí následující příkaz:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ). Název složky se také stal součástí názvu projektu.

V aplikaci Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started). Nastavte **ověřování** na **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty do aplikace** .

## <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty, aplikace automaticky obdrží odkaz na balíček pro balíček [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) v souboru projektu aplikace. Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metodou rozšíření poskytovanou balíčkem [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

Podpora ověřování pro samostatné aplikace se nabízí pomocí Open ID Connect (OIDC). <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace pomocí OIDC. Hodnoty požadované pro konfiguraci aplikace lze získat z IP adresy kompatibilní s OIDC. Získejte hodnoty při registraci aplikace, ke kterým obvykle dochází na online portálu.

## <a name="access-token-scopes"></a>Obory přístupového tokenu

BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API. Pro zřízení přístupového tokenu v rámci procesu přihlašování přidejte obor do výchozího oboru tokenu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Další informace najdete v následujících částech článku o *dalších scénářích* :

* [Vyžádání dalších přístupových tokenů](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Připojit tokeny k odchozím žádostem](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Importovat soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Komponenta LoginDisplay

`LoginDisplay`Součást (*Shared/LoginDisplay. Razor*) je vykreslena ve `MainLayout` komponentě (*Shared/MainLayout. Razor*) a spravuje následující chování:

* Pro ověřené uživatele:
  * Zobrazí aktuální uživatelské jméno.
  * Nabízí tlačítko pro odhlášení od aplikace.
* Pro anonymní uživatele nabízí možnost přihlásit se.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a>Součást ověřování

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/webassembly/additional-scenarios>
* [Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
