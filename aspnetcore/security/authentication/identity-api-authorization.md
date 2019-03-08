---
title: Úvod k ověřování pro jednu stránku aplikace v ASP.NET Core
author: javiercn
description: Použít identitu s jedinou stránku aplikaci hostované uvnitř aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 4afc9ac0a3c54b452c6a1b23e4de31d7e2fc5284
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665334"
---
# <a name="authentication-and-authorization-for-spas"></a>Ověřování a autorizace pro SPA

ASP.NET Core 3.0 nebo novější nabízí ověřování v jedné stránky aplikace (SPA) pomocí podpory pro povolení rozhraní API. ASP.NET Core Identity pro ověřování a ukládání uživatelé číslo zkombinuje s [IdentityServer](https://identityserver.io/) pro implementaci Open ID Connect.

Parametr ověřování byl přidán do **Angular** a **React** šablony, které je podobné tomuto: Parametr ověřování v projektu **webové aplikace (Model-View-Controller)**  (MVC) a **webovou aplikaci** šablony projektů (stránky Razor). Parametr povolené hodnoty jsou **žádný** a **jednotlivé**. **React.js a Reduxem** šablonu projektu nepodporuje parametr ověřování v tuto chvíli.

## <a name="create-an-app-with-api-authorization-support"></a>Vytvoření aplikace s podporou ověřování API

Ověřování a autorizace uživatelů je možné s Angular a SPA React. Otevřete příkazové okno a spusťte následující příkaz:

**Angular**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**React**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

Předchozí příkaz vytvoří aplikace ASP.NET Core s *ClientApp* adresáře, který obsahuje tato jednostránková aplikace.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Obecný popis součástí aplikace ASP.NET Core

Následující části popisují dodatky k projektu při ověřování podpora je součástí:

### <a name="startup-class"></a>Třída při spuštění

`Startup` Třída má těmito přídavky:

* Uvnitř `Startup.ConfigureServices` metody:
  * Identita s výchozí uživatelské rozhraní:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer ještě `AddApiAuthorization` Pomocná metoda tohoto nastavení některé výchozí konvence ASP.NET Core nad IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování pomocí dalších `AddIdentityServerJwt` Pomocná metoda, která nakonfiguruje aplikaci pro ověření tokenů JWT vytvářených IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Uvnitř `Startup.Configure` metody:
  * Middleware ověřování, která zodpovídá za ověření požadavku pověření a nastavení uživatele v kontextu požadavku:

    ```csharp
    app.UseAuthentication();
    ```

  * IdentityServer middleware, který zveřejňuje koncové body Open ID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Tato pomocná metoda nakonfiguruje IdentityServer používat naše podporovanou konfiguraci. IdentityServer je výkonný a rozšiřitelný rámec pro zpracování otázky zabezpečení aplikace. Ve stejnou dobu, která zveřejní zbytečné složitosti pro nejběžnější scénáře. V důsledku toho je sada konvence a možnosti konfigurace přesnost je potřeba, které jsou považovány za dobrým výchozím bodem. Po ověření potřeby změní, je plný výkon IdentityServer stále k dispozici pro přizpůsobení ověřování tak, aby odpovídala vašim potřebám.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Tato pomocná metoda nakonfiguruje zásady schéma pro aplikaci jako výchozí obslužnou rutinu ověřování. Pokud chcete, aby identita zpracování všech požadavků směrovat do jakékoli podřízená cesta v prostoru adresa URL Identity nakonfigurované "/ Identity". `JwtBearerHandler` Zpracovává všechny ostatní žádosti. Kromě toho tato metoda zaregistruje `<<ApplicationName>>API` prostředku rozhraní API s IdentityServer s výchozí obor `<<ApplicationName>>API` a nakonfiguruje middlewaru tokenu nosiče JWT ověření tokeny vystavené službou IdentityServer pro aplikaci.

### <a name="sampledatacontroller"></a>SampleDataController

V *Controllers\SampleDataController.cs* souboru, Všimněte si, že `[Authorize]` atributu použít pro třídu, která označuje, že uživatel musí být povoleno podle výchozí zásady pro přístup k prostředku. Byly konfigurovány k použití výchozího schématu ověřování, který je nastavený podle výchozí zásady autorizace se stane s `AddIdentityServerJwt` na schéma zásad, které jsme zmínili výše, což `JwtBearerHandler` nakonfiguroval tato Pomocná metoda pro obslužnou rutinu výchozí požadavky na aplikace.

### <a name="applicationdbcontext"></a>ApplicationDbContext

V *Data\ApplicationDbContext.cs* souboru, Všimněte si, že stejné `DbContext` se používá v Identity s výjimkou, která rozšiřuje `ApiAuthorizationDbContext` (více odvozené třídy z `IdentityDbContext`) a zahrnout schéma IdentityServer do.

K získání úplné kontroly schématu databáze, dědit z jednoho z dostupných Identity `DbContext` třídy a konfigurovat kontextu se zahrnout schéma Identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` na `OnModelCreating` metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

V *Controllers\OidcConfigurationController.cs* souboru, Všimněte si, že koncový bod, pro kterého je zřízené pro obsluhu OIDC parametry, které je potřeba použít klienta.

### <a name="appsettingsjson"></a>appsettings.json

V *appsettings.json* souboru kořen projektu je nový `IdentityServer` část popisující seznam nakonfigurovat klienty. V následujícím příkladu je jednoho klienta. Název klienta odpovídá názvu aplikace a mapovat pomocí konvence na OAuth `ClientId` parametru. Profil, který určuje typ aplikace, která se právě nastavuje. Používá se interně k jednotce vytváření názvů, které zjednodušují proces konfigurace pro server. K dispozici několik profilů, jak je vysvětleno v [profily aplikací](#application-profiles) oddílu.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.json

V *appsettings. Development.JSON* souboru kořen projektu je `IdentityServer` část popisující klíč používaný k podepisování tokenů. Při nasazení do produkčního prostředí, klíč se musí zřídit a nasadit spolu s aplikací, jak je vysvětleno v [nasazení do produkčního prostředí](#deploy-to-production) oddílu.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Obecný popis aplikaci Angular

Ověřování a autorizace API podporují v Angular šablona se nachází v jeho vlastní Angular modulu *ClientApp\src\api autorizace* adresáře. Modul se skládá z následujících elementů:

* 3 součásti:
  * *Login.Component.TS*: Zpracuje proces přihlášení aplikace.
  * *logout.Component.TS*: Zpracovává odhlášení toku aplikace.
  * *login-menu.component.ts*: Widget, který zobrazí jedno z následujících sad odkazů:
    * Správa profilů uživatelů a odkazy, když je uživatel ověřený při odhlášení.
    * Registrace a přihlášení odkazy, když uživatel není ověřen.
* Trasa guard `AuthorizeGuard` , které mohou být přidány do cesty a vyžaduje, uživatel k ověření před návštěvou trasy.
* Protokolu HTTP zachycování `AuthorizeInterceptor` přístupový token, který připojí k cílení na rozhraní API, když je uživatel ověřený odchozí požadavky HTTP.
* Služba `AuthorizeService` , který zpracovává podrobnosti nižší úrovně o procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.
* Angular modul, který definuje trasy, které jsou přidružené k ověřování části aplikace. Zpřístupňuje komponenta nabídky přihlášení, sběrač, ochranného zařízení a služby pro použití na zbývající části aplikace.

## <a name="general-description-of-the-react-app"></a>Obecný popis aplikace React

Podpora pro ověřování a autorizace rozhraní API v šabloně React se nachází v *ClientApp\src\components\api autorizace* adresáře. Se skládá z následujících elementů:

* 4 komponenty:
  * *Login.js*: Zpracuje proces přihlášení aplikace.
  * *Logout.js*: Zpracovává odhlášení toku aplikace.
  * *LoginMenu.js*: Widget, který zobrazí jedno z následujících sad odkazů:
    * Správa profilů uživatelů a odkazy, když je uživatel ověřený při odhlášení.
    * Registrace a přihlášení odkazy, když uživatel není ověřen.
  * *AuthorizeRoute.js*: Podle postupu součást, která vyžaduje uživatele k ověření před vykreslením komponentu `Component` parametru.
* Exportovaná `authService` instance třídy `AuthorizeService` , který zpracovává podrobnosti nižší úrovně o procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.

Teď, když jste viděli základních komponent řešení, můžete využít najdete podrobnější přehled jednotlivých scénářů aplikace.

## <a name="require-authorization-on-a-new-api"></a>Vyžadovat ověření na nové rozhraní API

Ve výchozím nastavení systém je nakonfigurován tak, aby vyžadovala snadno autorizace pro nové rozhraní API. Uděláte to tak, vytvořte nový kontroler a přidejte `[Authorize]` atribut třídy kontroleru nebo na každou akci v rámci kontroleru.

## <a name="protect-a-client-side-route-angular"></a>Ochrana trasu na straně klienta (Angular)

Ochrana trasu na straně klienta se provádí tak, že přidáte authorize guard do seznamu chrání ke spuštění při konfiguraci směrování. Jako příklad najdete v tématu Jak `fetch-data` je trasa nakonfigurována v hlavní aplikaci Angular modulu:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Je důležité zmínit, že ochrana trasu nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut), ale, že ji pouze znemožní uživateli navigace pro danou trasu na straně klienta, když není ověřený.

## <a name="authenticate-api-requests-angular"></a>Ověření požadavků rozhraní API (Angular)

Ověřování požadavků na rozhraní API hostovaná aplikace se provádí automaticky pomocí zachycování klienta HTTP definované aplikací.

## <a name="protect-a-client-side-route-react"></a>Ochrana trasu na straně klienta (React)

Ochrana trasu na straně klienta pomocí `AuthorizeRoute` komponentu místo prostý `Route` komponenty. Například, Všimněte si, jak `fetch-data` je trasa nakonfigurována v rámci `App` komponenty:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Ochrana trasy:

* Nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut).
* Pouze znemožní uživateli navigace pro danou trasu na straně klienta, když není ověřený.

## <a name="authenticate-api-requests-react"></a>Ověření požadavků rozhraní API (React)

Ověřování požadavků pomocí React probíhá v první import `authService` z instance `AuthorizeService`. Přístupový token je načten z `authService` je připojená k požadavku, jak je znázorněno níže. V součástech React tuto práci se obvykle provádí `componentDidMount` životního cyklu metody nebo jako výsledek z některých interakci s uživatelem.

### <a name="import-the-authservice-into-your-component"></a>Importovat authService do komponenty

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Načíst a připojit přístupového tokenu do odpovědi

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

Nasazení aplikace do produkčního prostředí, třeba zřídí následující prostředky:

* Databázi k ukládání uživatelských účtů identit a IdentityServer uděluje.
* Produkční certifikát používaný k podepisování tokenů.
  * Neexistují žádné zvláštní požadavky tohoto certifikátu; může být certifikát podepsaný svým držitelem nebo certifikát poskytované prostřednictvím Certifikační autority.
  * Mohou být generovány prostřednictvím standardních nástrojů jako PowerShell nebo OpenSSL.
  * Můžete nainstalovat do úložiště certifikátů na cílových počítačích nebo nasadit jako *.pfx* souboru silným heslem.

### <a name="example-deploy-to-azure-websites"></a>Příklad: Nasazení do Azure Websites

Tato část popisuje nasazení aplikace na Azure websites pomocí certifikátu uloženého v úložišti certifikátů. Pokud chcete upravit aplikaci načíst certifikát z úložiště certifikátů, plán služby App Service musí být na alespoň na úrovni Standard při konfiguraci v pozdějším kroku. V aplikaci prvku *appsettings.json* souboru, upravte `IdentityServer` části Zahrnout podrobnosti klíče:

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

* Vlastnost name u certifikátu odpovídá rozlišující subjekt certifikátu.
* Představuje umístění úložiště, kam se načíst certifikát z (`CurrentUser` nebo `LocalMachine`).
* Název úložiště představuje název úložiště certifikátů, kdy certifikát je uložen. V takovém případě odkazuje na úložiště osobní uživatele.

Pokud chcete nasadit na weby Azure, nasazení aplikace podle kroků v [aplikaci nasadit do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) prostředky Azure potřebné pro vytvoření a nasazení aplikace do produkčního prostředí.

Po provedení předchozích kroků, aplikace se nasadí do Azure, ale ještě není funkční. Certifikát používaný aplikací je stále potřeba nastavit. Vyhledejte kryptografický otisk certifikátu, který se má použít a postupujte podle kroků popsaných v [načíst vaše certifikáty](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).

Přestože tyto kroky zmiňovat SSL, je **privátní certifikáty** části na portálu, kde můžete nahrát zřízené certifikátu pro použití s aplikací.

Po provedení tohoto kroku, restartujte aplikaci a měla by být funkční.

## <a name="other-configuration-options"></a>Další možnosti konfigurace

Podpora rozhraní API autorizace postavená na IdentityServer sadu konvence, výchozí hodnoty a vylepšení prostředí pro SPA zjednodušit. Needless znamená potenciál IdentityServer je k dispozici na pozadí na integrace ASP.NET Core, které nepokrývá váš scénář. Podpora technologie ASP.NET Core se zaměřuje na "vlastní" aplikace Microsoftu, kde jsou všechny aplikace vytvoření a nasazení naší organizace. V důsledku toho není dostupná podpora pro takové věci, třeba souhlas nebo federace. Pro tyto scénáře použití IdentityServer a postupujte podle jejich dokumentaci.

### <a name="application-profiles"></a>Profily aplikací

Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále určují své parametry. V současné době jsou podporovány následující profily:

* `IdentityServerSPA`: Představuje SPA hostované vedle IdentityServer jako jeden celek.
  * `redirect_uri` Výchozí hodnota je `/authentication/login-callback`.
  * `post_logout_redirect_uri` Výchozí hodnota je `/authentication/logout-callback`.
  * Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).
  * Režim povolený odpovědi je `fragment`.
* `SPA`: Představuje SPA, která není hostována s IdentityServer.
  * Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).
  * Režim povolený odpovědi je `fragment`.
* `IdentityServerJwt`: Představuje rozhraní API, která je hostována společně s IdentityServer.
  * Aplikace má nastavený jeden obor, která jako výchozí název aplikace.
* `API`: Představuje rozhraní API, která není hostována s IdentityServer.
  * Aplikace má nastavený jeden obor, která jako výchozí název aplikace.

### <a name="configuration-through-appsettings"></a>Konfigurace prostřednictvím AppSettings

Konfigurace aplikací pomocí konfiguračního systému jejich přidáním do seznamu `Clients` nebo `Resources`.

Konfigurovat každý klient `redirect_uri` a `post_logout_redirect_uri` vlastnost, jak je znázorněno v následujícím příkladu:

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

Při konfiguraci prostředků, můžete nakonfigurovat obory pro prostředek, jak je znázorněno níže:

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

Můžete taky nakonfigurovat klienty a prostředků prostřednictvím kódu pomocí přetížení `AddApiAuthorization` , která má akci, kterou chcete nakonfigurovat možnosti.

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
