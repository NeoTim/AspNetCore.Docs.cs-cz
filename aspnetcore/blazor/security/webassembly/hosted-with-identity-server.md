---
title: Zabezpečení hostované Blazor WebAssembly aplikace v ASP.NET Core se Identity serverem
author: guardrex
description: Seznamte se s postupem zabezpečení hostované Blazor WebAssembly aplikace ASP.NET Core se Identity serverem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 6ae8c55fcfc85dc725a7dd20a7dbecba063a13e9
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900779"
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

## <a name="server-app-configuration"></a>*`Server`* Konfigurace aplikace

Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.

### <a name="startup-class"></a>Spouštěcí třída

`Startup`Třída obsahuje následující doplňky.

* V `Startup.ConfigureServices`:

  * ASP.NET Core Identity:

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

## <a name="client-app-configuration"></a>*`Client`* Konfigurace aplikace

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

V *`Client`* aplikaci vytvořte vlastní objekt pro vytváření uživatelů. Identity Server odesílá v jedné deklaraci více rolí jako pole JSON `role` . Jedna role se pošle jako řetězcová hodnota v deklaraci identity. Továrna vytvoří jednotlivou `role` deklaraci identity pro každou roli uživatele.

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
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

V *`Client`* aplikaci Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

V *`Server`* aplikaci zavolejte <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> na Identity Tvůrce, který přidá služby související s rolemi:

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

V *`Server`* aplikaci:

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

V *`Server`* aplikaci vytvořte `ProfileService` implementaci.

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

V *`Server`* aplikaci Zaregistrujte službu profilů v nástroji `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Použití autorizačních mechanismů

V *`Client`* této aplikaci jsou v tuto chvíli funkční přístupy k komponentám autorizace. Kterýkoli z autorizačních mechanismů v součástech může použít roli k autorizaci uživatele:

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

`User.Identity.Name` aplikace se v aplikaci naplní *`Client`* uživatelským jménem uživatele, což je obvykle jejich přihlašovací e-mailová adresa.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a>Hostování v Azure App Service s vlastní doménou

Následující pokyny popisují, jak nasadit hostovanou Blazor WebAssembly aplikaci se Identity serverem pro [Azure App Service](https://azure.microsoft.com/services/app-service/) s vlastní doménou.

Pro tento scénář hostování **nepoužívejte stejný** certifikát pro [ Identity podpisový klíč tokenu serveru](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) a zabezpečenou komunikaci protokolu HTTPS s prohlížeči:

* Používání různých certifikátů pro tyto dvě požadavky je dobrým zvykem zabezpečení, protože pro každý účel izoluje privátní klíče.
* Certifikáty TLS pro komunikaci s prohlížeči se spravují nezávisle, aniž by to ovlivnilo Identity podepisování tokenů serveru.
* Když [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) poskytne certifikát do App Service aplikace pro vlastní doménovou vazbu, Identity Server nemůže získat stejný certifikát od Azure Key Vault pro podepisování tokenů. I když Identity je možné nakonfigurovat server tak, aby používal stejný certifikát TLS z fyzické cesty, je vkládání certifikátů zabezpečení do správy zdrojového kódu **špatným postupem a mělo by se ve většině případů vyhnout**.

V následujících pokynech se certifikát podepsaný svým držitelem vytvoří v Azure Key Vault výhradně pro Identity Podepisování tokenu serveru. IdentityKonfigurace serveru používá certifikát trezoru klíčů prostřednictvím `My`  >  `CurrentUser` úložiště certifikátů aplikace. Další certifikáty, které se používají pro přenosy HTTPS s vlastními doménami, se vytvářejí a konfigurují odděleně od Identity podpisového certifikátu serveru.

Konfigurace aplikace, Azure App Service a Azure Key Vault pro hostování s vlastní doménou a HTTPS:

1. Vytvořte [plán App Service](/azure/app-service/overview-hosting-plans) s úrovní plánu `Basic B1` nebo vyšší. App Service vyžaduje, `Basic B1` aby úroveň služby nebo vyšší používala vlastní domény.
1. Vytvořte certifikát PFX pro komunikaci zabezpečeného prohlížeče (protokol HTTPS) lokality s běžným názvem plně kvalifikovaného názvu domény (FQDN) webu, který vaše organizace řídí (například `www.contoso.com` ). Vytvořte certifikát pomocí:
   * Použití klíče
     * Ověření digitálního podpisu ( `digitalSignature` )
     * Šifrování klíče ( `keyEncipherment` )
   * Rozšířené/rozšířené použití klíče
     * Ověřování klientů (1.3.6.1.5.5.7.3.2)
     * Ověřování serveru (1.3.6.1.5.5.7.3.1)

   Chcete-li vytvořit certifikát, použijte jeden z následujících přístupů nebo jakýkoli jiný vhodný nástroj nebo online službu:

   * [Azure Key Vault](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [MakeCert ve Windows](/windows/desktop/seccrypto/makecert)
   * [OpenSSL](https://www.openssl.org)

   Poznamenejte si heslo, které se použije později k importu certifikátu do Azure Key Vault.

   Další informace o Azure Key Vaultch certifikátech najdete v článku [Azure Key Vault: certifikáty](/azure/key-vault/certificates/).
1. Vytvořte novou Azure Key Vault nebo použijte existující Trezor klíčů v předplatném Azure.
1. V oblasti **certifikáty** trezoru klíčů importujte certifikát webu PFX. Poznamenejte si kryptografický otisk certifikátu, který se v konfiguraci aplikace používá později.
1. V Azure Key Vault Vygenerujte nový certifikát podepsaný svým držitelem pro Identity Podepisování tokenu serveru. Zadejte **název** certifikátu a **Předmět**. **Předmět** je určen jako `CN={COMMON NAME}` , kde `{COMMON NAME}` zástupný symbol je běžný název certifikátu. Běžným názvem může být libovolný alfanumerický řetězec. Například `CN=IdentityServerSigning` je platný **Předmět**certifikátu. Použijte výchozí **Rozšířená nastavení konfigurace zásad** . Poznamenejte si kryptografický otisk certifikátu, který se v konfiguraci aplikace používá později.
1. V Azure Portal přejděte na Azure App Service a vytvořte novou App Service s následující konfigurací:
   * **Publikování** je nastaveno na `Code` .
   * **Zásobník modulu runtime** nastavený na modul runtime aplikace
   * V poli **SKU a velikost**potvrďte, že je úroveň App Service `Basic B1` nebo vyšší.  App Service vyžaduje, `Basic B1` aby úroveň služby nebo vyšší používala vlastní domény.
1. Jakmile Azure vytvoří App Service, otevřete **konfiguraci** aplikace a přidejte nové nastavení aplikace s určením kryptografických otisků certifikátů, které jste si poznamenali dříve. Klíč nastavení aplikace je `WEBSITE_LOAD_CERTIFICATES` . Kryptografické otisky certifikátů v hodnotě nastavení aplikace oddělte čárkou, jak ukazuje následující příklad:
   * Klíč: `WEBSITE_LOAD_CERTIFICATES`
   * Hodnota: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`

   V Azure Portal je ukládání nastavení aplikace dvoustupňový proces: uložte `WEBSITE_LOAD_CERTIFICATES` nastavení klíč-hodnota a potom v horní části okna vyberte tlačítko **Uložit** .
1. Vyberte **Nastavení TLS/SSL**aplikace. Vyberte **certifikáty privátních klíčů (. pfx)**. Dvakrát pomocí procesu **import Key Vault certifikátů** importujte certifikát webu pro komunikaci pomocí protokolu HTTPS a Identity podpisový certifikát tokenu serveru podepsaného svým držitelem.
1. Přejděte do okna **vlastní domény** . Na webu vašeho doménového registrátora nakonfigurujte doménu pomocí **IP adresy** a **ID ověření vlastní domény** . Typická konfigurace domény zahrnuje:
   * **Záznam** a s **hostitelem** `@` a hodnotou IP adresy z Azure Portal.
   * **Záznam TXT** s **hostitelem** `asuid` a hodnotou identifikátoru ověřování vygenerovaných Azure a poskytovaný Azure Portal.

   Ujistěte se, že jste změny uložili na webu vašeho registrátora domény správně. Některé weby registrátora vyžadují dvoustupňový proces ukládání záznamů v doméně: jeden nebo více záznamů se ukládá jednotlivě a pomocí samostatného tlačítka aktualizují registraci domény.
1. Vraťte se do okna **vlastní domény** v Azure Portal. Vyberte **Přidat vlastní doménu**. Vyberte možnost **záznam A** . Zadejte doménu a vyberte **ověřit**. Pokud jsou záznamy v doméně správné a šířené přes Internet, portál vám umožní vybrat tlačítko **Přidat vlastní doménu** .

   Může trvat několik dní, než se změny v registraci domény šíří v rámci služby DNS (Internet Domain Name Server) po jejich zpracování doménovým registrátorem. Pokud se záznamy v doméně během tří pracovních dnů neaktualizují, potvrďte, že záznamy jsou správně nastavené s doménovým registrátorem, a obraťte se na zákaznickou podporu.
1. V okně **vlastní domény** je **stav protokolu SSL** pro danou doménu označený `Not Secure` . Vyberte odkaz **Přidat vazbu** . Vyberte certifikát HTTPS lokality z trezoru klíčů pro vlastní doménovou vazbu.
1. V aplikaci Visual Studio otevřete soubor nastavení aplikace v projektu *serveru* ( `appsettings.json` nebo `appsettings.Production.json` ). V Identity konfiguraci serveru přidejte následující `Key` část. Zadejte **Předmět** certifikátu podepsaného svým držitelem pro `Name` klíč. V následujícím příkladu je běžný název certifikátu přiřazený v trezoru klíčů `IdentityServerSigning` , což má **za** následek `CN=IdentityServerSigning` :

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. V aplikaci Visual Studio vytvořte Azure App Service [profil publikování](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) pro *serverový* projekt. V řádku nabídek vyberte: **sestavit**  >  **publikovat**  >  **novou**službu  >  **Azure**  >  **Azure App Service** (Windows nebo Linux). Když je sada Visual Studio připojená k předplatnému Azure, můžete si nastavit **zobrazení** prostředků Azure podle **typu prostředku**. Přejděte v seznamu **webové aplikace** , vyhledejte App Service pro aplikaci a vyberte ji. Vyberte **Dokončit**.
1. Když se Visual Studio vrátí do okna **publikovat** , automaticky se zjistí závislosti trezoru klíčů a SQL serverch databázových služeb.

   Pro službu trezoru klíčů se nevyžadují žádné změny konfigurace výchozích nastavení.

   Pro účely testování je možné nasadit místní databázi [SQLite](https://www.sqlite.org/index.html) aplikace, která je nakonfigurovaná ve výchozím nastavení Blazor šablonou, a to bez další konfigurace. Konfigurace jiné databáze pro Identity Server v produkčním prostředí je mimo rámec tohoto článku. Další informace najdete v tématu prostředky databáze v následujících sadách dokumentace:
   * [App Service](/azure/app-service/)
   * [Identity WebServer](https://identityserver4.readthedocs.io/en/latest/)

1. V části název profilu nasazení v horní části okna vyberte odkaz **Upravit** . Změňte cílovou adresu URL na adresu URL vlastní domény webu (například `https://www.contoso.com` ). Uložte nastavení.
1. Publikujte aplikaci. Visual Studio otevře okno prohlížeče a požádá ho o web ve své vlastní doméně.

Dokumentace k Azure obsahuje další podrobnosti o používání služeb Azure a vlastních domén s vazbou TLS v App Service, včetně informací o použití záznamů CNAME namísto záznamů. Další informace naleznete v následujících zdrojích:

* [Dokumentace k App Service](/azure/app-service/)
* [Kurz: mapování stávajícího vlastního názvu DNS na Azure App Service](/azure/app-service/app-service-web-tutorial-custom-domain)
* [Zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](/azure/app-service/configure-ssl-bindings)
* [Azure Key Vault](/azure/key-vault/)

Pro každý testovací běh aplikace po změně aplikace, konfigurace aplikace nebo služeb Azure v Azure Portal doporučujeme použít nové soukromé nebo anonymním okno prohlížeče. cookiePři záchodu s z předchozího testovacího běhu může dojít k selhání ověřování nebo autorizaci při testování lokality i v případě, že je Konfigurace lokality správná. Další informace o tom, jak nakonfigurovat aplikaci Visual Studio tak, aby otevřela nové v soukromém nebo anonymním okně prohlížeče pro každý testovací běh, najdete v části [ Cookie s a daty o webu](#cookies-and-site-data) .

Když se v Azure Portal změní konfigurace App Service, obecně se tyto aktualizace projeví rychleji, ale ne okamžitě. V některých případech je třeba počkat krátce App Service restartováním, aby se změna konfigurace projevila.

Pokud řešíte problém s načtením certifikátu, spusťte v prostředí příkazového řádku PowerShellu Azure Portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) následující příkaz. Příkaz zobrazí seznam certifikátů, ke kterým může aplikace přistupovat z `My`  >  `CurrentUser` úložiště certifikátů. Výstup zahrnuje předměty certifikátů a kryptografické otisky užitečné při ladění aplikace:

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* [Nasazení do Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Import certifikátu z Key Vault (dokumentace k Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
