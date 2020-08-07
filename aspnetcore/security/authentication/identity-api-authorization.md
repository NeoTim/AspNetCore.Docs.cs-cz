---
title: Seznámení s ověřováním pro jednostránkové aplikace v ASP.NET Core
author: javiercn
description: Používejte Identity s jednou stránkou, která je hostovaná v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: c06f1d4bf772d7726d19163fcdee8c92d4006cd2
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819110"
---
# <a name="authentication-and-authorization-for-spas"></a>Ověřování a autorizace pro jednostránkové

ASP.NET Core 3,0 nebo novější nabízí ověřování v aplikacích s jednou stránkou (jednostránkové) pomocí podpory pro autorizaci rozhraní API. ASP.NET Core Identity pro ověřování a ukládání uživatelů se zkombinuje se [ Identity serverem](https://identityserver.io/) pro implementaci OpenID Connect.

Parametr ověřování byl přidán do **úhlových** a **reagujících** šablon projektů, které se podobají parametru ověřování v šablonách projektů **webové aplikace (model-zobrazení-kontroler)** a **webové aplikace** ( Razor stránky). Povolené hodnoty parametrů jsou **none** a **jednotlivce**. Šablona projektu **React.js a Redux** v tuto chvíli nepodporuje parametr ověřování.

## <a name="create-an-app-with-api-authorization-support"></a>Vytvoření aplikace s podporou autorizace rozhraní API

Ověřování a autorizaci uživatelů lze použít s použitím úhlů i reagujících jednostránkové. Otevřete příkazové prostředí a spusťte následující příkaz:

**Úhlová**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Reakce**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Předchozí příkaz vytvoří aplikaci ASP.NET Core s adresářem *clientapp* obsahujícím Spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Obecný popis ASP.NET Core komponent aplikace

Následující části popisují přidání do projektu, pokud je k dispozici podpora ověřování:

### <a name="startup-class"></a>Spouštěcí třída

Následující příklady kódu se spoléhají na [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer)Balíček NuGet serveru. Příklady konfigurují ověřování rozhraní API a autorizaci pomocí <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> metod rozšíření a. Projekty používající šablony projektů s použitím reakce nebo úhlové SPA s ověřováním zahrnují odkaz na tento balíček.

`Startup`Třída má následující doplňky:

* Uvnitř `Startup.ConfigureServices` metody:
  * Identitys výchozím uživatelským rozhraním:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer s další `AddApiAuthorization` pomocnou metodou, která nastaví některé výchozí konvence ASP.NET Core na Identity serveru:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování s další `AddIdentityServerJwt` pomocnou metodou, která nakonfiguruje aplikaci k ověření tokenů JWT vyprodukovaných Identity serverem:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Uvnitř `Startup.Configure` metody:
  * Middleware ověřování, který zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:

    ```csharp
    app.UseAuthentication();
    ```

  * IdentityMiddleware serveru, který zpřístupňuje koncové body OpenID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Tato pomocná metoda nakonfiguruje Identity Server tak, aby používal naši podporovanou konfiguraci. IdentityServer je výkonné a rozšiřitelné rozhraní pro zpracování otázek zabezpečení aplikací. Ve stejnou chvíli zveřejňuje nepotřebnou složitost v nejběžnějších scénářích. V důsledku toho jsou k dispozici sady konvencí a možností konfigurace, které jsou považovány za dobrý výchozí bod. Jakmile se vaše požadavky na ověření změní, je k Identity dispozici kompletní výkon serveru, aby bylo možné přizpůsobit ověřování tak, aby vyhovovalo vašim potřebám.

### <a name="addno-locidentityserverjwt"></a>Přidat Identity ServerJwt

Tato pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování. Zásady jsou nakonfigurované tak, aby pomohly Identity zpracovávat všechny požadavky směrované na jakoukoli dílčí cestu v Identity prostoru URL (/ Identity ). `JwtBearerHandler`Zpracovává všechny ostatní požadavky. Tato metoda navíc registruje `<<ApplicationName>>API` prostředek rozhraní API se Identity serverem s výchozím rozsahem `<<ApplicationName>>API` a konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných Identity serverem pro aplikaci.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

V souboru *Controllers\WeatherForecastController.cs* si všimněte `[Authorize]` atributu použitého pro třídu, která indikuje, že uživatel musí být autorizován na základě výchozích zásad pro přístup k prostředku. Výchozí zásady autorizace se budou konfigurovat tak, aby používaly výchozí schéma ověřování, které je nastavené `AddIdentityServerJwt` na základě výše zmíněného schématu zásad, což nastaví `JwtBearerHandler` jako výchozí obslužnou rutinu pro požadavky na aplikaci.

### <a name="applicationdbcontext"></a>ApplicationDbContext

V souboru *Data\ApplicationDbContext.cs* si všimněte, že se `DbContext` používá v Identity s výjimkou, kterou rozšiřuje `ApiAuthorizationDbContext` (další odvozená třída z `IdentityDbContext` ) k zahrnutí schématu pro Identity Server.

Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity `DbContext` tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

V souboru *Controllers\OidcConfigurationController.cs* si všimněte koncového bodu, který se zřídí, aby sloužil parametrům OIDC, které musí klient použít.

### <a name="appsettingsjson"></a>appsettings.jsna

V *appsettings.jssouboru v* kořenovém adresáři projektu je k dispozici nová `IdentityServer` část, která popisuje seznam konfigurovaných klientů. V následujícím příkladu je jeden klient. Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth. Profil indikuje typ aplikace, která se konfiguruje. Interně se používá k zajištění konvencí, které zjednodušují proces konfigurace serveru. K dispozici je několik profilů, jak je vysvětleno v části [profily aplikací](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.jsna

V *appsettings.Development.jssouboru v* kořenovém adresáři projektu je k dispozici `IdentityServer` oddíl, který popisuje klíč použitý k podepisování tokenů. Při nasazování do produkčního prostředí se musí klíč zřídit a nasadit společně s aplikací, jak je vysvětleno v části [nasazení do produkčního](#deploy-to-production) prostředí.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Obecný popis aplikace z úhlů

Podpora ověřování a autorizace rozhraní API v úhlové šabloně se nachází v jeho vlastním úhlovém modulu v adresáři *ClientApp\src\api-Authorization* . Modul se skládá z následujících prvků:

* 3 součásti:
  * *Login. Component. TS*: zpracovává tok přihlášení aplikace.
  * *logout. Component. TS*: zpracovává tok odhlášení aplikace.
  * *Login-menu. Component. TS*: widget, který zobrazuje jednu z následujících sad odkazů:
    * Správa profilů uživatelů a odhlášení odkazů při ověření uživatele
    * Registrace a přihlášení v odkazech, když se uživatel neověřuje
* Ochrana trasy `AuthorizeGuard` , kterou je možné přidat do tras a vyžaduje ověření uživatele před návštěvou trasy.
* Zachytávací protokol HTTP `AuthorizeInterceptor` , který připojuje přístupový token k odchozím požadavkům HTTP, které cílí na rozhraní API při ověření uživatele.
* Služba `AuthorizeService` , která zpracovává podrobné informace o procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.
* Úhlový modul, který definuje trasy přidružené k částem ověřování aplikace. Zpřístupňuje komponentu nabídky přihlášení, zachytávací modul, ochranu a službu pro využití ze zbytku aplikace.

## <a name="general-description-of-the-react-app"></a>Obecný popis aplikace s poreakcim

Podpora ověřování a autorizace rozhraní API v šabloně reakce se nachází v adresáři *ClientApp\src\components\api-Authorization* . Skládá se z následujících prvků:

* 4 součásti:
  * *Login.js*: zpracovává tok přihlášení aplikace.
  * *Logout.js*: zpracovává tok odhlášení aplikace.
  * *LoginMenu.js*: widget, který zobrazuje jednu z následujících sad odkazů:
    * Správa profilů uživatelů a odhlášení odkazů při ověření uživatele
    * Registrace a přihlášení v odkazech, když se uživatel neověřuje
  * *AuthorizeRoute.js*: komponenta směrování, která vyžaduje ověření uživatele před vykreslením součásti uvedené v `Component` parametru.
* Exportovaná `authService` instance třídy `AuthorizeService` , která zpracovává podrobnosti na nižší úrovni procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.

Teď, když jste viděli hlavní součásti řešení, se můžete podívat na podrobnější přehled o jednotlivých scénářích aplikace.

## <a name="require-authorization-on-a-new-api"></a>Vyžadovat autorizaci pro nové rozhraní API

Ve výchozím nastavení je systém nakonfigurován tak, aby snadno vyžadoval autorizaci pro nová rozhraní API. Provedete to tak, že vytvoříte nový kontroler a přidáte `[Authorize]` ho do třídy Controller nebo do jakékoli akce v rámci kontroleru.

## <a name="customize-the-api-authentication-handler"></a>Přizpůsobení obslužné rutiny ověřování rozhraní API

Chcete-li přizpůsobit konfiguraci obslužné rutiny JWT rozhraní API, nakonfigurujte její <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instanci:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Obslužná rutina tokenu JWT rozhraní API vyvolává události, které umožňují řízení procesu ověřování pomocí `JwtBearerEvents` . Pro zajištění podpory pro autorizaci rozhraní API `AddIdentityServerJwt` Zaregistrujte vlastní obslužné rutiny událostí.

Chcete-li přizpůsobit zpracování události, zabalte existující obslužnou rutinu události s další logikou podle potřeby. Příklad:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

V předchozím kódu `OnTokenValidated` je obslužná rutina události nahrazena vlastní implementací. Tato implementace:

1. Volá původní implementaci poskytovanou podporou autorizace rozhraní API.
1. Spusťte vlastní logiku.

## <a name="protect-a-client-side-route-angular"></a>Ochrana trasy na straně klienta (úhlové)

Ochrana směrování na straně klienta se provádí přidáním ochrany autorizace do seznamu ochranných zařízení, která se spustí při konfiguraci trasy. Jako příklad můžete vidět, jak `fetch-data` je trasa nakonfigurovaná v rámci modulu Main v hlavní aplikaci:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Je důležité si zmínit, že při ochraně trasy se nechrání skutečný koncový bod (který stále vyžaduje `[Authorize]` použití atributu), ale to brání uživateli v přechodu na danou trasu na straně klienta, pokud není ověřen.

## <a name="authenticate-api-requests-angular"></a>Ověřit požadavky rozhraní API (úhlové)

Ověřování požadavků na rozhraní API hostovaná společně s aplikací se provádí automaticky pomocí zachytávací klienta HTTP definovaného aplikací.

## <a name="protect-a-client-side-route-react"></a>Ochrana trasy na straně klienta (reakce)

Chránit směrování na straně klienta pomocí `AuthorizeRoute` komponenty místo prosté `Route` součásti. Všimněte si například, jak `fetch-data` je trasa nakonfigurovaná v rámci `App` součásti:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Ochrana trasy:

* Nechrání skutečný koncový bod (který pořád vyžaduje `[Authorize]` Aplikování atributu).
* Zabrání uživateli v přechodu na danou trasu na straně klienta, pokud není ověřen.

## <a name="authenticate-api-requests-react"></a>Ověřit požadavky rozhraní API (reagovat)

Ověřování požadavků pomocí reakce je provedeno napřed importem `authService` instance z `AuthorizeService` . Přístupový token je načten z `authService` a je připojen k žádosti, jak je uvedeno níže. V reakci na komponenty se tato práce obvykle provádí v `componentDidMount` metodě životního cyklu nebo jako výsledek z nějaké interakce s uživatelem.

### <a name="import-the-authservice-into-your-component"></a>Import authService do komponenty

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Načtení a připojení přístupového tokenu k odpovědi

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Nasazení do produkčního prostředí

Pokud chcete nasadit aplikaci do produkčního prostředí, je potřeba zřídit tyto prostředky:

* Databáze pro ukládání Identity uživatelských účtů a Identity udělení oprávnění serveru.
* Provozní certifikát, který se má použít pro podepisování tokenů.
  * Pro tento certifikát neexistují žádné zvláštní požadavky. může se jednat o certifikát podepsaný svým držitelem nebo certifikát zřízený autoritou certifikační autority.
  * Dá se vygenerovat pomocí standardních nástrojů, jako je PowerShell nebo OpenSSL.
  * Dá se nainstalovat do úložiště certifikátů na cílových počítačích nebo nasadit jako soubor *. pfx* se silným heslem.

### <a name="example-deploy-to-azure-app-service"></a>Příklad: nasazení na Azure App Service

Tato část popisuje nasazení aplikace, aby Azure App Service pomocí certifikátu uloženého v úložišti certifikátů. Pokud chcete aplikaci upravit tak, aby načetla certifikát z úložiště certifikátů, při konfiguraci aplikace v Azure Portal v pozdějším kroku se vyžaduje plán Service úrovně Standard nebo vyšší.

V *appsettings.jsaplikace v* souboru upravte `IdentityServer` část tak, aby obsahovala podrobnosti klíče:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Název úložiště představuje název úložiště certifikátů, ve kterém je certifikát uložený. V tomto případě odkazuje na úložiště osobních uživatelů.
* Umístění úložiště představuje místo, kde se má certifikát načíst ( `CurrentUser` nebo `LocalMachine` ).
* Vlastnost Name u certifikátu odpovídá rozlišujícímu předmětu certifikátu.

Pokud chcete nasadit Azure App Service, postupujte podle kroků v části [nasazení aplikace do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), která vysvětluje, jak vytvořit potřebné prostředky Azure a nasadit aplikaci do produkčního prostředí.

Po provedení kroků uvedených v předchozích pokynech se aplikace nasadí do Azure, ale ještě není funkční. Certifikát používaný aplikací musí být nakonfigurovaný v Azure Portal. Vyhledejte kryptografický otisk certifikátu a postupujte podle kroků popsaných v tématu [načtení certifikátů](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

I když tento postup uvádí protokol SSL, existuje část **privátní certifikáty** v Azure Portal, kde můžete nahrát zřízený certifikát pro použití s aplikací.

Po nakonfigurování aplikace a nastavení aplikace v Azure Portal restartujte aplikaci na portálu.

## <a name="other-configuration-options"></a>Další možnosti konfigurace

Podpora pro autorizaci rozhraní API je založena na Identity serveru se sadou konvencí, výchozími hodnotami a vylepšeními, které zjednodušují prostředí jednostránkové. Bez nutnosti vyslovit, Identity pokud ASP.NET Core integrace nepokrývá váš scénář, je kompletní síla serveru dostupná na pozadí. Podpora ASP.NET Core se zaměřuje na aplikace "první strany", kde se všechny aplikace vytvářejí a nasazují v naší organizaci. V takovém případě se podpora nenabízí pro věci, jako je například souhlas nebo federace. Pro tyto scénáře použijte Identity Server a postupujte podle jejich dokumentace.

### <a name="application-profiles"></a>Profily aplikací

Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále definují jejich parametry. V současné době jsou podporovány následující profily:

* `IdentityServerSPA`: Představuje zabezpečené servery hostované společně se Identity serverem jako jediná jednotka.
  * `redirect_uri`Výchozí hodnota je `/authentication/login-callback` .
  * `post_logout_redirect_uri`Výchozí hodnota je `/authentication/logout-callback` .
  * Sada oborů zahrnuje `openid` , `profile` a každý obor definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů OIDC odezvy je `id_token token` nebo každou z nich jednotlivě ( `id_token` , `token` ).
  * Režim povolených odpovědí je `fragment` .
* `SPA`: Představuje zabezpečené zabezpečené ověřování, které není hostované na Identity serveru.
  * Sada oborů zahrnuje `openid` , `profile` a každý obor definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů OIDC odezvy je `id_token token` nebo každou z nich jednotlivě ( `id_token` , `token` ).
  * Režim povolených odpovědí je `fragment` .
* `IdentityServerJwt`: Představuje rozhraní API, které je hostované společně se Identity serverem.
  * Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.
* `API`: Představuje rozhraní API, které není hostované na Identity serveru.
  * Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.

### <a name="configuration-through-appsettings"></a>Konfigurace prostřednictvím AppSettings

Nakonfigurujte aplikace přes konfigurační systém jejich přidáním do seznamu `Clients` nebo `Resources` .

Nakonfigurujte jednotlivé vlastnosti klienta `redirect_uri` a `post_logout_redirect_uri` , jak je znázorněno v následujícím příkladu:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Při konfiguraci prostředků můžete nakonfigurovat obory pro prostředek, jak je znázorněno níže:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Konfigurace prostřednictvím kódu

Můžete také nakonfigurovat klienty a prostředky prostřednictvím kódu pomocí přetížení `AddApiAuthorization` , které provede akci konfigurace možností.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Další materiály

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
