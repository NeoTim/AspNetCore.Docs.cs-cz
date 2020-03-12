---
title: Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí serveru identit
author: guardrex
description: Vytvoření nové hostované aplikace v Blazor s ověřováním ze sady Visual Studio, která používá back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 98eb126ab3c483e0a6dc2274db8ffcfd9d5bc59a
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083774"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí serveru identit

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Vytvoření nové hostované aplikace Blazor v aplikaci Visual Studio, která používá [IdentityServer](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API:

1. Pomocí sady Visual Studio vytvořte novou **Blazor aplikaci WebAssembly** . Další informace naleznete v tématu <xref:blazor/get-started>.
1. V dialogovém okně **vytvořit novou aplikaci Blazor** vyberte v části **ověřování** možnost **změnit** .
1. Vyberte **jednotlivé uživatelské účty** a potom klikněte na **OK**.
1. Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .
1. Vyberte tlačítko **Vytvořit**.

Pokud chcete vytvořit aplikaci v příkazovém prostředí, spusťte následující příkaz:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`). Název složky se také stal součástí názvu projektu.

## <a name="server-app-configuration"></a>Konfigurace aplikace serveru

Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.

### <a name="startup-class"></a>Spouštěcí třída

Třída `Startup` obsahuje následující doplňky:

* V `Startup.ConfigureServices`:

  * Identita s výchozím uživatelským rozhraním:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer se další pomocnou metodou <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, která nastaví některé výchozí konvence ASP.NET Core na IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování s dodatečnou podpůrnou metodou <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, která nakonfiguruje aplikaci tak, aby ověřovala tokeny JWT vytvořené pomocí IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* V `Startup.Configure`:

  * Middleware ověřování, který zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:

    ```csharp
    app.UseAuthentication();
    ```

  * Middleware IdentityServer, který zveřejňuje koncové body Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Pomocná metoda <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> konfiguruje [IdentityServer](https://identityserver.io/) pro ASP.NET Core scénáře. IdentityServer je výkonná a rozšiřitelná architektura pro zpracování otázek zabezpečení aplikací. IdentityServer zpřístupňuje zbytečné složitosti pro nejběžnější scénáře. V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod. Jakmile se vaše potřeby ověřování změní, je stále k dispozici kompletní výkon IdentityServer, aby bylo možné přizpůsobit ověřování podle požadavků aplikace.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Pomocná metoda <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování. Zásady jsou nakonfigurovány tak, aby umožňovaly identitě zpracovávat všechny požadavky směrované na jakoukoli dílčí cestu v prostoru adres URL `/Identity`. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> zpracovává všechny ostatní požadavky. Tato metoda navíc:

* Zaregistruje prostředek rozhraní `{APPLICATION NAME}API` API s IdentityServer s výchozím rozsahem `{APPLICATION NAME}API`.
* Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

V `WeatherForecastController` (*Controllers/WeatherForecastController. cs*) se pro třídu použije atribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) . Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku. Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> na dříve zmíněné schéma zásad. Pomocná metoda nakonfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužnou rutinu pro požadavky na aplikaci.

### <a name="applicationdbcontext"></a>ApplicationDbContext

V `ApplicationDbContext` (*data/ApplicationDbContext. cs*) se stejný <xref:Microsoft.EntityFrameworkCore.DbContext> používá v identitě s výjimkou, že rozšíření <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> pro zahrnutí schématu pro IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných tříd identity <xref:Microsoft.EntityFrameworkCore.DbContext> a nakonfigurujte kontext tak, aby zahrnoval schéma identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v metodě `OnModelCreating`.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

V `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*) se koncový bod klienta zřídí pro poskytování parametrů OIDC.

### <a name="app-settings-files"></a>Soubory nastavení aplikace

V souboru nastavení aplikace (*appSettings. JSON*) v kořenovém adresáři projektu popisuje část `IdentityServer` seznam konfigurovaných klientů. V následujícím příkladu je jeden klient. Název klienta odpovídá názvu aplikace a je mapován podle konvence na parametr `ClientId` OAuth. Profil indikuje typ aplikace, která se konfiguruje. Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

V souboru nastavení aplikace pro vývojové prostředí (*appSettings. Development. JSON*) v kořenovém adresáři projektu popisuje část `IdentityServer` klíč použitý k podepisování tokenů. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Konfigurace klientské aplikace

### <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty (`Individual`), aplikace automaticky obdrží odkaz na balíček pro balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` v souboru projektu aplikace. Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.

Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.

### <a name="api-authorization-support"></a>Podpora autorizace rozhraní API

Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v balíčku `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s existujícím autorizačním systémem.

```csharp
builder.Services.AddApiAuthorization();
```

Ve výchozím nastavení načte konfiguraci aplikace podle konvence z `_configuration/{client-id}`. Podle konvence je ID klienta nastaveno na název sestavení aplikace. Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.

### <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Komponenta LoginDisplay

Součást `LoginDisplay` (*Shared/LoginDisplay. Razor*) je vykreslena ve `MainLayout` komponentě (*Shared/MainLayout. Razor*) a spravuje následující chování:

* Pro ověřené uživatele:
  * Zobrazí aktuální uživatelské jméno.
  * Nabízí odkaz na stránku profilu uživatele v ASP.NET Core identita.
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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
