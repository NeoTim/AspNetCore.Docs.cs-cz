---
title: Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core se Identity serverem
author: guardrex
description: Vytvoření nové Blazor hostované aplikace s ověřováním v sadě Visual Studio, které používá back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: b0c6cbd814a23afabbbf9a0d943d28125ac1f61c
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944582"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="a1cde-103">Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core se Identity serverem</span><span class="sxs-lookup"><span data-stu-id="a1cde-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="a1cde-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a1cde-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a1cde-105">Tento článek vysvětluje, jak vytvořit novou Blazor hostovanou aplikaci, která používá [IdentityServer](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a1cde-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1cde-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1cde-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1cde-107">V aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a1cde-107">In Visual Studio:</span></span>

1. <span data-ttu-id="a1cde-108">Vytvořte novou **Blazor WebAssembly** aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a1cde-108">Create a new **Blazor WebAssembly** app.</span></span>
1. <span data-ttu-id="a1cde-109">V dialogovém okně **vytvořit novou Blazor aplikaci** vyberte v části **ověřování** možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="a1cde-109">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="a1cde-110">Vyberte **jednotlivé uživatelské účty** a potom klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="a1cde-110">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="a1cde-111">Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .</span><span class="sxs-lookup"><span data-stu-id="a1cde-111">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="a1cde-112">Vyberte tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a1cde-112">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a1cde-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1cde-113">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a1cde-114">Pokud chcete vytvořit aplikaci v příkazovém prostředí, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a1cde-114">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="a1cde-115">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="a1cde-115">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a1cde-116">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="a1cde-116">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="a1cde-117">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="a1cde-117">Server app configuration</span></span>

<span data-ttu-id="a1cde-118">Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.</span><span class="sxs-lookup"><span data-stu-id="a1cde-118">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="a1cde-119">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="a1cde-119">Startup class</span></span>

<span data-ttu-id="a1cde-120">`Startup`Třída obsahuje následující doplňky.</span><span class="sxs-lookup"><span data-stu-id="a1cde-120">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="a1cde-121">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a1cde-121">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="a1cde-122">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="a1cde-122">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="a1cde-123">IdentityServer s další <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnou metodou, která nastaví výchozí konvence ASP.NET Core nad IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="a1cde-123">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="a1cde-124">Ověřování s další <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnou metodou, která nakonfiguruje aplikaci pro ověření tokenů JWT vyprodukovaných pomocí IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="a1cde-124">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="a1cde-125">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a1cde-125">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="a1cde-126">Middleware IdentityServer zpřístupňuje koncové body Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="a1cde-126">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="a1cde-127">Middleware ověřování zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="a1cde-127">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="a1cde-128">Middleware autorizace povoluje možnosti autorizace:</span><span class="sxs-lookup"><span data-stu-id="a1cde-128">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="a1cde-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="a1cde-129">AddApiAuthorization</span></span>

<span data-ttu-id="a1cde-130"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Pomocná metoda konfiguruje [IdentityServer](https://identityserver.io/) pro scénáře ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1cde-130">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="a1cde-131">IdentityServer je výkonná a rozšiřitelná architektura pro zpracování otázek zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="a1cde-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="a1cde-132">IdentityServer zpřístupňuje zbytečné složitosti pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="a1cde-132">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="a1cde-133">V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="a1cde-133">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="a1cde-134">Jakmile se vaše potřeby ověřování změní, je k dispozici plná síla IdentityServer, která vám umožní přizpůsobit ověřování podle požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1cde-134">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="a1cde-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="a1cde-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="a1cde-136"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="a1cde-136">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="a1cde-137">Zásady jsou nakonfigurovány tak, aby umožňovaly Identity zpracování všech požadavků směrovaných na jakoukoli dílčí cestu v Identity prostoru URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="a1cde-137">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="a1cde-138"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Zpracovává všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="a1cde-138">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="a1cde-139">Tato metoda navíc:</span><span class="sxs-lookup"><span data-stu-id="a1cde-139">Additionally, this method:</span></span>

* <span data-ttu-id="a1cde-140">Zaregistruje `{APPLICATION NAME}API` prostředek rozhraní API s IdentityServer s výchozím oborem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="a1cde-140">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="a1cde-141">Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a1cde-141">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="a1cde-142">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="a1cde-142">WeatherForecastController</span></span>

<span data-ttu-id="a1cde-143">V rozhraní `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) je atribut použit pro třídu.</span><span class="sxs-lookup"><span data-stu-id="a1cde-143">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="a1cde-144">Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="a1cde-144">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="a1cde-145">Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené nástrojem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="a1cde-145">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="a1cde-146">Pomocná metoda se konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužná rutina pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a1cde-146">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="a1cde-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="a1cde-147">ApplicationDbContext</span></span>

<span data-ttu-id="a1cde-148">V rozhraní `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> rozšiřuje, <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> aby zahrnovalo schéma pro IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="a1cde-148">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="a1cde-149"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="a1cde-149"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="a1cde-150">Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity <xref:Microsoft.EntityFrameworkCore.DbContext> tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodě.</span><span class="sxs-lookup"><span data-stu-id="a1cde-150">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="a1cde-151">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="a1cde-151">OidcConfigurationController</span></span>

<span data-ttu-id="a1cde-152">V rozhraní `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) se koncový bod klienta zřídí pro poskytování OIDC parametrů.</span><span class="sxs-lookup"><span data-stu-id="a1cde-152">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="a1cde-153">Soubory nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="a1cde-153">App settings files</span></span>

<span data-ttu-id="a1cde-154">V souboru nastavení aplikace ( `appsettings.json` ) v kořenovém adresáři projektu `IdentityServer` obsahuje část seznam konfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="a1cde-154">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="a1cde-155">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="a1cde-155">In the following example, there's a single client.</span></span> <span data-ttu-id="a1cde-156">Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth.</span><span class="sxs-lookup"><span data-stu-id="a1cde-156">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="a1cde-157">Profil indikuje typ aplikace, která se konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="a1cde-157">The profile indicates the app type being configured.</span></span> <span data-ttu-id="a1cde-158">Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru.</span><span class="sxs-lookup"><span data-stu-id="a1cde-158">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="a1cde-159">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="a1cde-159">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="a1cde-160">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="a1cde-160">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a1cde-161">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="a1cde-161">Authentication package</span></span>

<span data-ttu-id="a1cde-162">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty ( `Individual` ), aplikace automaticky obdrží odkaz na balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1cde-162">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="a1cde-163">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a1cde-163">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a1cde-164">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="a1cde-164">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="a1cde-165">Podpora autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="a1cde-165">API authorization support</span></span>

<span data-ttu-id="a1cde-166">Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v rámci [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="a1cde-166">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="a1cde-167">Tato metoda nastaví služby, které aplikace požaduje, aby spolupracovaly se stávajícím autorizačním systémem.</span><span class="sxs-lookup"><span data-stu-id="a1cde-167">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="a1cde-168">Ve výchozím nastavení je konfigurace pro aplikaci načtena podle konvence z `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="a1cde-168">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="a1cde-169">Podle konvence je ID klienta nastaveno na název sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1cde-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="a1cde-170">Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.</span><span class="sxs-lookup"><span data-stu-id="a1cde-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="a1cde-171">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="a1cde-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a1cde-172">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="a1cde-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="a1cde-173">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="a1cde-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a1cde-174">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a1cde-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a1cde-175">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a1cde-175">LoginDisplay component</span></span>

<span data-ttu-id="a1cde-176">`LoginDisplay`Součást ( `Shared/LoginDisplay.razor` ) je vykreslena v `MainLayout` komponentě ( `Shared/MainLayout.razor` ) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="a1cde-176">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="a1cde-177">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="a1cde-177">For authenticated users:</span></span>
  * <span data-ttu-id="a1cde-178">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="a1cde-178">Displays the current user name.</span></span>
  * <span data-ttu-id="a1cde-179">Nabízí odkaz na stránku profil uživatele v ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="a1cde-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="a1cde-180">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1cde-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="a1cde-181">Pro anonymní uživatele:</span><span class="sxs-lookup"><span data-stu-id="a1cde-181">For anonymous users:</span></span>
  * <span data-ttu-id="a1cde-182">Nabízí možnost registrace.</span><span class="sxs-lookup"><span data-stu-id="a1cde-182">Offers the option to register.</span></span>
  * <span data-ttu-id="a1cde-183">Nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="a1cde-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="a1cde-184">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="a1cde-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a1cde-185">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="a1cde-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a1cde-186">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a1cde-186">Run the app</span></span>

<span data-ttu-id="a1cde-187">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="a1cde-187">Run the app from the Server project.</span></span> <span data-ttu-id="a1cde-188">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="a1cde-188">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a1cde-189">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="a1cde-189">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a1cde-190">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="a1cde-190">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="a1cde-191">Deklarace identity u názvů a rolí pomocí ověřování API</span><span class="sxs-lookup"><span data-stu-id="a1cde-191">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="a1cde-192">Vlastní továrna uživatelů</span><span class="sxs-lookup"><span data-stu-id="a1cde-192">Custom user factory</span></span>

<span data-ttu-id="a1cde-193">V klientské aplikaci vytvořte vlastní objekt pro vytváření uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a1cde-193">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="a1cde-194">Server odesílá v jedné deklaraci více rolí jako pole JSON `role` .</span><span class="sxs-lookup"><span data-stu-id="a1cde-194"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="a1cde-195">Jedna role se pošle jako řetězcová hodnota v deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="a1cde-195">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="a1cde-196">Továrna vytvoří jednotlivou `role` deklaraci identity pro každou roli uživatele.</span><span class="sxs-lookup"><span data-stu-id="a1cde-196">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="a1cde-197">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="a1cde-197">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="a1cde-198">V klientské aplikaci Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="a1cde-198">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="a1cde-199">V serverové aplikaci zavolejte <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> na Identity Tvůrce, který přidá služby související s rolemi:</span><span class="sxs-lookup"><span data-stu-id="a1cde-199">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="a1cde-200">Konfigurovat Identity Server</span><span class="sxs-lookup"><span data-stu-id="a1cde-200">Configure Identity Server</span></span>

<span data-ttu-id="a1cde-201">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a1cde-201">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="a1cde-202">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="a1cde-202">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="a1cde-203">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="a1cde-203">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="a1cde-204">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="a1cde-204">API authorization options</span></span>

<span data-ttu-id="a1cde-205">V serverové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="a1cde-205">In the Server app:</span></span>

* <span data-ttu-id="a1cde-206">Nakonfigurujte Identity Server tak, aby `name` uvedl `role` deklarace identity a do tokenu ID a přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="a1cde-206">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="a1cde-207">Zabrání výchozímu mapování rolí v obslužné rutině tokenu JWT.</span><span class="sxs-lookup"><span data-stu-id="a1cde-207">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="a1cde-208">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="a1cde-208">Profile Service</span></span>

<span data-ttu-id="a1cde-209">V serverové aplikaci vytvořte `ProfileService` implementaci.</span><span class="sxs-lookup"><span data-stu-id="a1cde-209">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="a1cde-210">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="a1cde-210">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="a1cde-211">V serverové aplikaci Zaregistrujte službu profilů v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a1cde-211">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="a1cde-212">Použití autorizačních mechanismů</span><span class="sxs-lookup"><span data-stu-id="a1cde-212">Use authorization mechanisms</span></span>

<span data-ttu-id="a1cde-213">V klientské aplikaci jsou v tuto chvíli funkční přístupy k komponentám autorizace.</span><span class="sxs-lookup"><span data-stu-id="a1cde-213">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="a1cde-214">Kterýkoli z autorizačních mechanismů v součástech může použít roli k autorizaci uživatele:</span><span class="sxs-lookup"><span data-stu-id="a1cde-214">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="a1cde-215">[ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="a1cde-215">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="a1cde-216">[ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="a1cde-216">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="a1cde-217">[Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="a1cde-217">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="a1cde-218">Podporuje se vícenásobné testy rolí:</span><span class="sxs-lookup"><span data-stu-id="a1cde-218">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="a1cde-219">`User.Identity.Name`se naplní v klientské aplikaci pomocí uživatelského jména uživatele, což je obvykle jejich přihlašovací e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="a1cde-219">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a1cde-220">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a1cde-220">Additional resources</span></span>

* [<span data-ttu-id="a1cde-221">Nasazení do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a1cde-221">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="a1cde-222">Import certifikátu z Key Vault (dokumentace k Azure)</span><span class="sxs-lookup"><span data-stu-id="a1cde-222">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a1cde-223">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="a1cde-223">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
