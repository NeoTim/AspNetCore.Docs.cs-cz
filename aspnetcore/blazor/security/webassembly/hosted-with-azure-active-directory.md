---
title: Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory
author: guardrex
description: Naučte se zabezpečit aplikaci ASP.NET Core Blazor WebAssembly Hosted pomocí Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e6f514793a2efde120f70ac58f4ad4be7516ada7
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900835"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

Tento článek popisuje, jak vytvořit [hostovanou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) .

## <a name="register-apps-in-aad-and-create-solution"></a>Registrace aplikací v AAD a vytvoření řešení

### <a name="create-a-tenant"></a>Vytvoření tenanta

Postupujte podle pokynů v [rychlém startu: nastavení tenanta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření TENANTA v AAD.

### <a name="register-a-server-api-app"></a>Registrace aplikace API serveru

Postupujte podle pokynů v [rychlém startu: registrace aplikace pomocí platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat služby Azure AAD k registraci aplikace AAD pro *aplikaci API serveru* a následnému provedení následujících akcí:

1. V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor Server AAD**).
1. Vyberte **podporované typy účtů**. Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).
1. *Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.
1. Zrušte zaškrtnutí políčka **oprávnění**  >  **udělit souhlas správce OpenID a offline_access oprávnění** .
1. Vyberte **Zaregistrovat**.

Zaznamenejte následující informace:

* *Aplikace API serveru* ID aplikace (klienta) (například `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* ID adresáře (tenanta) (například `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )
* Primární/Vydavatel nebo doména klienta služby AAD (například `contoso.onmicrosoft.com` ): doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.

V okně **oprávnění rozhraní API**odeberte **Microsoft Graph**  >  **uživatel. číst** , protože aplikace nevyžaduje přihlášení ani přístup k profilu uživatele.

Ve **vystavení rozhraní API**:

1. Vyberte **Přidat obor**.
1. Vyberte **Uložit a pokračovat**.
1. Zadejte **název oboru** (například `API.Access` ).
1. Zadejte **Zobrazovaný název souhlasu správce** (například `Access API` ).
1. Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.` ).
1. Potvrďte, že je **stav** nastavený na **povoleno**.
1. Vyberte **Přidat obor**.

Zaznamenejte následující informace:

* Identifikátor URI ID aplikace (například, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` nebo vlastní hodnota, kterou zadáte)
* Název oboru (například `API.Access` )

### <a name="register-a-client-app"></a>Registrace klientské aplikace

Postupujte podle pokynů v [rychlém startu: registrace aplikace s využitím platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *`Client`* aplikaci a následnému provedení následujícího postupu:

::: moniker range=">= aspnetcore-5.0"

1. V **Azure Active Directory** > **Registrace aplikací**vyberte možnost **Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor AAD klienta**).
1. Vyberte **podporované typy účtů**. Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).
1. Rozevírací seznam **identifikátor URI pro přesměrování** nastavte na **JEDNOSTRÁNKOVOU aplikaci (Spa)** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` . Výchozí port pro aplikaci běžící na Kestrel je 5001. Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace. Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve *`Server`* vlastnostech aplikace na panelu **ladění** . Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování. V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.
1. Zrušte zaškrtnutí políčka **oprávnění** > **udělit souhlas správce OpenID a offline_access oprávnění** .
1. Vyberte **Zaregistrovat**.

Poznamenejte si *`Client`* ID aplikace (klienta) aplikace (například `4369008b-21fa-427c-abaa-9b53bf58e538` ).

V **Authentication** > **Platform configurations** > **aplikaci Single-Page konfigurace platformy ověřování (Spa)**:

1. Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.
1. Pro **implicitní udělení**zajistěte, aby se u **přístupových tokenů** a **tokenů ID** **nevybrali zaškrtávací** políčka.
1. Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.
1. Vyberte tlačítko **Uložit**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. V **Azure Active Directory** > **Registrace aplikací**vyberte možnost **Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor AAD klienta**).
1. Vyberte **podporované typy účtů**. Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).
1. Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` . Výchozí port pro aplikaci běžící na Kestrel je 5001. Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace. Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve *`Server`* vlastnostech aplikace na panelu **ladění** . Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování. V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.
1. Zrušte zaškrtnutí políčka **oprávnění** > **udělit souhlas správce OpenID a offline_access oprávnění** .
1. Vyberte **Zaregistrovat**.

Poznamenejte si *`Client`* ID aplikace (klienta) aplikace (například `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Na webu konfigurace **ověřovacích** > **platforem** > **Web**:

1. Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.
1. V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.
1. Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.
1. Vyberte tlačítko **Uložit**.

::: moniker-end

V **oprávněních rozhraní API**:

1. Potvrďte, že aplikace má **Microsoft Graph**  >  oprávnění**uživatel. číst** .
1. Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.
1. Vyberte *aplikaci API serveru* ze sloupce **název** (například ** Blazor Server AAD**).
1. Otevřete seznam **rozhraní API** .
1. Povolte přístup k rozhraní API (například `API.Access` ).
1. Vyberte **Přidat oprávnění**.
1. Klikněte na tlačítko **udělení souhlasu správce pro {Name}** . Akci potvrďte výběrem **Ano**.

### <a name="create-the-app"></a>Vytvoření aplikace

V prázdné složce Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a spusťte příkaz v příkazovém prostředí:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Zástupný symbol                  | Název Azure Portal                                     | Příklad                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`     | ID aplikace (klienta) pro *`Client`* aplikaci        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | Název oboru                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | ID aplikace (klienta) pro *aplikaci Server API*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | Identifikátor URI ID aplikace                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | Primární/Vydavatel/doména tenanta                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | ID adresáře (tenanta)                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Změna konfigurace může být nutná při použití tenanta Azure s neověřenou doménou vydavatele, která je popsaná v části [nastavení aplikace](#app-settings) .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Změna konfigurace může být nutná při použití tenanta Azure s neověřenou doménou vydavatele, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .

::: moniker-end

> [!NOTE]
> V Azure Portal *`Client`* je **identifikátor URI pro přesměrování** konfigurace platformy aplikace nakonfigurovaný pro port 5001 pro aplikace, které běží na serveru Kestrel s výchozími nastaveními.
>
> Pokud *`Client`* je aplikace spuštěná na náhodném IIS Express portu, můžete port pro aplikaci najít ve vlastnostech *aplikace API serveru* na panelu **ladění** .
>
> Pokud port nebyl dříve nakonfigurovaný se *`Client`* známým portem aplikace, vraťte se k *`Client`* registraci aplikace v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.

## <a name="server-app-configuration"></a>*`Server`* Konfigurace aplikace

*Tato část se týká **`Server`** aplikace řešení.*

### <a name="authentication-package"></a>Ověřovací balíček

::: moniker range=">= aspnetcore-5.0"

Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API s Identity platformou Microsoft je poskytována následujícími balíčky:

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese NuGet.org.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je zajištěna [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) balíčkem:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

::: moniker-end

### <a name="authentication-service-support"></a>Podpora ověřovací služby

::: moniker range=">= aspnetcore-5.0"

`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování. <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>Metoda konfiguruje služby pro ochranu webového rozhraní API pomocí platformy Microsoft Identity Platform v 2.0. Tato metoda očekává `AzureAd` v konfiguraci aplikace oddíl s nezbytným nastavením pro inicializaci možností ověřování.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování. <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Ujistěte se, že:

* Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.
* Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>Uživatel. Identity . Jméno

Ve výchozím nastavení *`Server`* rozhraní API aplikace naplní `User.Identity.Name` hodnotu z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu deklarace (například `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).

Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu z `name` typu deklarace, nakonfigurujte <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Nastavení aplikace

::: moniker range=">= aspnetcore-5.0"

`appsettings.json`Soubor obsahuje možnosti pro konfiguraci obslužné rutiny nosiče JWT používané k ověření přístupových tokenů:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

Příklad:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`appsettings.json`Soubor obsahuje možnosti pro konfiguraci obslužné rutiny nosiče JWT používané k ověření přístupových tokenů:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Příklad:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

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

Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

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
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace. Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.

Soubor zadal konfiguraci `wwwroot/appsettings.json` :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Příklad:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
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

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/security/webassembly/additional-scenarios>
* [Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentace k platformě Microsoft Identity Platform](/azure/active-directory/develop/)
