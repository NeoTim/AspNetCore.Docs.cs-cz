---
title: Zabezpečení hostované Blazor WebAssembly aplikace v ASP.NET Core se Identity serverem
author: guardrex
description: Vytvoření nového hostovaného Blazor řešení s ověřováním v rámci sady Visual Studio, které používá [ Identity Server](https://identityserver.io/) back-end
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 33a82885dee5e13a97c76e6ecef96731a0336fad
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504681"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core se Identity serverem

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

Tento článek vysvětluje, jak vytvořit [hostovanou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která používá [ Identity Server](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API.

> [!NOTE]
> Pokud chcete samostatnou nebo hostovanou Blazor WebAssembly aplikaci nakonfigurovat tak, aby používala existující externí Identity instanci serveru, postupujte podle pokynů v části <xref:blazor/security/webassembly/standalone-with-authentication-library> .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:

1. Po výběru šablony ** Blazor WebAssembly aplikace** v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .

1. Vyberte **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty v aplikaci** pro ukládání uživatelů v aplikaci pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core.

1. Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

Pokud chcete vytvořit nový Blazor WebAssembly projekt s mechanismem ověřování v prázdné složce, zadejte `Individual` ověřovací mechanismus s `-au|--auth` možností ukládat uživatele v rámci aplikace pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core:

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Zástupný symbol  | Příklad        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.

Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:

1. V kroku **Konfigurace nového Blazor WebAssembly aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .

1. Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core [Identity](xref:security/authentication/identity) .

1. Zaškrtněte políčko **ASP.NET Core hostované** .

---

## <a name="server-app-configuration"></a>Konfigurace aplikace serveru

Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.

### <a name="startup-class"></a>Spouštěcí třída

`Startup`Třída obsahuje následující doplňky.

* V `Startup.ConfigureServices`:

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer s další <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnou metodou, která nastaví výchozí konvence ASP.NET Core na Identity serveru:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování s další <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnou metodou, která nakonfiguruje aplikaci k ověření tokenů JWT vyprodukovaných Identity serverem:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* V `Startup.Configure`:

  * IdentityMiddleware serveru zpřístupňuje koncové body OpenID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * Middleware ověřování zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:

    ```csharp
    app.UseAuthentication();
    ```

  * Middleware autorizace povoluje možnosti autorizace:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Pomocná metoda konfiguruje [ Identity Server](https://identityserver.io/) pro ASP.NET Core scénáře. IdentityServer je výkonné a rozšiřitelné rozhraní pro zpracování otázek zabezpečení aplikací. IdentityServer zveřejňuje nepotřebnou složitost pro nejběžnější scénáře. V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod. Jakmile se vaše potřeby ověřování změní, Identity je k dispozici plná síla serveru pro přizpůsobení ověřování podle požadavků aplikace.

### <a name="addno-locidentityserverjwt"></a>Přidat Identity ServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování. Zásady jsou nakonfigurovány tak, aby umožňovaly Identity zpracování všech požadavků směrovaných na jakoukoli dílčí cestu v Identity prostoru URL `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Zpracovává všechny ostatní požadavky. Tato metoda navíc:

* Zaregistruje `{APPLICATION NAME}API` prostředek rozhraní API se Identity serverem s výchozím oborem `{APPLICATION NAME}API` .
* Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných Identity serverem pro aplikaci.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

V rozhraní `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) je atribut použit pro třídu. Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku. Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené nástrojem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Pomocná metoda se konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužná rutina pro požadavky na aplikaci.

### <a name="applicationdbcontext"></a>ApplicationDbContext

V rozhraní `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) se <xref:Microsoft.EntityFrameworkCore.DbContext> rozšiřuje <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> na zahrnutí schématu pro Identity Server. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity <xref:Microsoft.EntityFrameworkCore.DbContext> tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodě.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

V rozhraní `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) se koncový bod klienta zřídí pro poskytování OIDC parametrů.

### <a name="app-settings"></a>Nastavení aplikace

V souboru nastavení aplikace ( `appsettings.json` ) v kořenovém adresáři projektu `IdentityServer` obsahuje část seznam konfigurovaných klientů. V následujícím příkladu je jeden klient. Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth. Profil indikuje typ aplikace, která se konfiguruje. Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>Konfigurace klientské aplikace

### <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty ( `Individual` ), aplikace automaticky obdrží odkaz na balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) v souboru projektu aplikace. Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

### <a name="httpclient-configuration"></a>`HttpClient` rozšířeného

V `Program.Main` ( `Program.cs` ) <xref:System.Net.Http.HttpClient> je pojmenovaný ( `HostIS.ServerAPI` ) nakonfigurovaný tak, aby poskytoval <xref:System.Net.Http.HttpClient> instance, které zahrnují přístupové tokeny při vytváření žádostí na rozhraní API serveru:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Pokud konfigurujete Blazor WebAssembly aplikaci tak, aby používala stávající Identity instanci serveru, která není součástí hostovaného Blazor řešení, změňte <xref:System.Net.Http.HttpClient> registraci základní adresy z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adresu URL koncového bodu rozhraní API serverové aplikace.

### <a name="api-authorization-support"></a>Podpora autorizace rozhraní API

Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v rámci [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) balíčku. Tato metoda nastaví služby, které aplikace požaduje, aby spolupracovaly se stávajícím autorizačním systémem.

```csharp
builder.Services.AddApiAuthorization();
```

Ve výchozím nastavení je konfigurace pro aplikaci načtena podle konvence z `_configuration/{client-id}` . Podle konvence je ID klienta nastaveno na název sestavení aplikace. Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.

### <a name="imports-file"></a>Importovat soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Komponenta LoginDisplay

`LoginDisplay`Součást ( `Shared/LoginDisplay.razor` ) je vykreslena v `MainLayout` komponentě ( `Shared/MainLayout.razor` ) a spravuje následující chování:

* Pro ověřené uživatele:
  * Zobrazí aktuální uživatelské jméno.
  * Nabízí odkaz na stránku profil uživatele v ASP.NET Core Identity .
  * Nabízí tlačítko pro odhlášení od aplikace.
* Pro anonymní uživatele:
  * Nabízí možnost registrace.
  * Nabízí možnost přihlásit se.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a>Součást ověřování

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Komponenta FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci z projektu serveru. Při použití sady Visual Studio buď:

* Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .
* Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .

## <a name="name-and-role-claim-with-api-authorization"></a>Deklarace identity u názvů a rolí pomocí ověřování API

### <a name="custom-user-factory"></a>Vlastní továrna uživatelů

V klientské aplikaci vytvořte vlastní objekt pro vytváření uživatelů. Identity Server odesílá v jedné deklaraci více rolí jako pole JSON `role` . Jedna role se pošle jako řetězcová hodnota v deklaraci identity. Továrna vytvoří jednotlivou `role` deklaraci identity pro každou roli uživatele.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

V klientské aplikaci Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

V serverové aplikaci zavolejte <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> na Identity Tvůrce, který přidá služby související s rolemi:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>Konfigurovat Identity Server

Použijte **jeden** z následujících přístupů:

* [Možnosti autorizace rozhraní API](#api-authorization-options)
* [Profilová služba](#profile-service)

#### <a name="api-authorization-options"></a>Možnosti autorizace rozhraní API

V serverové aplikaci:

* Nakonfigurujte Identity Server tak, aby `name` uvedl `role` deklarace identity a do tokenu ID a přístupového tokenu.
* Zabrání výchozímu mapování rolí v obslužné rutině tokenu JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Profilová služba

V serverové aplikaci vytvořte `ProfileService` implementaci.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

V serverové aplikaci Zaregistrujte službu profilů v nástroji `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Použití autorizačních mechanismů

V klientské aplikaci jsou v tuto chvíli funkční přístupy k komponentám autorizace. Kterýkoli z autorizačních mechanismů v součástech může použít roli k autorizaci uživatele:

* [ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )
* [Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )

  Podporuje se vícenásobné testy rolí:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name` se naplní v klientské aplikaci pomocí uživatelského jména uživatele, což je obvykle jejich přihlašovací e-mailová adresa.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* [Nasazení do Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Import certifikátu z Key Vault (dokumentace k Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
