---
title: Zabezpečení ASP.NET Blazor aplikace hostované pomocí webové sestavy Core S identitou
author: guardrex
description: Vytvoření nové Blazor hostované aplikace s ověřováním z aplikace Visual Studio, která používá back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501273"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Zabezpečení ASP.NET Blazor aplikace hostované pomocí webové sestavy Core S identitou

[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Vytvoření nové Blazor hostované aplikace v sadě Visual Studio, která používá [IdentityServer](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API:

1. Pomocí sady Visual Studio ** Blazor ** vytvořte novou aplikaci WebAssembly. Další informace naleznete v tématu <xref:blazor/get-started>.
1. V **dialogovém Blazor okně Vytvořit novou aplikaci** vyberte **Změnit** v části **Ověřování.**
1. Vyberte **jednotlivé uživatelské účty** následované **ok**.
1. Zaškrtněte **políčko ASP.NET Jádro hostované** v části **Upřesnit.**
1. Vyberte tlačítko **Vytvořit**.

Chcete-li aplikaci vytvořit v příkazovém prostředí, proveďte následující příkaz:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ). Název složky se také stane součástí názvu projektu.

## <a name="server-app-configuration"></a>Konfigurace serverové aplikace

Následující části popisují dodatky k projektu, pokud je zahrnuta podpora ověřování.

### <a name="startup-class"></a>Spouštěcí třída

Třída `Startup` má následující dodatky:

* V `Startup.ConfigureServices`:

  * Identita s výchozím ui:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer s <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> další pomocnou metodou, která nastaví některé výchozí ASP.NET základní konvence nad IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Ověřování s <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> další pomocnou metodou, která konfiguruje aplikaci k ověření tokenů JWT vytvořených serverem IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* V `Startup.Configure`:

  * Ověřovací middleware, který je zodpovědný za ověření pověření požadavku a nastavení uživatele v kontextu požadavku:

    ```csharp
    app.UseAuthentication();
    ```

  * Middleware IdentityServer, který zveřejňuje koncové body Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>Přidat oprávnění api

Pomocná <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metoda konfiguruje [IdentityServer](https://identityserver.io/) pro scénáře ASP.NET Core. IdentityServer je výkonný a rozšiřitelný rámec pro zpracování problémů se zabezpečením aplikací. IdentityServer zveřejňuje zbytečné složitosti pro nejběžnější scénáře. V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod. Jakmile se vaše ověřování potřebuje změnit, plný výkon IdentityServer je stále k dispozici přizpůsobit ověřování tak, aby vyhovovaly požadavkům aplikace.

### <a name="addidentityserverjwt"></a>PřidatIdentityServerJwt

Pomocná <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metoda konfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování. Zásada je nakonfigurována tak, aby identita mohla zpracovávat všechny požadavky `/Identity`směrované na libovolnou podcestu v prostoru adresy URL identity . Zpracovává <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> všechny ostatní požadavky. Navíc tato metoda:

* Registruje `{APPLICATION NAME}API` prostředek rozhraní API s IdentityServer `{APPLICATION NAME}API`s výchozím rozsahem .
* Konfiguruje Middleware tokenu Nosiče JWT tak, aby ověřoval tokeny vydané serverem IdentityServer pro danou aplikaci.

### <a name="weatherforecastcontroller"></a>Kontrollet weatherforecastu

V `WeatherForecastController` souboru (*Controllers/WeatherForecastController.cs*) je [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atribut použit pro třídu. Atribut označuje, že uživatel musí být autorizován na základě výchozí zásady pro přístup k prostředku. Výchozí zásady autorizace jsou nakonfigurovány tak, aby <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> používaly výchozí schéma ověřování, které je nastaveno podle schématu zásad, které bylo uvedeno dříve. Pomocná metoda konfiguruje jako výchozí obslužná <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> rutina pro požadavky na aplikaci.

### <a name="applicationdbcontext"></a>ApplicationDbContext

V `ApplicationDbContext` (*Data/ApplicationDbContext.cs*) <xref:Microsoft.EntityFrameworkCore.DbContext> se totéž používá v identitě <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> s výjimkou, že se rozšiřuje zahrnout schéma pro IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>je odvozen <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>od .

Chcete-li získat úplnou kontrolu nad schématem databáze, dědit z jedné z dostupných tříd identity <xref:Microsoft.EntityFrameworkCore.DbContext> `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` a `OnModelCreating` nakonfigurovat kontext zahrnout schéma identity voláním v metodě.

### <a name="oidcconfigurationcontroller"></a>Řadič konfigurace Oidc

V `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) je zřízen koncový bod klienta, který slouží parametrům OIDC.

### <a name="app-settings-files"></a>Soubory nastavení aplikace

V souboru nastavení aplikace (*appsettings.json*) `IdentityServer` v kořenovém adresáři projektu popisuje část seznam nakonfigurovaných klientů. V následujícím příkladu je jeden klient. Název klienta odpovídá názvu aplikace a je mapován podle `ClientId` konvence na parametr OAuth. Profil označuje nakonfigurovaný typ aplikace. Profil se používá interně k pohonu konvence, které zjednodušují proces konfigurace pro server. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

V souboru nastavení aplikace vývojové prostředí (*nastavení aplikace. Development.json*) v kořenovém adresáři projektu, `IdentityServer` část popisuje klíč používaný k podepisování tokenů. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Konfigurace klientské aplikace

### <a name="authentication-package"></a>Ověřovací balíček

Když je aplikace vytvořena pro použití`Individual`individuálních uživatelských účtů ( `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ), aplikace automaticky obdrží odkaz na balíček pro balíček v souboru projektu aplikace. Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.

Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.

### <a name="api-authorization-support"></a>Podpora autorizace rozhraní API

Podpora pro ověřování uživatelů je zapojen do kontejneru služby metodou `Microsoft.AspNetCore.Components.WebAssembly.Authentication` rozšíření poskytované uvnitř balíčku. Tato metoda nastaví všechny služby potřebné pro aplikaci pro interakci s existujícím systémem autorizace.

```csharp
builder.Services.AddApiAuthorization();
```

Ve výchozím nastavení načte konfiguraci aplikace `_configuration/{client-id}`podle konvence z . Podle konvence id klienta je nastavena na název sestavení aplikace. Tuto adresu URL lze změnit tak, aby přecšití na samostatný koncový bod voláním přetížení s možnostmi.

### <a name="imports-file"></a>Importuje soubor

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexová stránka

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Součást aplikace

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Komponenta RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Komponenta LoginDisplay

Komponenta `LoginDisplay` (*Shared/LoginDisplay.razor*) je `MainLayout` vykreslena v komponentě (*Shared/MainLayout.razor*) a spravuje následující chování:

* Pro ověřené uživatele:
  * Zobrazí aktuální uživatelské jméno.
  * Nabízí odkaz na stránku profilu uživatele v ASP.NET základní identity.
  * Nabízí tlačítko pro odhlášení z aplikace.
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

### <a name="authentication-component"></a>Ověřovací komponenta

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Komponenta FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci z projektu Server. Při použití sady Visual Studio vyberte projekt serveru v **Průzkumníku řešení** a vyberte tlačítko **Spustit** na panelu nástrojů nebo spusťte aplikaci z nabídky **Ladění.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Další zdroje

* [Vyžádání dalších přístupových tokenů](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
