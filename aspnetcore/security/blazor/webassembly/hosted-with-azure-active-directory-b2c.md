---
title: Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 232a4247f8bea23eec3dc35cba4659c88887124d
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083879"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory B2C

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Tento článek popisuje, jak vytvořit samostatnou aplikaci Blazorového sestavení, která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) .

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrace aplikací v AAD B2C a vytvoření řešení

### <a name="create-a-tenant"></a>Vytvoření tenanta

Postupujte podle pokynů v [kurzu: vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pro vytvoření tenanta AAD B2C a zaznamenání následujících informací:

* Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)
* AAD B2C domény klienta (například `contoso.onmicrosoft.com`)

### <a name="register-a-server-api-app"></a>Registrace aplikace API serveru

Postupujte podle pokynů v [kurzu: registrace aplikace v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *aplikaci API serveru* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:

1. Vyberte **Nová registrace**.
1. Zadejte **název** aplikace (například **Blazor Server AAD B2C**).
1. U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.** (více tenantů) pro toto prostředí.
1. *Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.
1. Potvrďte, že **oprávnění** > **udělují správcům oprávnění k OpenID a offline_access** je povolená.
1. Vyberte **Zaregistrovat**.

Ve **vystavení rozhraní API**:

1. Vyberte **Přidat obor**.
1. Vyberte **Uložit a pokračovat**.
1. Zadejte **název oboru** (například `API.Access`).
1. Zadejte **Zobrazovaný název souhlasu správce** (například `Access API`).
1. Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.`).
1. Potvrďte, že je **stav** nastavený na **povoleno**.
1. Vyberte **Přidat obor**.

Zaznamenejte následující informace:

* *Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)
* ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222`)
* *Aplikace API serveru* Identifikátor URI ID aplikace (například `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure Portal může být ve výchozím nastavení hodnota ID klienta)
* Výchozí obor (například `API.Access`)

### <a name="register-a-client-app"></a>Registrace klientské aplikace

Postupujte podle pokynů v [kurzu: znovu zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *klientskou aplikaci* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:

1. Vyberte **Nová registrace**.
1. Zadejte **název** aplikace (například **Blazor AAD B2C klienta**).
1. U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.** (více tenantů) pro toto prostředí.
1. Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro přesměrování `https://localhost:5001/authentication/login-callback`.
1. Potvrďte, že **oprávnění** > **udělují správcům oprávnění k OpenID a offline_access** je povolená.
1. Vyberte **Zaregistrovat**.

V > ověřování **Konfigurace platforem** > **webu**:

1. Potvrďte, že je k dispozici **identifikátor URI pro přesměrování** `https://localhost:5001/authentication/login-callback`.
1. V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.
1. Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.
1. Vyberte tlačítko **Uložit**.

V **oprávněních rozhraní API**:

1. Ověřte, že aplikace má **Microsoft Graph** > **uživatel. oprávnění číst** .
1. Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.
1. Ve sloupci **název** vyberte *aplikace API serveru* (například **Blazor Server AAD B2C**).
1. Otevřete seznam **rozhraní API** .
1. Povolte přístup k rozhraní API (například `API.Access`).
1. Vyberte **Přidat oprávnění**.
1. Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** . Odstranění potvrďte výběrem **Ano**.

V **domácích** > **Azure AD B2C** > **uživatelských toků**:

[Vytvoření uživatelského toku pro registraci a přihlašování](/azure/active-directory-b2c/tutorial-create-user-flows)

Zaznamenejte následující informace:

* Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333`).
* Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin`).

### <a name="create-the-app"></a>Vytvoření aplikace

Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`). Název složky se také stal součástí názvu projektu.

## <a name="server-app-configuration"></a>Konfigurace aplikace serveru

*Tato část se vztahuje k **serverové** aplikaci řešení.*

### <a name="authentication-package"></a>Ověřovací balíček

Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Podpora ověřovací služby

Metoda `AddAuthentication` nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování. Metoda `AddAzureADBearer` nastaví konkrétní parametry v obslužné rutině JWT nosiče vyžadované pro ověřování tokenů emitovaných Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` a `UseAuthorization` zajistěte, aby:

* Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.
* Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>Nastavení aplikace

Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>Kontroler WeatherForecast

Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s atributem `[Authorize]` použitým pro kontroler. Je **důležité** si uvědomit, že:

* Atribut `[Authorize]` v tomto kontroleru rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.
* Atribut `[Authorize]` použitý v Blazor aplikace WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.

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

## <a name="client-app-configuration"></a>Konfigurace klientské aplikace

*Tato část se vztahuje k **klientské** aplikaci řešení.*

### <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala individuální účet B2C (`IndividualB2C`), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.

`Microsoft.Authentication.WebAssembly.Msal` balíček do aplikace přidá balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.

### <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s metodou rozšíření `AddMsalAuthentication` poskytovanou balíčkem `Microsoft.Authentication.WebAssembly.Msal`. Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace. Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.

Šablona Blazor WebAssembly automaticky nakonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API pro výchozí obor, který je k dispozici pro příkaz `dotnet new` (`{APP ID URI}/{DEFAULT SCOPE}`).

Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:

* Ve výchozím nastavení zahrnuty v žádosti o přihlášení.
* Slouží ke zřízení přístupového tokenu hned po ověření.

Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory. Další obory je možné přidat pro další aplikace API podle potřeby:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authentication/azure-ad-b2c>
* [Kurz: Vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
