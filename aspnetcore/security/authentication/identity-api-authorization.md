---
title: Seznámení s ověřováním pro jednostránkové aplikace v ASP.NET Core
author: javiercn
description: Používejte identitu s jednou stránkou, která je hostovaná v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/05/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: cb51df0267a5eabd4a2694727e9c896d0554265e
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583606"
---
# <a name="authentication-and-authorization-for-spas"></a>Ověřování a autorizace pro jednostránkové

ASP.NET Core 3,0 nebo novější nabízí ověřování v aplikacích s jednou stránkou (jednostránkové) pomocí podpory pro autorizaci rozhraní API. ASP.NET Coreá identita pro ověřování a ukládání uživatelů je kombinována s [IdentityServer](https://identityserver.io/) pro implementaci otevřeného ID Connect.

Parametr ověřování byl přidán do úhlových a **reagujících** šablon projektů, které se podobají parametru ověřování ve **webové aplikaci (Model-View-Controller)** (MVC) a **webové aplikaci** (Razor Pages). šablony projektů. Povolené hodnoty parametrů jsou **none** a **jednotlivce**. Šablona projektu **reagují. js a Redux** v tuto chvíli nepodporuje parametr ověřování.

## <a name="create-an-app-with-api-authorization-support"></a>Vytvoření aplikace s podporou autorizace rozhraní API

Ověřování a autorizaci uživatelů lze použít s použitím úhlů i reagujících jednostránkové. Otevřete příkazové okno a spusťte následující příkaz:

**Úhlová**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**Reakce**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

Předchozí příkaz vytvoří aplikaci ASP.NET Core s adresářem *clientapp* obsahujícím Spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Obecný popis ASP.NET Core komponent aplikace

Následující části popisují přidání do projektu, pokud je k dispozici podpora ověřování:

### <a name="startup-class"></a>Spouštěcí třída

`Startup` Třída má následující doplňky:

* `Startup.ConfigureServices` Uvnitř metody:
  * Identita s výchozím uživatelským rozhraním:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer s další `AddApiAuthorization` pomocnou metodou, která nastaví některé výchozí konvence ASP.NET Core v horní části IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování s další `AddIdentityServerJwt` pomocnou metodou, která nakonfiguruje aplikaci pro ověření tokenů JWT vyprodukovaných pomocí IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure` Uvnitř metody:
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

Tato pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování. Zásada je nakonfigurovaná tak, aby umožňovala identitě zpracovat všechny požadavky směrované na jakoukoli dílčí cestu v prostoru URL identity/identity. `JwtBearerHandler` Zpracovává všechny ostatní požadavky. Tato metoda navíc registruje `<<ApplicationName>>API` prostředek rozhraní API s IdentityServer s výchozím `<<ApplicationName>>API` rozsahem a konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

V souboru *Controllers\WeatherForecastController.cs* si všimněte `[Authorize]` atributu použitého pro třídu, která indikuje, že uživatel musí být autorizován na základě výchozích zásad pro přístup k prostředku. Výchozí zásady autorizace se budou konfigurovat tak, aby používaly výchozí schéma ověřování, které je nastavené na `AddIdentityServerJwt` základě výše zmíněného schématu zásad. `JwtBearerHandler` tím se nakonfiguruje Tato pomocná metoda jako výchozí obslužná rutina pro žádosti do aplikace

### <a name="applicationdbcontext"></a>ApplicationDbContext

V souboru *Data\ApplicationDbContext.cs* si všimněte `DbContext` , že se používá v identitě s výjimkou, kterou rozšiřuje `ApiAuthorizationDbContext` (další odvozená třída z `IdentityDbContext`) k zahrnutí schématu pro IdentityServer.

Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných tříd identity `DbContext` a nakonfigurovat kontext tak, aby zahrnoval schéma identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

V souboru *Controllers\OidcConfigurationController.cs* si všimněte koncového bodu, který se zřídí, aby sloužil parametrům OIDC, které musí klient použít.

### <a name="appsettingsjson"></a>appsettings.json

V souboru *appSettings. JSON* kořenového adresáře projektu je k dispozici nová `IdentityServer` část, která popisuje seznam konfigurovaných klientů. V následujícím příkladu je jeden klient. Název klienta odpovídá názvu aplikace a je mapován podle konvence na parametr OAuth `ClientId` . Profil indikuje typ aplikace, která se konfiguruje. Interně se používá k zajištění konvencí, které zjednodušují proces konfigurace serveru. K dispozici je několik profilů, jak je vysvětleno v části [profily aplikací](#application-profiles) .

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

V souboru *appSettings. Soubor Development. JSON* kořenového adresáře projektu obsahuje `IdentityServer` oddíl, který popisuje klíč použitý k podepisování tokenů. Při nasazování do produkčního prostředí se musí klíč zřídit a nasadit společně s aplikací, jak je vysvětleno v části [nasazení do produkčního](#deploy-to-production) prostředí.

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
  * *Login. Component. TS*: Zpracovává tok přihlášení aplikace.
  * *Odhlásit. Component. TS*: Zpracovává tok odhlášení aplikace.
  * *přihlášení – nabídka. Component. TS*: Widget, který zobrazuje jednu z následujících sad odkazů:
    * Správa profilů uživatelů a odhlášení odkazů při ověření uživatele
    * Registrace a přihlášení v odkazech, když se uživatel neověřuje
* Ochrana `AuthorizeGuard` trasy, kterou je možné přidat do tras a vyžaduje ověření uživatele před návštěvou trasy.
* Zachytávací `AuthorizeInterceptor` protokol HTTP, který připojuje přístupový token k odchozím požadavkům HTTP, které cílí na rozhraní API při ověření uživatele.
* Služba `AuthorizeService` , která zpracovává podrobné informace o procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.
* Úhlový modul, který definuje trasy přidružené k částem ověřování aplikace. Zpřístupňuje komponentu nabídky přihlášení, zachytávací modul, ochranu a službu pro využití ze zbytku aplikace.

## <a name="general-description-of-the-react-app"></a>Obecný popis aplikace s poreakcim

Podpora ověřování a autorizace rozhraní API v šabloně reakce se nachází v adresáři *ClientApp\src\components\api-Authorization* . Skládá se z následujících prvků:

* 4 součásti:
  * *Login.js*: Zpracovává tok přihlášení aplikace.
  * *Odhlašovací. js*: Zpracovává tok odhlášení aplikace.
  * *LoginMenu. js*: Widget, který zobrazuje jednu z následujících sad odkazů:
    * Správa profilů uživatelů a odhlášení odkazů při ověření uživatele
    * Registrace a přihlášení v odkazech, když se uživatel neověřuje
  * *AuthorizeRoute.js*: Komponenta směrování, která vyžaduje ověření uživatele před vykreslením součásti uvedené v `Component` parametru.
* Exportovaná `authService` instance třídy `AuthorizeService` , která zpracovává podrobnosti na nižší úrovni procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.

Teď, když jste viděli hlavní součásti řešení, se můžete podívat na podrobnější přehled o jednotlivých scénářích aplikace.

## <a name="require-authorization-on-a-new-api"></a>Vyžadovat autorizaci pro nové rozhraní API

Ve výchozím nastavení je systém nakonfigurován tak, aby snadno vyžadoval autorizaci pro nová rozhraní API. Provedete to tak, že vytvoříte nový kontroler `[Authorize]` a přidáte ho do třídy Controller nebo do jakékoli akce v rámci kontroleru.

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

Chránit směrování na straně klienta pomocí `AuthorizeRoute` komponenty místo prosté `Route` součásti. Všimněte si například, jak `fetch-data` je trasa nakonfigurovaná `App` v rámci součásti:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Ochrana trasy:

* Nechrání skutečný koncový bod (který pořád vyžaduje `[Authorize]` Aplikování atributu).
* Zabrání uživateli v přechodu na danou trasu na straně klienta, pokud není ověřen.

## <a name="authenticate-api-requests-react"></a>Ověřit požadavky rozhraní API (reagovat)

Ověřování požadavků pomocí reakce je provedeno napřed importem `authService` instance `AuthorizeService`z. Přístupový token je načten z `authService` a je připojen k žádosti, jak je uvedeno níže. V reakci na komponenty se tato práce obvykle provádí v `componentDidMount` metodě životního cyklu nebo jako výsledek z nějaké interakce s uživatelem.

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

### <a name="example-deploy-to-azure-websites"></a>Příklad: Nasazení na Azure websites

Tato část popisuje nasazení aplikace do Azure websites pomocí certifikátu uloženého v úložišti certifikátů. Aby bylo možné aplikaci upravit, aby načetla certifikát z úložiště certifikátů, musí být plán App Service při konfiguraci v pozdějším kroku přinejmenším na úrovni Standard. V souboru *appSettings. JSON* aplikace upravte `IdentityServer` oddíl tak, aby obsahoval podrobnosti klíče:

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
* Umístění úložiště představuje místo, kde se má certifikát načíst (`CurrentUser` nebo `LocalMachine`).
* Název úložiště představuje název úložiště certifikátů, ve kterém je certifikát uložený. V tomto případě odkazuje na úložiště osobních uživatelů.

Pokud chcete nasadit na Azure websites, nasaďte aplikaci podle kroků v části [nasazení aplikace do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) a vytvořte potřebné prostředky Azure a nasaďte aplikaci do produkčního prostředí.

Po provedení kroků uvedených v předchozích pokynech se aplikace nasadí do Azure, ale ještě není funkční. Certifikát používaný aplikací je stále potřeba nastavit. Vyhledejte kryptografický otisk certifikátu, který se má použít, a postupujte podle kroků popsaných v tématu [načtení certifikátů](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

I když tento postup uvádí protokol SSL, existuje část **privátní certifikáty** na portálu, kde můžete nahrát zřízený certifikát pro použití s aplikací.

Po provedení tohoto kroku restartujte aplikaci a měla by být funkční.

## <a name="other-configuration-options"></a>Další možnosti konfigurace

Podpora pro autorizaci rozhraní API se sestavuje na IdentityServer se sadou konvencí, výchozími hodnotami a vylepšeními, které zjednodušují prostředí jednostránkové. Bez nutnosti vyslovit, pokud ASP.NET Core integrace nepokrývá váš scénář, je k dispozici plný výkon IdentityServer na pozadí. Podpora ASP.NET Core se zaměřuje na aplikace "první strany", kde se všechny aplikace vytvářejí a nasazují v naší organizaci. V takovém případě se podpora nenabízí pro věci, jako je například souhlas nebo federace. V případě těchto scénářů použijte IdentityServer a postupujte podle jejich dokumentace.

### <a name="application-profiles"></a>Profily aplikací

Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále definují jejich parametry. V současné době jsou podporovány následující profily:

* `IdentityServerSPA`: Představuje zabezpečené ověřování na hostitele společně s IdentityServer jako s jednou jednotkou.
  * Výchozí hodnota je `redirect_uri`. `/authentication/login-callback`
  * Výchozí hodnota je `post_logout_redirect_uri`. `/authentication/logout-callback`
  * Sada oborů zahrnuje `openid`, `profile`a každý obor definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů OIDC odezvy je `id_token token` nebo každou z nich jednotlivě (`id_token`, `token`).
  * Režim povolených odpovědí je `fragment`.
* `SPA`: Představuje zabezpečené ověřování hesla, které není hostované na IdentityServer.
  * Sada oborů zahrnuje `openid`, `profile`a každý obor definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů OIDC odezvy je `id_token token` nebo každou z nich jednotlivě (`id_token`, `token`).
  * Režim povolených odpovědí je `fragment`.
* `IdentityServerJwt`: Představuje rozhraní API, které je hostované spolu s IdentityServer.
  * Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.
* `API`: Představuje rozhraní API, které není hostované pomocí IdentityServer.
  * Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.

### <a name="configuration-through-appsettings"></a>Konfigurace prostřednictvím AppSettings

Nakonfigurujte aplikace přes konfigurační systém jejich přidáním do seznamu `Clients` nebo. `Resources`

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

## <a name="additional-resources"></a>Další zdroje

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
