---
title: Úvod k ověřování pro jednostránkové aplikace v ASP.NET Core
author: ''
description: Pomocí Identity jednostránkovou aplikaci hostované uvnitř aplikace ASP.NET Core.
ms.author: ''
ms.date: 03/05/2018
uid: security/authentication/identity/spa
ms.openlocfilehash: cf04ec1ff0ae9afea066fd1864ab0a7956ace32c
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346806"
---
# <a name="authentication-and-authorization-for-spas"></a>Ověřování a autorizace pro SPA

V technologii ASP.NET 3.0 přinášíme podporu pro ověřování v jednostránkové aplikace pomocí naši novou podporu pro ověření rozhraní API. Tato podpora je založen na kombinaci ASP.NET Core Identity pro ověřování a ukládání uživatele a Identity serveru pro implementaci Open ID Connect.

Přidali jsme nový parametr ověřování do našich Angular a React šablony, které je podobné tomuto: Parametr ověřování v našich šablon stránky mvc a razor s povolenými hodnotami 'None' a "Individuální".

## <a name="create-an-angular-app-with-api-authorization-support"></a>Vytvořit aplikaci Angular díky podpoře rozhraní API autorizace

Pokud chcete vytvořit novou aplikaci Angular s podporou pro ověřování a autorizaci uživatelů, otevřete příkazové okno a spusťte následující příkaz:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

Předchozí příkaz vytvoří aplikace ASP.NET Core s *ClientApp* adresář obsahující aplikaci Angular.

## <a name="create-a-react-app-with-api-authorization-support"></a>Vytvoření aplikace s React díky podpoře rozhraní API autorizace

Pokud chcete vytvořit novou aplikaci React s podporou pro ověřování a autorizaci uživatelů, otevřete příkazové okno a spusťte následující příkaz:

```console
dotnet new react -o <output_directory_name> -au Individual
```

Předchozí příkaz vytvoří aplikace ASP.NET Core s *ClientApp* adresář obsahující aplikaci React.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Obecný popis součástí aplikace ASP.NET Core

Když jsme zahrnují podporu ověřování jsou několik dodatky k projektu:

### <a name="startup-class"></a>Třída při spuštění

Když se podíváme na kód v třídu pro spuštění vážíme následující zahrnutí:
* Uvnitř `public void ConfigureServices(IServiceCollection services)`:
  * Identita s výchozí uživatelské rozhraní.
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * Server identit s další Pomocná metoda AddApiAuthorization tohoto nastavení některé výchozí konvence ASP.NET na serveru identit.
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * Ověřování pomocí další Pomocná metoda AddIdentityServerJwt, který konfiguruje aplikaci, aby ověřoval tokeny Jwt vytvářených Identity serveru. 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* Uvnitř `public void Configure(IApplicationBuilder app)`:
  * Ověřovací middleware, který je zodpovědný za ověřují se přihlašovací údaje v příchozím požadavku a nastavení uživatele v kontextu požadavku.
    ```csharp
    app.UseAuthentication();
    ```
  * Middleware identity serveru, který zveřejňuje koncové body Open ID Connect.
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization 
Tato pomocná metoda nakonfiguruje Server Identity použít naše podporovanou konfiguraci. Identita serveru je velmi výkonný a rozšiřitelný rámec pro zpracování otázky zabezpečení aplikací, ale ve stejnou dobu, která zveřejňuje spoustu složitost, která nemusíme vědět o nejběžnějších scénářů, takže jsme vyberte sadu konvence a Možnosti konfigurace, který budeme přemýšlet o jsou dobrým výchozím bodem. Po ověření měnících se požadavků všech možností Identity serveru je stále k dispozici, takže můžete přizpůsobit tak, aby odpovídala vašim potřebám.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt
Tato pomocná metoda nakonfiguruje zásady schéma pro aplikaci jako výchozí obslužnou rutinu ověřování. Pokud chcete, aby identita zpracování všech požadavků, které jsou určeny pro všechny dílčí cestou do pole Adresa url Identity nakonfigurované "/ Identity" a chcete, aby JwtBearerHandler zpracování všech ostatních požadavků.
Metoda registruje Addionally `<<ApplicationName>>API` prostředku rozhraní Api serveru identit s výchozí obor `<<ApplicationName>>API` a nakonfiguruje middlewaru tokenu nosiče JWT ověření tokeny vystavené službou serveru identit pro aplikaci.

### <a name="sampledatacontroller"></a>SampleDataController
Když se podíváte na soubor Controllers\SampleDataController.cs můžete podle našich zkušeností `[Authorize]` atributu použít pro třídu, která označuje, že uživatel musí být povoleno podle výchozí zásady pro přístup k prostředku. Nakonfigurovat, aby používal výchozí schéma ověřování, který je nastavený podle výchozí zásady autorizace se stane s `AddIdentityServerJwt` na schéma zásad, které už jsme zmínili výše, provádění obslužné rutiny JwtBearer nakonfiguroval tato Pomocná metoda pro obslužnou rutinu výchozí požadavky na aplikace.

### <a name="applicationdbcontext"></a>ApplicationDbContext
Když se podíváme na soubor na Data\ApplicationDbContext.cs vidíme stejného DbContext používáme v identitě s tím rozdílem, že rozšiřuje ApiAuthorizationDbContext (více odvozené třídy z IdentityDbContext) zahrnout schéma pro identitu serveru.
Pokud se mají plnou kontrolu nad schéma databáze můžeme jednoduše dědit z jednoho z dostupných tříd Identity DbContext a nakonfigurovat kontextu se zahrnout schéma identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` na `OnModelCreating` metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController
Když se podíváte na soubor Controllers\OidcConfigurationController.cs vidíme, koncový bod, který jsme Týmová poskytovat OIDC parametry, které je potřeba použít klienta.

### <a name="appsettingsjson"></a>appsettings.json
Když se podíváte na soubor appsettings.json v kořenovém adresáři projektu, můžeme vidět nový `IdentityServer` část popisující seznam nakonfigurovat klienty a vidíme, že je jednoho klienta. Název klienta odpovídá názvu aplikace a mapovat pomocí konvence na parametr ID klienta oAuth. Profil, který označuje, jaký typ aplikace jsme konfigurace a ji interně používáme konvencím jednotky, které zjednodušují proces konfigurace pro server. Nejsou k dispozici několik profilů je vysvětleno v následující části.

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
Když se podíváte na appsettings. Soubor Development.JSON v kořenovém adresáři projektu, můžeme vidět nový `IdentityServer` oddíl, který popisuje klíč se používá k podepisování tokenů. Při nasazení do produkčního prostředí klíč se musí zřídit a nasadit spolu s aplikace, jak je popsáno níže.

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a>Obecný popis aplikaci Angular
Podpora pro ověřování a autorizace rozhraní API v šabloně Angular se nachází v jeho vlastní Angular modulu. V části ClientApp\src\api autorizace a se skládá z následujících elementů:
* 3 součásti:
  * Součást přihlášení: Zpracuje proces přihlášení pro aplikaci.
  * Součást odhlášení: Zpracovává odhlášení toku pro aplikaci.
  * Komponenta nabídky přihlášení: Widget, který zobrazuje aktuálně ověřeného uživatele pomocí odkazů spravovat profil uživatele a odhlášení nebo odkazy na Přihlaste se nebo zaregistrujte, pokud uživatel není ověřen.
* Trasa guard `AuthorizeGuard` , které mohou být přidány do cesty a vyžaduje, uživatel k ověření před návštěvou trasy.
* Protokolu http zachycování `AuthorizeInterceptor` přístupový token, který připojí k cílení na rozhraní API, když je uživatel ověřený odchozí požadavky HTTP.
* Služba `AuthorizeService` , která zpracovává nižší úroveň podrobností v procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.
* Angular modul, který definuje trasy, které jsou přidružené k ověřování částí aplikace a poskytuje komponenta nabídky přihlášení, sběrač, ochranného zařízení a služby pro použití na zbývající části aplikace.

## <a name="general-description-of-the-react-application"></a>Obecný popis aplikace React
Podpora pro ověřování a autorizace rozhraní API v React život šablony v části ClientApp\src\components\api authorization\ a se skládá z následujících elementů:
* 4 komponenty:
  * Součást přihlášení: Zpracuje proces přihlášení pro aplikaci.
  * Součást odhlášení: Zpracovává odhlášení toku pro aplikaci.
  * Komponenta nabídky přihlášení: Widget, který zobrazuje aktuálně ověřeného uživatele pomocí odkazů spravovat profil uživatele a odhlášení nebo odkazy na Přihlaste se nebo zaregistrujte, pokud uživatel není ověřen.
  * AuthorizeRoute: Součást trasy, která vyžaduje uživatele k ověření před vykreslením součástí uvedené v parametru komponenty.
  * Exportovaná `authService` instance třídy `AuthorizeService` , která zpracovává nižší úroveň podrobností v procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.

Teď, když jsme se seznámili s základních komponent řešení, můžeme provést konkrétní podívejte se na jednotlivé scénáře pro aplikaci:

## <a name="requiring-authorization-on-a-new-api"></a>Vyžadování ověření na nové rozhraní API
Systém je nakonfigurován připravených k němu jednoduché vyžadují autorizace pro nové rozhraní API. Aby bylo možné učinit, jednoduše vytvořte nový řadič a přidejte `[Authorize]` atribut třídy kontroleru nebo na každou akci v rámci kontroleru.

## <a name="protecting-a-client-side-route-angular"></a>Ochrana trasu na straně klienta (Angular)
Ochrana trasu na straně klienta se provádí tak, že přidáte authorize guard do seznamu chrání ke spuštění při konfiguraci směrování. Jako příklad můžete vidět, konfigurace načítat data trasy v hlavní aplikaci angular modulu:

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Je důležité zmínit, že ochrana trasu nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut), ale, že ji pouze znemožní uživateli přejdete na trasu na straně daného klienta, když není ověřený.

## <a name="authenticate-api-requests-angular"></a>Ověření požadavků rozhraní API (Angular)

Ověřování požadavků na rozhraní API hostovaná po straně, že aplikace se provádí automaticky pomocí zachycování klienta HTTP definované aplikací.

## <a name="protect-a-client-side-route-react"></a>Ochrana trasu na straně klienta (React)

Ochrana trasu na straně klienta se provádí pomocí komponenty AuthorizeRoute místo prostého komponenty trasy. Jako příklad můžete vidět, konfigurace trasy načítat data do komponenty aplikace:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Je důležité zmínit, že ochrana trasu nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut), ale, že ji pouze znemožní uživateli přejdete na trasu na straně daného klienta, když není ověřený.

## <a name="authenticate-api-requests-react"></a>Ověření požadavků rozhraní API (React)

Ověřování požadavků pomocí react probíhá v první import `authService` z instance `AuthorizeService` a načtení přístupového tokenu z authService a připojíte ho k požadavku, jak je znázorněno níže. V součásti react to se obvykle provádí v metodě componentDidMount životního cyklu nebo jako výsledek z některých interakci s uživatelem.

### <a name="import-the-authservice-into-your-component"></a>Importovat authService do komponenty

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Načíst a připojit přístupového tokenu do odpovědi

```js
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-into-production"></a>Nasazení do produkčního prostředí

Pokud chcete nasadit aplikaci do produkčního prostředí, musíme zřídit několik prostředků:
* Uděluje databázi k ukládání uživatelských účtů identit a server identit.
* Produkční certifikát používaný k podepisování tokenů.
  * Neexistují žádné zvláštní požadavky tohoto certifikátu; může být certifikát podepsaný svým držitelem nebo certifikát poskytované prostřednictvím Certifikační autority.
  * Mohou být generovány prostřednictvím standardních nástrojů jako powershell nebo openssl.
  * Můžete nainstalovat do úložiště certifikátů na cílových počítačích nebo nasadit jako soubor pfx silným heslem.

### <a name="example-deploy-into-azure-websites"></a>Příklad: Nasazení do Azure Websites

V této části jsme se chystáte nasadit aplikaci do Azure websites pomocí certifikátu uloženého v úložišti certifikátů. Potřebujeme upravit aplikaci načíst certicate z úložiště certifikátů. Uděláte to tak, náš plán služby app service musí být alespoň na úrovni standard, když nakonfigurujeme v pozdějším kroku. V naší aplikaci jednoduše potřebujeme upravit IdentityServer věnované appsettings.json zahrnout podrobnosti klíče:
```json
  "IdentityServer": {
    "Key": {
      "Type": "Store",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "Name": "CN=MyApplication"
    }
  }
}
```
* Vlastnost name u certifikátu odpovídá rozlišující subjekt certifikátu.
* Umístění úložiště představuje kam se mají načíst certifikát (CurrentUrser nebo LocalMachine).
* Název úložiště představuje název úložiště certifikátů, kde je certifikát uložený v tomto případě odkazuje na úložiště osobní uživatele.

Pokud chcete nasadit na weby Azure, nasazení aplikace podle kroků v [aplikaci nasadit do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) prostředky Azure potřebné pro vytvoření a nasazení aplikace do produkčního prostředí.

Po této, aplikace se nasazuje do Azure, ale ještě není úplně funkční jak musíme nastavit certifikát, který chcete používat aplikace. Uděláte to tak, musíme mít kryptografický otisk certifikátu, budeme používat a postupujte podle kroků popsaných v [načíst vaše certifikáty](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).

Zatímco tyto kroky zmíníte SSL, je na portálu, kde jsme naše zřízené certifikátu pro použití s naší aplikace můžete nahrát oddíl "Privátní certifikáty".

Po provedení tohoto kroku jsme měli restartovat naši aplikaci a musí být plně funkční.

## <a name="other-configuration-options"></a>Další možnosti konfigurace
Naši podporu pro rozhraní API autorizace postavená na Identity serveru se sadou konvence, výchozí hodnoty a vylepšení pro zjednodušení prostředí pro jednostránkové aplikace. Needless znamená plný výkon serveru identit je k dispozici na pozadí na integrace, které nabízíme nepokrývá váš scénář. Naše podpora se zaměřuje na čemu říkáme "vlastní" aplikace, kde jsou všechny aplikace vytvoření a nasazení naší organizace. V důsledku nenabízíme podporu pro věci, jako je souhlas nebo federace. U scénářů doporučujeme používat identitu serveru a postupujte podle jejich dokumentaci.

### <a name="application-profiles"></a>Profily aplikací
Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále určují své parametry. V tuto chvíli podporujeme dva profily:
* IdentityServerSPA: Představuje jednostránková aplikace hostované vedle serveru identit jako jeden celek.
  * Výchozí hodnota redirect_uri `/authentication/login-callback`.
  * Výchozí hodnota post_logout_redirect_uri `/authentication/logout-callback`.
  * Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).
  * Režim povolený odpovědi je `fragment`.
* JEDNOSTRÁNKOVÁ APLIKACE: Představuje jednostránková aplikace, která není hostována serverem Identity.
  * Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.
  * Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).
  * Režim povolený odpovědi je `fragment`.
* IdentityServerJwt: Představuje rozhraní API, která je hostována společně s Identity serveru.
  * Aplikace má nastavený jeden obor, která jako výchozí název aplikace.
* API: Představuje rozhraní API, která není hostována serverem Identity.
  * Aplikace má nastavený jeden obor, která jako výchozí název aplikace.

### <a name="configuration-through-appsettings"></a>Konfigurace prostřednictvím AppSettings
Můžeme nakonfigurovat aplikace prostřednictvím našeho systému konfigurace jejich přidáním do seznamu klienty nebo prostředky v uvedeném pořadí. 

Při konfiguraci klientů můžeme nakonfigurovat `redirect_uri` a `post_logout_redirect_uri` jak je znázorněno níže:
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

Při konfiguraci zdroje můžeme nakonfigurovat obory pro prostředek, jak je znázorněno níže:
```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c",
    }
  }
}
```

### <a name="configuration-through-code"></a>Konfigurace prostřednictvím kódu
Můžeme také nakonfigurovat klienty a prostředků prostřednictvím kódu pomocí přetížení AddApiAuthorization, který provede akci ke konfiguraci možností.
```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA",
        spa => spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
            .WithLogoutRedirectUri("http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource => resource.WithScopes("a", "b", "c"));
});
```
