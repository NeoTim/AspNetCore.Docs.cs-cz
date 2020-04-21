---
title: Zabezpečení ASP.NET Blazor hostované webové sestavy pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661837"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Zabezpečení ASP.NET Blazor hostované webové sestavy pomocí Azure Active Directory

[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Tento článek popisuje, jak vytvořit [ Blazor hostovku webové sestavy,](xref:blazor/hosting-models#blazor-webassembly) která používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pro ověřování.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Registrace aplikací v AAD B2C a vytvoření řešení

### <a name="create-a-tenant"></a>Vytvoření tenanta

Postupujte podle pokynů v [úvodním startu: Nastavení klienta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření klienta v AAD.

### <a name="register-a-server-api-app"></a>Registrace aplikace serverového rozhraní API

Postupujte podle pokynů v [úvodním panelu: Zaregistrujte aplikaci s platformou microsoftu pro identity](/azure/active-directory/develop/quickstart-register-app) a další témata Azure AAD k registraci aplikace AAD pro *aplikaci Rozhraní API serveru* v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:

1. Vyberte **možnost Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor Server AAD**).
1. Zvolte **podporované typy účtů**. Pro toto prostředí můžete vybrat **účty v tomto organizačním adresáři (pouze** jeden tenant).
1. *Aplikace rozhraní API serveru* nevyžaduje identifikátor URI **přesměrování** v tomto scénáři, takže ponechte rozevírací soubor nastavený na **web** a nezadávejte identifikátor URI přesměrování.
1. Zamítejte**zaškrtávací** políčko **Oprávnění** > Udělit oprávnění k otevření a offline_access oprávnění.
1. Vyberte **Zaregistrovat**.

V **oprávnění chodrozhraní API**odeberte oprávnění Microsoft **Graph** > **User.Read,** protože aplikace nevyžaduje přístup k profilu přihlášení nebo uer.

V **vystavit rozhraní API**:

1. Vyberte **Přidat obor**.
1. Vyberte **Uložit a pokračovat**.
1. Zadejte **název oboru** `API.Access`(například).
1. Zadejte **zobrazovaný název souhlasu správce** `Access API`(například).
1. Uveďte **popis souhlasu** správce `Allows the app to access server app API endpoints.`(například).
1. Zkontrolujte, zda je **stav** nastaven na **povoleno**.
1. Vyberte **přidat obor**.

Zaznamenejte následující informace:

* *Aplikace rozhraní API serveru* ID aplikace (ID klienta) `11111111-1111-1111-1111-111111111111`(například)
* Identifikátor URI ID aplikace `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`(například , nebo vlastní hodnota, kterou jste zadali)
* ID adresáře (ID klienta) `222222222-2222-2222-2222-222222222222`(například)
* Doména klienta AAD `contoso.onmicrosoft.com`(například)
* Výchozí obor (například `API.Access`)

### <a name="register-a-client-app"></a>Registrace klientské aplikace

Postupujte podle pokynů v [úvodním panelu: Zaregistrujte aplikaci s platformou microsoftu pro identity](/azure/active-directory/develop/quickstart-register-app) a další témata Azure AAD k registraci aplikace AAD pro *klientskou aplikaci* v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:

1. Vyberte **možnost Nová registrace**.
1. Zadejte **název** aplikace (například ** Blazor AAD klienta).**
1. Zvolte **podporované typy účtů**. Pro toto prostředí můžete vybrat **účty v tomto organizačním adresáři (pouze** jeden tenant).
1. Ponechejte rozevírací soubor **URI přesměrování** nastavený `https://localhost:5001/authentication/login-callback`na **web**a poskytněte identifikátor URI přesměrování aplikace .
1. Zamítejte**zaškrtávací** políčko **Oprávnění** > Udělit oprávnění k otevření a offline_access oprávnění.
1. Vyberte **Zaregistrovat**.

V**konfiguracích platformy** >  **ověřování** > **web**:

1. Potvrďte, že `https://localhost:5001/authentication/login-callback` je přítomen **identifikátor URI přesměrování.**
1. V **případě implicitního udělení**zaškrtněte políčka u **tokenů přístupu** a **tokenů ID**.
1. Zbývající výchozí hodnoty pro aplikaci jsou přijatelné pro toto prostředí.
1. Vyberte tlačítko **Uložit**.

V **rozhraní API oprávnění**:

1. Zkontrolujte, zda má aplikace oprávnění **Microsoft Graph** > **User.Read.**
1. Vyberte **přidat oprávnění** následované **moje api**.
1. Ve sloupci **Název** vyberte *aplikaci Server API* (například ** Blazor Server AAD**).
1. Otevřete seznam **rozhraní API.**
1. Povolte přístup k rozhraní `API.Access`API (například).
1. Vyberte **Přidat oprávnění**.
1. Vyberte **tlačítko Udělit obsah správce pro {NÁZEV TENANTA}.** Výběrem **Ano** potvrďte.

Zaznamenejte ID aplikace *klientské aplikace* `33333333-3333-3333-3333-333333333333`(ID klienta) (například).

### <a name="create-the-app"></a>Vytvoření aplikace

Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a proveďte příkaz v příkazovém prostředí:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ). Název složky se také stane součástí názvu projektu.

> [!NOTE]
> Předejte `app-id-uri` možnosti IDENTIFIKÁTOR UD aplikace, ale všimněte si, že změna konfigurace může být vyžadována v klientské aplikaci, která je popsána v části [Obory tokenů aplikace Access.](#access-token-scopes)

## <a name="server-app-configuration"></a>Konfigurace serverové aplikace

*Tato část se týkajících se aplikace **Server** řešení.*

### <a name="authentication-package"></a>Ověřovací balíček

Podpora ověřování a autorizace volání ASP.NET základní webová api je `Microsoft.AspNetCore.Authentication.AzureAD.UI`poskytována :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Podpora ověřovací služby

Metoda `AddAuthentication` nastaví ověřovací služby v rámci aplikace a nakonfiguruje obslužnou rutinu Nosiče JWT jako výchozí metodu ověřování. Metoda `AddAzureADBearer` nastaví konkrétní parametry v obslužné rutině Nosiče JWT, které jsou nutné k ověření tokenů vysílaných službou Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`a `UseAuthorization` zajistit, aby:

* Aplikace se pokusí analyzovat a ověřit tokeny na příchozí požadavky.
* Jakýkoli požadavek na přístup k chráněnému prostředku bez správných pověření se nezdaří.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Ve výchozím nastavení se rozhraní `User.Identity.Name` API aplikace `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` Server naplní hodnotou z typu deklarace `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`(například).

Chcete-li nakonfigurovat aplikaci `name` tak, aby přijímali hodnotu z typu <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`deklarace, nakonfigurujte [tokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) in :

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Nastavení aplikace

Soubor *appsettings.json* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT, která slouží k ověření přístupových tokenů.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>Řadič WeatherForecast

Kontroler WeatherForecast *(Controllers/WeatherForecastController.cs*) zpřístupňuje `[Authorize]` chráněné rozhraní API s atributem použitým na řadič. Je **důležité** si uvědomit, že:

* Atribut `[Authorize]` v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.
* Atribut `[Authorize]` použitý v Blazor aplikaci WebAssembly slouží pouze jako nápověda k aplikaci, že uživatel by měl být autorizován pro aplikaci pracovat správně.

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

*Tato část se týkajících se **klientské** aplikace řešení.*

### <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořena pro použití`SingleOrg`pracovních nebo školních účtů ( ), aplikace`Microsoft.Authentication.WebAssembly.Msal`automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ). Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.

Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.

Balíček `Microsoft.Authentication.WebAssembly.Msal` transitively `Microsoft.AspNetCore.Components.WebAssembly.Authentication` přidá balíček do aplikace.

### <a name="authentication-service-support"></a>Podpora ověřovací služby

Podpora pro ověřování uživatelů je registrována v `AddMsalAuthentication` kontejneru služby `Microsoft.Authentication.WebAssembly.Msal` s metodou rozšíření poskytované balíček. Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace. Hodnoty potřebné pro konfiguraci aplikace lze získat z konfigurace Azure Portal AAD při registraci aplikace.

### <a name="access-token-scopes"></a>Obory přístupových tokenů

Výchozí obory přístupových tokenů představují seznam oborů přístupových tokenů, které jsou:

* Ve výchozím nastavení zahrnuto do požadavku na přihlášení.
* Slouží k zřízení přístupového tokenu ihned po ověření.

Všechny obory musí patřit do stejné aplikace podle pravidel služby Azure Active Directory. Další obory lze přidat pro další aplikace rozhraní API podle potřeby:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Pokud portál Azure poskytuje identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku,** když obdrží *neoprávněnou odpověď 401* z rozhraní API, zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele. Portál Azure může například poskytovat jeden z následujících formátů identifikátoru URI oboru:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Zadej oblast URI oboru bez schématu a hostitele:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

### <a name="imports-file"></a>Importuje soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Komponenta LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Ověřovací komponenta

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Komponenta FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci z projektu Server. Při použití sady Visual Studio vyberte projekt serveru v **Průzkumníku řešení** a vyberte tlačítko **Spustit** na panelu nástrojů nebo spusťte aplikaci z nabídky **Ladění.**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* [Vyžádání dalších přístupových tokenů](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentace k platformě Microsoft Identity Platform](/azure/active-directory/develop/)
