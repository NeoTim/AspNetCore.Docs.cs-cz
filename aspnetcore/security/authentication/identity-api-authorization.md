---
title: Seznámení s ověřováním pro jednostránkové aplikace v ASP.NET Core
author: javiercn
description: Používejte identitu s jednou stránkou, která je hostovaná v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 31a5e47d772e7416646c4d83c3209d7d2b254199
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829163"
---
# <a name="authentication-and-authorization-for-spas"></a>Ověřování a autorizace pro jednostránkové

ASP.NET Core 3,0 nebo novější nabízí ověřování v aplikacích s jednou stránkou (jednostránkové) pomocí podpory pro autorizaci rozhraní API. ASP.NET Coreá identita pro ověřování a ukládání uživatelů je kombinována s [IdentityServer](https://identityserver.io/) pro implementaci otevřeného ID Connect.

Parametr ověřování byl přidán do **úhlových** a **reagujících** šablon projektů, které se podobají parametru ověřování v šablonách projektů **webové aplikace (model-zobrazení-kontroler)** a **webové aplikace** (Razor Pages). Povolené hodnoty parametrů jsou **none** a **jednotlivce**. Šablona projektu **reagují. js a Redux** v tuto chvíli nepodporuje parametr ověřování.

## <a name="create-an-app-with-api-authorization-support"></a>Vytvoření aplikace s podporou autorizace rozhraní API

Ověřování a autorizaci uživatelů lze použít s použitím úhlů i reagujících jednostránkové. Otevřete příkazové okno a spusťte následující příkaz:

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

Třída `Startup` obsahuje následující doplňky:

* Uvnitř metody `Startup.ConfigureServices`:
  * Identita s výchozím uživatelským rozhraním:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer se další pomocnou metodou `AddApiAuthorization`, která nastaví některé výchozí konvence ASP.NET Core na IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování s dodatečnou podpůrnou metodou `AddIdentityServerJwt`, která nakonfiguruje aplikaci tak, aby ověřovala tokeny JWT vytvořené pomocí IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Uvnitř metody `Startup.Configure`:
  * Middleware ověřování, který zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:

    ```csharp
    app.UseAuthentication();
    ```

  * Middleware IdentityServer, který zpřístupňuje koncové body připojení Open ID:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Tato pomocná metoda nakonfiguruje IdentityServer, aby používala naši podporovanou konfiguraci. IdentityServer je výkonná a rozšiřitelná architektura pro zpracování otázek zabezpečení aplikací. Ve stejnou chvíli zveřejňuje nepotřebnou složitost v nejběžnějších scénářích. V důsledku toho jsou k dispozici sady konvencí a možností konfigurace, které jsou považovány za dobrý výchozí bod. Jakmile se vaše potřeby ověřování změní, je stále k dispozici kompletní výkon IdentityServer, abyste mohli přizpůsobit ověřování podle vašich potřeb.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Tato pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování. Zásada je nakonfigurovaná tak, aby umožňovala identitě zpracovat všechny požadavky směrované na jakoukoli dílčí cestu v prostoru URL identity/identity. `JwtBearerHandler` zpracovává všechny ostatní požadavky. Kromě toho tato metoda registruje prostředek `<<ApplicationName>>API` API s IdentityServer s výchozím rozsahem `<<ApplicationName>>API` a nakonfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

V souboru *Controllers\WeatherForecastController.cs* si všimněte, že atribut `[Authorize]` aplikovaný na třídu, která indikuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku. Výchozí zásady autorizace se budou konfigurovat tak, aby používaly výchozí schéma ověřování, které je nastavené `AddIdentityServerJwt` na výše zmíněné schéma zásad. `JwtBearerHandler` tuto pomocnou metodu nakonfigurovali jako výchozí obslužnou rutinu pro požadavky na aplikaci.

### <a name="applicationdbcontext"></a>ApplicationDbContext

V souboru *Data\ApplicationDbContext.cs* si všimněte, že se v identitě používá stejná `DbContext`, s výjimkou, kterou rozšiřuje `ApiAuthorizationDbContext` (další odvozená třída z `IdentityDbContext`) pro zahrnutí schématu pro IdentityServer.

Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných tříd identity `DbContext` a nakonfigurujte kontext tak, aby zahrnoval schéma identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` na metodě `OnModelCreating`.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

V souboru *Controllers\OidcConfigurationController.cs* si všimněte koncového bodu, který se zřídí, aby sloužil parametrům OIDC, které musí klient použít.

### <a name="appsettingsjson"></a>appsettings.json

V souboru *appSettings. JSON* kořenového adresáře projektu je k dispozici nový `IdentityServer` oddíl, který popisuje seznam konfigurovaných klientů. V následujícím příkladu je jeden klient. Název klienta odpovídá názvu aplikace a je mapován podle konvence na parametr `ClientId` OAuth. Profil indikuje typ aplikace, která se konfiguruje. Interně se používá k zajištění konvencí, které zjednodušují proces konfigurace serveru. K dispozici je několik profilů, jak je vysvětleno v části [profily aplikací](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appSettings. Vývoj. JSON

V souboru *appSettings. Soubor Development. JSON* kořenového adresáře projektu obsahuje oddíl `IdentityServer`, který popisuje klíč použitý k podepisování tokenů. Při nasazování do produkčního prostředí se musí klíč zřídit a nasadit společně s aplikací, jak je vysvětleno v části [nasazení do produkčního](#deploy-to-production) prostředí.

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
* `AuthorizeGuard` přesměrování trasy, které je možné přidat do tras a vyžaduje, aby byl uživatel ověřený před návštěvou trasy.
* Zachytávací protokol HTTP `AuthorizeInterceptor`, který připojuje přístupový token k odchozím požadavkům HTTP, které cílí na rozhraní API při ověření uživatele.
* `AuthorizeService` služby, která zpracovává podrobné informace o procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.
* Úhlový modul, který definuje trasy přidružené k částem ověřování aplikace. Zpřístupňuje komponentu nabídky přihlášení, zachytávací modul, ochranu a službu pro využití ze zbytku aplikace.

## <a name="general-description-of-the-react-app"></a>Obecný popis aplikace s poreakcim

Podpora ověřování a autorizace rozhraní API v šabloně reakce se nachází v adresáři *ClientApp\src\components\api-Authorization* . Skládá se z následujících prvků:

* 4 součásti:
  * *Login. js*: zpracovává tok přihlášení aplikace.
  * *Odhlašovací. js*: zpracovává tok odhlašování aplikace.
  * *LoginMenu. js*: widget, který zobrazuje jednu z následujících sad odkazů:
    * Správa profilů uživatelů a odhlášení odkazů při ověření uživatele
    * Registrace a přihlášení v odkazech, když se uživatel neověřuje
  * *AuthorizeRoute. js*: komponenta směrování, která vyžaduje ověření uživatele před vykreslením součásti uvedené v parametru `Component`.
* Exportovaná instance `authService` třídy `AuthorizeService`, která zpracovává podrobné informace o procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.

Teď, když jste viděli hlavní součásti řešení, se můžete podívat na podrobnější přehled o jednotlivých scénářích aplikace.

## <a name="require-authorization-on-a-new-api"></a>Vyžadovat autorizaci pro nové rozhraní API

Ve výchozím nastavení je systém nakonfigurován tak, aby snadno vyžadoval autorizaci pro nová rozhraní API. Provedete to tak, že vytvoříte nový kontroler a přidáte atribut `[Authorize]` do třídy Controller nebo do jakékoli akce v rámci kontroleru.

## <a name="customize-the-api-authentication-handler"></a>Přizpůsobení obslužné rutiny ověřování rozhraní API

Pokud chcete přizpůsobit konfiguraci obslužné rutiny JWT rozhraní API, nakonfigurujte jeho instanci <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>:

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

Obslužná rutina JWT rozhraní API vyvolává události, které umožňují řízení procesu ověřování pomocí `JwtBearerEvents`. Pro zajištění podpory pro autorizaci rozhraní API `AddIdentityServerJwt` registrovat vlastní obslužné rutiny událostí.

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

V předchozím kódu je obslužná rutina události `OnTokenValidated` nahrazena vlastní implementací. Tato implementace:

1. Volá původní implementaci poskytovanou podporou autorizace rozhraní API.
1. Spusťte vlastní logiku.

## <a name="protect-a-client-side-route-angular"></a>Ochrana trasy na straně klienta (úhlové)

Ochrana směrování na straně klienta se provádí přidáním ochrany autorizace do seznamu ochranných zařízení, která se spustí při konfiguraci trasy. Jako příklad si můžete prohlédnout, jak je trasa `fetch-data` nakonfigurovaná v rámci hlavního úhlu aplikace:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Je důležité si zmínit, že při ochraně trasy se nechrání skutečný koncový bod (který pořád vyžaduje použití atributu `[Authorize]`), ale zabrání uživateli v přechodu na příslušnou trasu na straně klienta, pokud není ověřena.

## <a name="authenticate-api-requests-angular"></a>Ověřit požadavky rozhraní API (úhlové)

Ověřování požadavků na rozhraní API hostovaná společně s aplikací se provádí automaticky pomocí zachytávací klienta HTTP definovaného aplikací.

## <a name="protect-a-client-side-route-react"></a>Ochrana trasy na straně klienta (reakce)

Chránit směrování na straně klienta pomocí `AuthorizeRoute` komponenty místo prosté `Route` součásti. Všimněte si například, jak je trasa `fetch-data` nakonfigurovaná v rámci `App` součásti:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Ochrana trasy:

* Nechrání skutečný koncový bod (který pořád vyžaduje použití atributu `[Authorize]`).
* Zabrání uživateli v přechodu na danou trasu na straně klienta, pokud není ověřen.

## <a name="authenticate-api-requests-react"></a>Ověřit požadavky rozhraní API (reagovat)

Ověřování požadavků pomocí reakce je provedeno napřed importem instance `authService` z `AuthorizeService`. Přístupový token se načte z `authService` a je připojený k žádosti, jak je znázorněno níže. V reakci na komponenty se tato práce obvykle provádí v metodě životního cyklu `componentDidMount` nebo jako výsledek z nějaké interakce s uživatelem.

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

* Databáze pro ukládání uživatelských účtů identity a IdentityServer grantů.
* Provozní certifikát, který se má použít pro podepisování tokenů.
  * Pro tento certifikát neexistují žádné zvláštní požadavky. může se jednat o certifikát podepsaný svým držitelem nebo certifikát zřízený autoritou certifikační autority.
  * Dá se vygenerovat pomocí standardních nástrojů, jako je PowerShell nebo OpenSSL.
  * Dá se nainstalovat do úložiště certifikátů na cílových počítačích nebo nasadit jako soubor *. pfx* se silným heslem.

### <a name="example-deploy-to-azure-websites"></a>Příklad: nasazení na Azure websites

Tato část popisuje nasazení aplikace do Azure websites pomocí certifikátu uloženého v úložišti certifikátů. Aby bylo možné aplikaci upravit, aby načetla certifikát z úložiště certifikátů, musí být plán App Service při konfiguraci v pozdějším kroku přinejmenším na úrovni Standard. V souboru *appSettings. JSON* aplikace upravte část `IdentityServer` tak, aby obsahovala podrobné informace o klíči:

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

* Vlastnost Name u certifikátu odpovídá rozlišujícímu předmětu certifikátu.
* Umístění úložiště představuje místo, odkud se má certifikát načíst (`CurrentUser` nebo `LocalMachine`).
* Název úložiště představuje název úložiště certifikátů, ve kterém je certifikát uložený. V tomto případě odkazuje na úložiště osobních uživatelů.

Pokud chcete nasadit na Azure websites, nasaďte aplikaci podle kroků v části [nasazení aplikace do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) a vytvořte potřebné prostředky Azure a nasaďte aplikaci do produkčního prostředí.

Po provedení kroků uvedených v předchozích pokynech se aplikace nasadí do Azure, ale ještě není funkční. Certifikát používaný aplikací je stále potřeba nastavit. Vyhledejte kryptografický otisk certifikátu, který se má použít, a postupujte podle kroků popsaných v tématu [načtení certifikátů](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

I když tento postup uvádí protokol SSL, existuje část **privátní certifikáty** na portálu, kde můžete nahrát zřízený certifikát pro použití s aplikací.

Po provedení tohoto kroku restartujte aplikaci a měla by být funkční.

## <a name="other-configuration-options"></a>Další možnosti konfigurace

Podpora pro autorizaci rozhraní API se sestavuje na IdentityServer se sadou konvencí, výchozími hodnotami a vylepšeními, které zjednodušují prostředí jednostránkové. Bez nutnosti vyslovit, pokud ASP.NET Core integrace nepokrývá váš scénář, je k dispozici plný výkon IdentityServer na pozadí. Podpora ASP.NET Core se zaměřuje na aplikace "první strany", kde se všechny aplikace vytvářejí a nasazují v naší organizaci. V takovém případě se podpora nenabízí pro věci, jako je například souhlas nebo federace. V případě těchto scénářů použijte IdentityServer a postupujte podle jejich dokumentace.

### <a name="application-profiles"></a>Profily aplikací

Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále definují jejich parametry. V současné době jsou podporovány následující profily:

* `IdentityServerSPA`: představuje zabezpečené ověřování na hostitele společně IdentityServer jako jednu jednotku.
  * Výchozí hodnota `redirect_uri` `/authentication/login-callback`.
  * Výchozí hodnota `post_logout_redirect_uri` `/authentication/logout-callback`.
  * Sada oborů zahrnuje `openid`, `profile`a všechny obory definované pro rozhraní API v aplikaci.
  * Sada povolených typů OIDC odezvy je `id_token token` nebo každou jednotlivě (`id_token`, `token`).
  * Režim povolených odpovědí je `fragment`.
* `SPA`: představuje zabezpečené zabezpečené ověřování, které není hostované v IdentityServer.
  * Sada oborů zahrnuje `openid`, `profile`a všechny obory definované pro rozhraní API v aplikaci.
  * Sada povolených typů OIDC odezvy je `id_token token` nebo každou jednotlivě (`id_token`, `token`).
  * Režim povolených odpovědí je `fragment`.
* `IdentityServerJwt`: představuje rozhraní API, které je hostované spolu s IdentityServer.
  * Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.
* `API`: představuje rozhraní API, které není hostované pomocí IdentityServer.
  * Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.

### <a name="configuration-through-appsettings"></a>Konfigurace prostřednictvím AppSettings

Nakonfigurujte aplikace přes konfigurační systém jejich přidáním do seznamu `Clients` nebo `Resources`.

Nakonfigurujte vlastnost `redirect_uri` a `post_logout_redirect_uri` každého klienta, jak je znázorněno v následujícím příkladu:

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

Můžete také nakonfigurovat klienty a prostředky prostřednictvím kódu pomocí přetížení `AddApiAuthorization`, které provádí akci konfigurace možností.

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

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
