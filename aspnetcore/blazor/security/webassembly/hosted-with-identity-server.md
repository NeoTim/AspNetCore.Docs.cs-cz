---
title: Zabezpečení hostované Blazor WebAssembly aplikace v ASP.NET Core se Identity serverem
author: guardrex
description: Seznamte se s postupem zabezpečení hostované Blazor WebAssembly aplikace ASP.NET Core se Identity serverem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 58c21f4dbe831e99570ca8b0d7bc78616c1e5bfb
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712373"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="e42d6-103">Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core se Identity serverem</span><span class="sxs-lookup"><span data-stu-id="e42d6-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="e42d6-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e42d6-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e42d6-105">Tento článek vysvětluje, jak vytvořit [hostovanou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která používá [ Identity Server](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e42d6-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="e42d6-106">Pokud chcete samostatnou nebo hostovanou Blazor WebAssembly aplikaci nakonfigurovat tak, aby používala existující externí Identity instanci serveru, postupujte podle pokynů v části <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="e42d6-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e42d6-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e42d6-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e42d6-108">Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="e42d6-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="e42d6-109">Po výběru šablony \*\* Blazor WebAssembly aplikace\*\* v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="e42d6-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="e42d6-110">Vyberte **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty v aplikaci** pro ukládání uživatelů v aplikaci pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e42d6-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="e42d6-111">Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .</span><span class="sxs-lookup"><span data-stu-id="e42d6-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="e42d6-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e42d6-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="e42d6-113">Pokud chcete vytvořit nový Blazor WebAssembly projekt s mechanismem ověřování v prázdné složce, zadejte `Individual` ověřovací mechanismus s `-au|--auth` možností ukládat uživatele v rámci aplikace pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e42d6-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="e42d6-114">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="e42d6-114">Placeholder</span></span>  | <span data-ttu-id="e42d6-115">Příklad</span><span class="sxs-lookup"><span data-stu-id="e42d6-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="e42d6-116">Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="e42d6-117">Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e42d6-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e42d6-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e42d6-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e42d6-119">Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="e42d6-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="e42d6-120">V kroku **Konfigurace nového Blazor WebAssembly aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .</span><span class="sxs-lookup"><span data-stu-id="e42d6-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="e42d6-121">Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="e42d6-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="e42d6-122">Zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="e42d6-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="e42d6-123">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="e42d6-123">Server app configuration</span></span>

<span data-ttu-id="e42d6-124">Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.</span><span class="sxs-lookup"><span data-stu-id="e42d6-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="e42d6-125">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="e42d6-125">Startup class</span></span>

<span data-ttu-id="e42d6-126">`Startup`Třída obsahuje následující doplňky.</span><span class="sxs-lookup"><span data-stu-id="e42d6-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="e42d6-127">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e42d6-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="e42d6-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="e42d6-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="e42d6-129">IdentityServer s další <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnou metodou, která nastaví výchozí konvence ASP.NET Core na Identity serveru:</span><span class="sxs-lookup"><span data-stu-id="e42d6-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="e42d6-130">Ověřování s další <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnou metodou, která nakonfiguruje aplikaci k ověření tokenů JWT vyprodukovaných Identity serverem:</span><span class="sxs-lookup"><span data-stu-id="e42d6-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="e42d6-131">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e42d6-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="e42d6-132">IdentityMiddleware serveru zpřístupňuje koncové body OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="e42d6-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="e42d6-133">Middleware ověřování zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="e42d6-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="e42d6-134">Middleware autorizace povoluje možnosti autorizace:</span><span class="sxs-lookup"><span data-stu-id="e42d6-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="e42d6-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="e42d6-135">AddApiAuthorization</span></span>

<span data-ttu-id="e42d6-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Pomocná metoda konfiguruje [ Identity Server](https://identityserver.io/) pro ASP.NET Core scénáře.</span><span class="sxs-lookup"><span data-stu-id="e42d6-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="e42d6-137">IdentityServer je výkonné a rozšiřitelné rozhraní pro zpracování otázek zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="e42d6-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="e42d6-138">IdentityServer zveřejňuje nepotřebnou složitost pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="e42d6-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="e42d6-139">V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="e42d6-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="e42d6-140">Jakmile se vaše potřeby ověřování změní, Identity je k dispozici plná síla serveru pro přizpůsobení ověřování podle požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="e42d6-141">Přidat Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="e42d6-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="e42d6-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="e42d6-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="e42d6-143">Zásady jsou nakonfigurovány tak, aby umožňovaly Identity zpracování všech požadavků směrovaných na jakoukoli dílčí cestu v Identity prostoru URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="e42d6-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="e42d6-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Zpracovává všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="e42d6-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="e42d6-145">Tato metoda navíc:</span><span class="sxs-lookup"><span data-stu-id="e42d6-145">Additionally, this method:</span></span>

* <span data-ttu-id="e42d6-146">Zaregistruje `{APPLICATION NAME}API` prostředek rozhraní API se Identity serverem s výchozím oborem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="e42d6-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="e42d6-147">Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných Identity serverem pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e42d6-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="e42d6-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="e42d6-148">WeatherForecastController</span></span>

<span data-ttu-id="e42d6-149">V rozhraní `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) je atribut použit pro třídu.</span><span class="sxs-lookup"><span data-stu-id="e42d6-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="e42d6-150">Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="e42d6-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="e42d6-151">Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené nástrojem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="e42d6-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="e42d6-152">Pomocná metoda se konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužná rutina pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e42d6-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="e42d6-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="e42d6-153">ApplicationDbContext</span></span>

<span data-ttu-id="e42d6-154">V rozhraní `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) se <xref:Microsoft.EntityFrameworkCore.DbContext> rozšiřuje <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> na zahrnutí schématu pro Identity Server.</span><span class="sxs-lookup"><span data-stu-id="e42d6-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="e42d6-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="e42d6-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="e42d6-156">Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity <xref:Microsoft.EntityFrameworkCore.DbContext> tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodě.</span><span class="sxs-lookup"><span data-stu-id="e42d6-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="e42d6-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="e42d6-157">OidcConfigurationController</span></span>

<span data-ttu-id="e42d6-158">V rozhraní `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) se koncový bod klienta zřídí pro poskytování OIDC parametrů.</span><span class="sxs-lookup"><span data-stu-id="e42d6-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="e42d6-159">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="e42d6-159">App settings</span></span>

<span data-ttu-id="e42d6-160">V souboru nastavení aplikace ( `appsettings.json` ) v kořenovém adresáři projektu `IdentityServer` obsahuje část seznam konfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="e42d6-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="e42d6-161">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="e42d6-161">In the following example, there's a single client.</span></span> <span data-ttu-id="e42d6-162">Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth.</span><span class="sxs-lookup"><span data-stu-id="e42d6-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="e42d6-163">Profil indikuje typ aplikace, která se konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="e42d6-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="e42d6-164">Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru.</span><span class="sxs-lookup"><span data-stu-id="e42d6-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="e42d6-165">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="e42d6-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="e42d6-166">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="e42d6-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e42d6-167">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="e42d6-167">Authentication package</span></span>

<span data-ttu-id="e42d6-168">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty ( `Individual` ), aplikace automaticky obdrží odkaz na balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="e42d6-169">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e42d6-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e42d6-170">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="e42d6-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="e42d6-171">Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="e42d6-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="e42d6-172">`HttpClient` rozšířeného</span><span class="sxs-lookup"><span data-stu-id="e42d6-172">`HttpClient` configuration</span></span>

<span data-ttu-id="e42d6-173">V `Program.Main` ( `Program.cs` ) <xref:System.Net.Http.HttpClient> je pojmenovaný ( `HostIS.ServerAPI` ) nakonfigurovaný tak, aby poskytoval <xref:System.Net.Http.HttpClient> instance, které zahrnují přístupové tokeny při vytváření žádostí na rozhraní API serveru:</span><span class="sxs-lookup"><span data-stu-id="e42d6-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="e42d6-174">Pokud konfigurujete Blazor WebAssembly aplikaci tak, aby používala stávající Identity instanci serveru, která není součástí hostovaného Blazor řešení, změňte <xref:System.Net.Http.HttpClient> registraci základní adresy z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adresu URL koncového bodu rozhraní API serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="e42d6-175">Podpora autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e42d6-175">API authorization support</span></span>

<span data-ttu-id="e42d6-176">Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v rámci [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) balíčku.</span><span class="sxs-lookup"><span data-stu-id="e42d6-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="e42d6-177">Tato metoda nastaví služby, které aplikace požaduje, aby spolupracovaly se stávajícím autorizačním systémem.</span><span class="sxs-lookup"><span data-stu-id="e42d6-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="e42d6-178">Ve výchozím nastavení je konfigurace pro aplikaci načtena podle konvence z `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="e42d6-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="e42d6-179">Podle konvence je ID klienta nastaveno na název sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="e42d6-180">Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.</span><span class="sxs-lookup"><span data-stu-id="e42d6-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="e42d6-181">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="e42d6-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e42d6-182">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="e42d6-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="e42d6-183">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="e42d6-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e42d6-184">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e42d6-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e42d6-185">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e42d6-185">LoginDisplay component</span></span>

<span data-ttu-id="e42d6-186">`LoginDisplay`Součást ( `Shared/LoginDisplay.razor` ) je vykreslena v `MainLayout` komponentě ( `Shared/MainLayout.razor` ) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="e42d6-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="e42d6-187">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="e42d6-187">For authenticated users:</span></span>
  * <span data-ttu-id="e42d6-188">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="e42d6-188">Displays the current user name.</span></span>
  * <span data-ttu-id="e42d6-189">Nabízí odkaz na stránku profil uživatele v ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="e42d6-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="e42d6-190">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="e42d6-191">Pro anonymní uživatele:</span><span class="sxs-lookup"><span data-stu-id="e42d6-191">For anonymous users:</span></span>
  * <span data-ttu-id="e42d6-192">Nabízí možnost registrace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-192">Offers the option to register.</span></span>
  * <span data-ttu-id="e42d6-193">Nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="e42d6-193">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="e42d6-194">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="e42d6-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e42d6-195">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="e42d6-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e42d6-196">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e42d6-196">Run the app</span></span>

<span data-ttu-id="e42d6-197">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="e42d6-197">Run the app from the Server project.</span></span> <span data-ttu-id="e42d6-198">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="e42d6-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="e42d6-199">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="e42d6-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="e42d6-200">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="e42d6-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="e42d6-201">Deklarace identity u názvů a rolí pomocí ověřování API</span><span class="sxs-lookup"><span data-stu-id="e42d6-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="e42d6-202">Vlastní továrna uživatelů</span><span class="sxs-lookup"><span data-stu-id="e42d6-202">Custom user factory</span></span>

<span data-ttu-id="e42d6-203">V klientské aplikaci vytvořte vlastní objekt pro vytváření uživatelů.</span><span class="sxs-lookup"><span data-stu-id="e42d6-203">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="e42d6-204">Identity Server odesílá v jedné deklaraci více rolí jako pole JSON `role` .</span><span class="sxs-lookup"><span data-stu-id="e42d6-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="e42d6-205">Jedna role se pošle jako řetězcová hodnota v deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="e42d6-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="e42d6-206">Továrna vytvoří jednotlivou `role` deklaraci identity pro každou roli uživatele.</span><span class="sxs-lookup"><span data-stu-id="e42d6-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="e42d6-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="e42d6-207">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="e42d6-208">V klientské aplikaci Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="e42d6-208">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="e42d6-209">V serverové aplikaci zavolejte <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> na Identity Tvůrce, který přidá služby související s rolemi:</span><span class="sxs-lookup"><span data-stu-id="e42d6-209">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="e42d6-210">Konfigurovat Identity Server</span><span class="sxs-lookup"><span data-stu-id="e42d6-210">Configure Identity Server</span></span>

<span data-ttu-id="e42d6-211">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e42d6-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="e42d6-212">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e42d6-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="e42d6-213">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="e42d6-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="e42d6-214">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e42d6-214">API authorization options</span></span>

<span data-ttu-id="e42d6-215">V serverové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e42d6-215">In the Server app:</span></span>

* <span data-ttu-id="e42d6-216">Nakonfigurujte Identity Server tak, aby `name` uvedl `role` deklarace identity a do tokenu ID a přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="e42d6-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="e42d6-217">Zabrání výchozímu mapování rolí v obslužné rutině tokenu JWT.</span><span class="sxs-lookup"><span data-stu-id="e42d6-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="e42d6-218">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="e42d6-218">Profile Service</span></span>

<span data-ttu-id="e42d6-219">V serverové aplikaci vytvořte `ProfileService` implementaci.</span><span class="sxs-lookup"><span data-stu-id="e42d6-219">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="e42d6-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="e42d6-220">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="e42d6-221">V serverové aplikaci Zaregistrujte službu profilů v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e42d6-221">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="e42d6-222">Použití autorizačních mechanismů</span><span class="sxs-lookup"><span data-stu-id="e42d6-222">Use authorization mechanisms</span></span>

<span data-ttu-id="e42d6-223">V klientské aplikaci jsou v tuto chvíli funkční přístupy k komponentám autorizace.</span><span class="sxs-lookup"><span data-stu-id="e42d6-223">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="e42d6-224">Kterýkoli z autorizačních mechanismů v součástech může použít roli k autorizaci uživatele:</span><span class="sxs-lookup"><span data-stu-id="e42d6-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="e42d6-225">[ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="e42d6-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="e42d6-226">[ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="e42d6-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="e42d6-227">[Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="e42d6-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="e42d6-228">Podporuje se vícenásobné testy rolí:</span><span class="sxs-lookup"><span data-stu-id="e42d6-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="e42d6-229">`User.Identity.Name` se naplní v klientské aplikaci pomocí uživatelského jména uživatele, což je obvykle jejich přihlašovací e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="e42d6-229">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e42d6-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e42d6-230">Additional resources</span></span>

* [<span data-ttu-id="e42d6-231">Nasazení do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e42d6-231">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="e42d6-232">Import certifikátu z Key Vault (dokumentace k Azure)</span><span class="sxs-lookup"><span data-stu-id="e42d6-232">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="e42d6-233">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="e42d6-233">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
