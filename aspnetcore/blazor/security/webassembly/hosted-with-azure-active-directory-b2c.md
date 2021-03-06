---
title: Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory B2C
author: guardrex
description: Naučte se zabezpečit aplikaci ASP.NET Core Blazor WebAssembly Hosted pomocí Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: aa6c865f5fd51d1634bde3ac96e1fddc7216a801
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900944"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory B2C

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

Tento článek popisuje, jak vytvořit [hostovanou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) .

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrace aplikací v AAD B2C a vytvoření řešení

### <a name="create-a-tenant"></a>Vytvoření tenanta

Postupujte podle pokynů v [kurzu: vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pro vytvoření tenanta AAD B2C.

Poznamenejte si instanci AAD B2C (například `https://contoso.b2clogin.com/` , která obsahuje koncové lomítko). Instance je schéma a hostitel registrace aplikace Azure B2C, který najdete otevřením okna **koncové body** na stránce **Registrace aplikací** v Azure Portal.

### <a name="register-a-server-api-app"></a>Registrace aplikace API serveru

Postupujte podle pokynů v [kurzu: Zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *aplikaci API serveru* a pak postupujte takto:

1. V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor Server AAD B2C**).
1. U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**
1. *Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.
1. Ověřte, že je vybraná **oprávnění**  >  **udělit souhlas správce k OpenID a oprávněním offline_access** .
1. Vyberte **Zaregistrovat**.

Zaznamenejte následující informace:

* *Aplikace API serveru* ID aplikace (klienta) (například `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* Primární/Vydavatel nebo doména klienta služby AAD (například `contoso.onmicrosoft.com` ): doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.

Ve **vystavení rozhraní API**:

1. Vyberte **Přidat obor**.
1. Vyberte **Uložit a pokračovat**.
1. Zadejte **název oboru** (například `API.Access` ).
1. Zadejte **Zobrazovaný název souhlasu správce** (například `Access API` ).
1. Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.` ).
1. Potvrďte, že je **stav** nastavený na **povoleno**.
1. Vyberte **Přidat obor**.

Zaznamenejte následující informace:

* Identifikátor URI ID aplikace (například, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` nebo vlastní hodnota, kterou jste zadali)
* Název oboru (například `API.Access` )

### <a name="register-a-client-app"></a>Registrace klientské aplikace

Postupujte podle pokynů v [kurzu: Zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) znovu pro registraci aplikace AAD pro *`Client`* aplikaci a pak postupujte takto:

::: moniker range=">= aspnetcore-5.0"

1. V **Azure Active Directory** > **Registrace aplikací**vyberte možnost **Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor AAD B2C klienta**).
1. U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**
1. Rozevírací seznam **identifikátor URI pro přesměrování** nastavte na **JEDNOSTRÁNKOVOU aplikaci (Spa)** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` . Výchozí port pro aplikaci běžící na Kestrel je 5001. Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace. Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve *`Server`* vlastnostech aplikace na panelu **ladění** . Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování. V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.
1. Ověřte, že je vybraná **oprávnění** > **udělit souhlas správce k OpenID a oprávněním offline_access** .
1. Vyberte **Zaregistrovat**.

1. Poznamenejte si ID aplikace (klienta) (například `4369008b-21fa-427c-abaa-9b53bf58e538` ).

V **Authentication** > **Platform configurations** > **aplikaci Single-Page konfigurace platformy ověřování (Spa)**:

1. Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.
1. Pro **implicitní udělení**zajistěte, aby se u **přístupových tokenů** a **tokenů ID** **nevybrali zaškrtávací** políčka.
1. Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.
1. Vyberte tlačítko **Uložit**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. V **Azure Active Directory** > **Registrace aplikací**vyberte možnost **Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor AAD B2C klienta**).
1. U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**
1. Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` . Výchozí port pro aplikaci běžící na Kestrel je 5001. Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace. Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve *`Server`* vlastnostech aplikace na panelu **ladění** . Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování. V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.
1. Ověřte, že je vybraná **oprávnění** > **udělit souhlas správce k OpenID a oprávněním offline_access** .
1. Vyberte **Zaregistrovat**.

Poznamenejte si ID aplikace (klienta) (například `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Na webu konfigurace **ověřovacích** > **platforem** > **Web**:

1. Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.
1. V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.
1. Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.
1. Vyberte tlačítko **Uložit**.

::: moniker-end

V **oprávněních rozhraní API**:

1. Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.
1. Ve sloupci **název** vyberte *aplikace API serveru* (například ** Blazor Server AAD B2C**).
1. Otevřete seznam **rozhraní API** .
1. Povolte přístup k rozhraní API (například `API.Access` ).
1. Vyberte **Přidat oprávnění**.
1. Klikněte na tlačítko **udělení souhlasu správce pro {Name}** . Akci potvrďte výběrem **Ano**.

V **Home**  >  **Azure AD B2C**  >  **toky pro uživatele**doma:

[Vytvoření uživatelského toku pro registraci a přihlašování](/azure/active-directory-b2c/tutorial-create-user-flows)

**Application claims**  >  Aby bylo možné vyplnit součást (), vyberte alespoň atribut uživatelské**jméno zobrazení** deklarací identit aplikace `context.User.Identity.Name` `LoginDisplay` `Shared/LoginDisplay.razor` .

Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>Vytvoření aplikace

Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Zástupný symbol                   | Název Azure Portal                                     | Příklad                                      |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{AAD B2C INSTANCE}`          | Instance                                              | `https://contoso.b2clogin.com/`              |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`      | ID aplikace (klienta) pro *`Client`* aplikaci        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`             | Název oboru                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}`  | ID aplikace (klienta) pro *aplikaci Server API*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`     | Identifikátor URI ID aplikace                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Tok uživatele pro registraci nebo přihlášení                             | `B2C_1_signupsignin1`                        |
| `{TENANT DOMAIN}`             | Primární/Vydavatel/doména tenanta                       | `contoso.onmicrosoft.com`                    |

Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.

> [!NOTE]
> Obor nastavený podle hostované Blazor šablony může mít opakovaný hostitel identifikátoru URI ID aplikace. Ověřte, že je obor konfigurovaný pro `DefaultAccessTokenScopes` kolekci v `Program.Main` ( `Program.cs` ) *`Client`* aplikace správný.

> [!NOTE]
> V Azure Portal *`Client`* je **identifikátor URI pro přesměrování** konfigurace platformy aplikace nakonfigurovaný pro port 5001 pro aplikace, které běží na serveru Kestrel s výchozími nastaveními.
>
> Pokud *`Client`* je aplikace spuštěná na náhodném IIS Express portu, můžete port pro aplikaci najít ve vlastnostech *aplikace API serveru* na panelu **ladění** .
>
> Pokud port nebyl dříve nakonfigurovaný se *`Client`* známým portem aplikace, vraťte se k *`Client`* registraci aplikace v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.

## <a name="server-app-configuration"></a>*`Server`* Konfigurace aplikace

*Tato část se týká **`Server`** aplikace řešení.*

### <a name="authentication-package"></a>Ověřovací balíček

Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je zajištěna [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) balíčkem:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

### <a name="authentication-service-support"></a>Podpora ověřovací služby

`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování. <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory B2C:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Ujistěte se, že:

* Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.
* Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>Uživatel. Identity . Jméno

Ve výchozím nastavení `User.Identity.Name` není naplněno.

Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu z `name` typu deklarace, nakonfigurujte <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Nastavení aplikace

`appsettings.json`Soubor obsahuje možnosti pro konfiguraci obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Příklad:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Kontroler WeatherForecast

Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem použitým pro kontroler. Je **důležité** si uvědomit, že:

* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.
* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut použitý v Blazor WebAssembly aplikaci slouží pouze jako pomocný parametr aplikace, který by měl být uživatel autorizován, aby mohla aplikace správně fungovat.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>*`Client`* Konfigurace aplikace

*Tato část se týká **`Client`** aplikace řešení.*

### <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala individuální účet B2C ( `IndividualB2C` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) do této aplikace přidá balíček.

### <a name="authentication-service-support"></a>Podpora ověřovací služby

Přidávají <xref:System.Net.Http.HttpClient> se podpory pro instance, které zahrnují přístupové tokeny při vytváření žádostí na serverový projekt.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.ServerAPI` ).

Podpora ověřování uživatelů je registrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) balíčkem. Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace. Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.

Soubor zadal konfiguraci `wwwroot/appsettings.json` :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Příklad:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a>Obory přístupového tokenu

Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:

* Ve výchozím nastavení zahrnuty v žádosti o přihlášení.
* Slouží ke zřízení přístupového tokenu hned po ověření.

Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory. Další obory je možné přidat pro další aplikace API podle potřeby:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Určete další obory pomocí `AdditionalScopesToConsent` :

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

Další informace najdete v následujících částech článku o *dalších scénářích* :

* [Vyžádání dalších přístupových tokenů](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Připojit tokeny k odchozím žádostem](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Režim přihlášení

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Importovat soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Komponenta LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Součást ověřování

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Komponenta FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci z projektu serveru. Při použití sady Visual Studio buď:

* Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .
* Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/security/webassembly/additional-scenarios>
* [Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Kurz: Vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Dokumentace k platformě Microsoft Identity Platform](/azure/active-directory/develop/)
