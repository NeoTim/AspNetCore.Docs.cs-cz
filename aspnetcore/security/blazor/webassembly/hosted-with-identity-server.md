---
title: Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly se Identity serverem
author: guardrex
description: Vytvoření nové Blazor hostované aplikace s ověřováním v sadě Visual Studio, které používá back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ade2d88c6a2d59e169c9019e871982a74ae46b33
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452314"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly se Identity serverem

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

Tento článek vysvětluje, jak vytvořit novou Blazor hostovanou aplikaci, která používá [IdentityServer](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V aplikaci Visual Studio:

1. Vytvoří novou aplikaci ** Blazor WebAssembly** . Další informace naleznete v tématu <xref:blazor/get-started>.
1. V dialogovém okně **vytvořit novou Blazor aplikaci** vyberte v části **ověřování** možnost **změnit** .
1. Vyberte **jednotlivé uživatelské účty** a potom klikněte na **OK**.
1. Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .
1. Vyberte tlačítko **Vytvořit**.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Pokud chcete vytvořit aplikaci v příkazovém prostředí, spusťte následující příkaz:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ). Název složky se také stal součástí názvu projektu.

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

  * IdentityServer s další <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnou metodou, která nastaví výchozí konvence ASP.NET Core nad IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování s další <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnou metodou, která nakonfiguruje aplikaci pro ověření tokenů JWT vyprodukovaných pomocí IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* V `Startup.Configure`:

  * Middleware IdentityServer zpřístupňuje koncové body Open ID Connect (OIDC):

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

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Pomocná metoda konfiguruje [IdentityServer](https://identityserver.io/) pro scénáře ASP.NET Core. IdentityServer je výkonná a rozšiřitelná architektura pro zpracování otázek zabezpečení aplikací. IdentityServer zpřístupňuje zbytečné složitosti pro nejběžnější scénáře. V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod. Jakmile se vaše potřeby ověřování změní, je k dispozici plná síla IdentityServer, která vám umožní přizpůsobit ověřování podle požadavků aplikace.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování. Zásady jsou nakonfigurovány tak, aby umožňovaly Identity zpracování všech požadavků směrovaných na jakoukoli dílčí cestu v Identity prostoru URL `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Zpracovává všechny ostatní požadavky. Tato metoda navíc:

* Zaregistruje `{APPLICATION NAME}API` prostředek rozhraní API s IdentityServer s výchozím oborem `{APPLICATION NAME}API` .
* Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

V `WeatherForecastController` (*Controllers/WeatherForecastController. cs*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) je atribut použit pro třídu. Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku. Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené nástrojem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Pomocná metoda se konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužná rutina pro požadavky na aplikaci.

### <a name="applicationdbcontext"></a>ApplicationDbContext

V `ApplicationDbContext` (*data/ApplicationDbContext. cs*) <xref:Microsoft.EntityFrameworkCore.DbContext> rozšiřuje <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> na zahrnutí schématu pro IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .

Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity <xref:Microsoft.EntityFrameworkCore.DbContext> tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodě.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

V `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*) se koncový bod klienta zřídí pro poskytování parametrů OIDC.

### <a name="app-settings-files"></a>Soubory nastavení aplikace

V souboru nastavení aplikace (*appSettings. JSON*) v kořenovém adresáři projektu `IdentityServer` obsahuje část seznam konfigurovaných klientů. V následujícím příkladu je jeden klient. Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth. Profil indikuje typ aplikace, která se konfiguruje. Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>Konfigurace klientské aplikace

### <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty ( `Individual` ), aplikace automaticky obdrží odkaz na balíček pro balíček [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) v souboru projektu aplikace. Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Podpora autorizace rozhraní API

Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v rámci balíčku [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Tato metoda nastaví služby, které aplikace požaduje, aby spolupracovaly se stávajícím autorizačním systémem.

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

`LoginDisplay`Součást (*Shared/LoginDisplay. Razor*) je vykreslena ve `MainLayout` komponentě (*Shared/MainLayout. Razor*) a spravuje následující chování:

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

V klientské aplikaci vytvořte vlastní objekt pro vytváření uživatelů. IdentityServer odesílá v jedné deklaraci více rolí jako pole JSON `role` . Jedna role se pošle jako řetězcová hodnota v deklaraci identity. Továrna vytvoří jednotlivou `role` deklaraci identity pro každou roli uživatele.

*CustomUserFactory.cs*:

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

V klientské aplikaci Zaregistrujte továrnu v `Program.Main` (*program.cs*):

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

### <a name="configure-identity-server"></a>Konfigurovat Identity Server

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

*ProfileService.cs*:

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

* [AuthorizeView – komponenta](xref:security/blazor/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` direktiva atributu](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )
* [Procedurální logika](xref:security/blazor/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )

  Podporuje se vícenásobné testy rolí:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`se naplní v klientské aplikaci pomocí uživatelského jména uživatele, což je obvykle jejich přihlašovací e-mailová adresa.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* [Nasazení do Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Import certifikátu z Key Vault (dokumentace k Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
