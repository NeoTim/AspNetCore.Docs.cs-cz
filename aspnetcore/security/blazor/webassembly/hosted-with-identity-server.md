---
title: Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí serveru identit
author: guardrex
description: Vytvoření nové Blazor hostované aplikace s ověřováním v sadě Visual Studio, které používá back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ffdcd30ae9ce5350113569a500e99cf8db82ad65
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138600"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="aaf47-103">Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí serveru identit</span><span class="sxs-lookup"><span data-stu-id="aaf47-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="aaf47-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aaf47-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="aaf47-105">Vytvoření nové Blazor hostované aplikace v aplikaci Visual Studio, která používá [IdentityServer](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="aaf47-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="aaf47-106">Pomocí sady Visual Studio vytvořte novou \*\* Blazor aplikaci WebAssembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="aaf47-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="aaf47-107">Další informace naleznete v tématu <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="aaf47-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="aaf47-108">V dialogovém okně **vytvořit novou Blazor aplikaci** vyberte v části **ověřování** možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="aaf47-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="aaf47-109">Vyberte **jednotlivé uživatelské účty** a potom klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="aaf47-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="aaf47-110">Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .</span><span class="sxs-lookup"><span data-stu-id="aaf47-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="aaf47-111">Vyberte tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="aaf47-111">Select the **Create** button.</span></span>

<span data-ttu-id="aaf47-112">Pokud chcete vytvořit aplikaci v příkazovém prostředí, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="aaf47-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="aaf47-113">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="aaf47-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="aaf47-114">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="aaf47-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="aaf47-115">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="aaf47-115">Server app configuration</span></span>

<span data-ttu-id="aaf47-116">Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.</span><span class="sxs-lookup"><span data-stu-id="aaf47-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="aaf47-117">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="aaf47-117">Startup class</span></span>

<span data-ttu-id="aaf47-118">`Startup` Třída má následující doplňky:</span><span class="sxs-lookup"><span data-stu-id="aaf47-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="aaf47-119">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aaf47-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="aaf47-120">Odcizen</span><span class="sxs-lookup"><span data-stu-id="aaf47-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="aaf47-121">IdentityServer s další <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnou metodou, která nastaví některé výchozí konvence ASP.NET Core nad IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="aaf47-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="aaf47-122">Ověřování s další <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnou metodou, která nakonfiguruje aplikaci pro ověření TOKENů JWT vyprodukovaných pomocí IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="aaf47-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="aaf47-123">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aaf47-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="aaf47-124">Middleware ověřování, který zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="aaf47-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="aaf47-125">Middleware IdentityServer, který zveřejňuje koncové body Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="aaf47-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="aaf47-126">Middleware pro ověřování a autorizaci:</span><span class="sxs-lookup"><span data-stu-id="aaf47-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="aaf47-127">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="aaf47-127">AddApiAuthorization</span></span>

<span data-ttu-id="aaf47-128"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Pomocná metoda konfiguruje [IdentityServer](https://identityserver.io/) pro scénáře ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aaf47-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="aaf47-129">IdentityServer je výkonná a rozšiřitelná architektura pro zpracování otázek zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="aaf47-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="aaf47-130">IdentityServer zpřístupňuje zbytečné složitosti pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="aaf47-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="aaf47-131">V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="aaf47-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="aaf47-132">Jakmile se vaše potřeby ověřování změní, je stále k dispozici kompletní výkon IdentityServer, aby bylo možné přizpůsobit ověřování podle požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaf47-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="aaf47-133">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="aaf47-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="aaf47-134"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="aaf47-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="aaf47-135">Zásady jsou nakonfigurovány tak, aby umožňovaly identitě zpracovávat všechny požadavky směrované na jakoukoli dílčí cestu v prostoru `/Identity`adres URL identity.</span><span class="sxs-lookup"><span data-stu-id="aaf47-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="aaf47-136"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Zpracovává všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="aaf47-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="aaf47-137">Tato metoda navíc:</span><span class="sxs-lookup"><span data-stu-id="aaf47-137">Additionally, this method:</span></span>

* <span data-ttu-id="aaf47-138">Zaregistruje prostředek `{APPLICATION NAME}API` rozhraní API s IdentityServer s výchozím oborem `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="aaf47-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="aaf47-139">Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aaf47-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="aaf47-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="aaf47-140">WeatherForecastController</span></span>

<span data-ttu-id="aaf47-141">V `WeatherForecastController` (*Controllers/WeatherForecastController. cs*) je [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atribut použit pro třídu.</span><span class="sxs-lookup"><span data-stu-id="aaf47-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="aaf47-142">Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="aaf47-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="aaf47-143">Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> na dříve zmíněné schéma zásad.</span><span class="sxs-lookup"><span data-stu-id="aaf47-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="aaf47-144">Pomocná metoda se <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> konfiguruje jako výchozí obslužná rutina pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aaf47-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="aaf47-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="aaf47-145">ApplicationDbContext</span></span>

<span data-ttu-id="aaf47-146">V `ApplicationDbContext` (*data/ApplicationDbContext. cs*) se používá stejná <xref:Microsoft.EntityFrameworkCore.DbContext> v identitě s výjimkou, kterou rozšiřuje <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> , aby zahrnovala schéma pro IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="aaf47-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="aaf47-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="aaf47-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="aaf47-148">Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných tříd identity <xref:Microsoft.EntityFrameworkCore.DbContext> a nakonfigurovat kontext tak, aby zahrnoval schéma identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="aaf47-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="aaf47-149">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="aaf47-149">OidcConfigurationController</span></span>

<span data-ttu-id="aaf47-150">V `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*) se koncový bod klienta zřídí pro poskytování parametrů OIDC.</span><span class="sxs-lookup"><span data-stu-id="aaf47-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="aaf47-151">Soubory nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="aaf47-151">App settings files</span></span>

<span data-ttu-id="aaf47-152">V souboru nastavení aplikace (*appSettings. JSON*) v kořenovém adresáři projektu obsahuje `IdentityServer` část seznam konfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="aaf47-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="aaf47-153">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="aaf47-153">In the following example, there's a single client.</span></span> <span data-ttu-id="aaf47-154">Název klienta odpovídá názvu aplikace a je mapován podle konvence na parametr OAuth `ClientId` .</span><span class="sxs-lookup"><span data-stu-id="aaf47-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="aaf47-155">Profil indikuje typ aplikace, která se konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="aaf47-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="aaf47-156">Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru.</span><span class="sxs-lookup"><span data-stu-id="aaf47-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="aaf47-157">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="aaf47-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="aaf47-158">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="aaf47-158">Authentication package</span></span>

<span data-ttu-id="aaf47-159">Když je aplikace vytvořená tak, aby používala jednotlivé`Individual`uživatelské účty (), aplikace automaticky obdrží odkaz `Microsoft.AspNetCore.Components.WebAssembly.Authentication` na balíček v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaf47-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="aaf47-160">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="aaf47-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="aaf47-161">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="aaf47-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="aaf47-162">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="aaf47-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="aaf47-163">Podpora autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="aaf47-163">API authorization support</span></span>

<span data-ttu-id="aaf47-164">Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v rámci `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíčku.</span><span class="sxs-lookup"><span data-stu-id="aaf47-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="aaf47-165">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s existujícím autorizačním systémem.</span><span class="sxs-lookup"><span data-stu-id="aaf47-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="aaf47-166">Ve výchozím nastavení načte konfiguraci aplikace podle konvence z `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="aaf47-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="aaf47-167">Podle konvence je ID klienta nastaveno na název sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaf47-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="aaf47-168">Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.</span><span class="sxs-lookup"><span data-stu-id="aaf47-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="aaf47-169">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="aaf47-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="aaf47-170">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="aaf47-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="aaf47-171">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="aaf47-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="aaf47-172">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="aaf47-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="aaf47-173">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="aaf47-173">LoginDisplay component</span></span>

<span data-ttu-id="aaf47-174">Součást (*Shared/LoginDisplay. Razor*) je vykreslena ve `MainLayout` komponentě (*Shared/MainLayout. Razor*) a spravuje následující chování: `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="aaf47-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="aaf47-175">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="aaf47-175">For authenticated users:</span></span>
  * <span data-ttu-id="aaf47-176">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="aaf47-176">Displays the current user name.</span></span>
  * <span data-ttu-id="aaf47-177">Nabízí odkaz na stránku profilu uživatele v ASP.NET Core identita.</span><span class="sxs-lookup"><span data-stu-id="aaf47-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="aaf47-178">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaf47-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="aaf47-179">Pro anonymní uživatele:</span><span class="sxs-lookup"><span data-stu-id="aaf47-179">For anonymous users:</span></span>
  * <span data-ttu-id="aaf47-180">Nabízí možnost registrace.</span><span class="sxs-lookup"><span data-stu-id="aaf47-180">Offers the option to register.</span></span>
  * <span data-ttu-id="aaf47-181">Nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="aaf47-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="aaf47-182">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="aaf47-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="aaf47-183">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="aaf47-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="aaf47-184">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="aaf47-184">Run the app</span></span>

<span data-ttu-id="aaf47-185">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="aaf47-185">Run the app from the Server project.</span></span> <span data-ttu-id="aaf47-186">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="aaf47-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="aaf47-187">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="aaf47-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
