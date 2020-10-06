---
title: Zabezpečení hostované Blazor WebAssembly aplikace v ASP.NET Core se Identity serverem
author: guardrex
description: Seznamte se s postupem zabezpečení hostované Blazor WebAssembly aplikace ASP.NET Core se Identity serverem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
ms.openlocfilehash: 91cc7ffc46f5f1f68efd7e481479b19938476cb0
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762240"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="70c79-103">Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core se Identity serverem</span><span class="sxs-lookup"><span data-stu-id="70c79-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="70c79-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70c79-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="70c79-105">Tento článek vysvětluje, jak vytvořit [hostovanou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která používá [ Identity Server](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="70c79-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="70c79-106">Pokud chcete samostatnou nebo hostovanou Blazor WebAssembly aplikaci nakonfigurovat tak, aby používala existující externí Identity instanci serveru, postupujte podle pokynů v části <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="70c79-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="70c79-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70c79-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="70c79-108">Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="70c79-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="70c79-109">Po výběru šablony \*\* Blazor WebAssembly aplikace\*\* v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="70c79-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="70c79-110">Vyberte **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty v aplikaci** pro ukládání uživatelů v aplikaci pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70c79-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="70c79-111">Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .</span><span class="sxs-lookup"><span data-stu-id="70c79-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="70c79-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="70c79-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="70c79-113">Pokud chcete vytvořit nový Blazor WebAssembly projekt s mechanismem ověřování v prázdné složce, zadejte `Individual` ověřovací mechanismus s `-au|--auth` možností ukládat uživatele v rámci aplikace pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="70c79-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="70c79-114">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="70c79-114">Placeholder</span></span>  | <span data-ttu-id="70c79-115">Příklad</span><span class="sxs-lookup"><span data-stu-id="70c79-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="70c79-116">Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="70c79-117">Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="70c79-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="70c79-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="70c79-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="70c79-119">Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="70c79-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="70c79-120">V kroku **Konfigurace nového Blazor WebAssembly aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .</span><span class="sxs-lookup"><span data-stu-id="70c79-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="70c79-121">Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="70c79-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="70c79-122">Zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="70c79-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="70c79-123">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="70c79-123">Server app configuration</span></span>

<span data-ttu-id="70c79-124">Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.</span><span class="sxs-lookup"><span data-stu-id="70c79-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="70c79-125">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="70c79-125">Startup class</span></span>

<span data-ttu-id="70c79-126">`Startup`Třída obsahuje následující doplňky.</span><span class="sxs-lookup"><span data-stu-id="70c79-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="70c79-127">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="70c79-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="70c79-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="70c79-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="70c79-129">IdentityServer s další <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnou metodou, která nastaví výchozí konvence ASP.NET Core na Identity serveru:</span><span class="sxs-lookup"><span data-stu-id="70c79-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="70c79-130">Ověřování s další <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnou metodou, která nakonfiguruje aplikaci k ověření tokenů JWT vyprodukovaných Identity serverem:</span><span class="sxs-lookup"><span data-stu-id="70c79-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="70c79-131">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="70c79-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="70c79-132">IdentityMiddleware serveru zpřístupňuje koncové body OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="70c79-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="70c79-133">Middleware ověřování zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="70c79-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="70c79-134">Middleware autorizace povoluje možnosti autorizace:</span><span class="sxs-lookup"><span data-stu-id="70c79-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="70c79-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="70c79-135">AddApiAuthorization</span></span>

<span data-ttu-id="70c79-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Pomocná metoda konfiguruje [ Identity Server](https://identityserver.io/) pro ASP.NET Core scénáře.</span><span class="sxs-lookup"><span data-stu-id="70c79-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="70c79-137">IdentityServer je výkonné a rozšiřitelné rozhraní pro zpracování otázek zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="70c79-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="70c79-138">IdentityServer zveřejňuje nepotřebnou složitost pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="70c79-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="70c79-139">V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="70c79-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="70c79-140">Jakmile se vaše potřeby ověřování změní, Identity je k dispozici plná síla serveru pro přizpůsobení ověřování podle požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="70c79-141">Přidat Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="70c79-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="70c79-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="70c79-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="70c79-143">Zásady jsou nakonfigurovány tak, aby umožňovaly Identity zpracování všech požadavků směrovaných na jakoukoli dílčí cestu v Identity prostoru URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="70c79-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="70c79-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Zpracovává všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="70c79-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="70c79-145">Tato metoda navíc:</span><span class="sxs-lookup"><span data-stu-id="70c79-145">Additionally, this method:</span></span>

* <span data-ttu-id="70c79-146">Zaregistruje `{APPLICATION NAME}API` prostředek rozhraní API se Identity serverem s výchozím oborem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="70c79-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="70c79-147">Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných Identity serverem pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70c79-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="70c79-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="70c79-148">WeatherForecastController</span></span>

<span data-ttu-id="70c79-149">V rozhraní `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) je atribut použit pro třídu.</span><span class="sxs-lookup"><span data-stu-id="70c79-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="70c79-150">Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="70c79-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="70c79-151">Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené nástrojem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="70c79-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="70c79-152">Pomocná metoda se konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužná rutina pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70c79-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="70c79-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="70c79-153">ApplicationDbContext</span></span>

<span data-ttu-id="70c79-154">V rozhraní `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) se <xref:Microsoft.EntityFrameworkCore.DbContext> rozšiřuje <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> na zahrnutí schématu pro Identity Server.</span><span class="sxs-lookup"><span data-stu-id="70c79-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="70c79-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="70c79-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="70c79-156">Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity <xref:Microsoft.EntityFrameworkCore.DbContext> tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodě.</span><span class="sxs-lookup"><span data-stu-id="70c79-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="70c79-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="70c79-157">OidcConfigurationController</span></span>

<span data-ttu-id="70c79-158">V rozhraní `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) se koncový bod klienta zřídí pro poskytování OIDC parametrů.</span><span class="sxs-lookup"><span data-stu-id="70c79-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="70c79-159">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="70c79-159">App settings</span></span>

<span data-ttu-id="70c79-160">V souboru nastavení aplikace ( `appsettings.json` ) v kořenovém adresáři projektu `IdentityServer` obsahuje část seznam konfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="70c79-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="70c79-161">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="70c79-161">In the following example, there's a single client.</span></span> <span data-ttu-id="70c79-162">Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth.</span><span class="sxs-lookup"><span data-stu-id="70c79-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="70c79-163">Profil indikuje typ aplikace, která se konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="70c79-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="70c79-164">Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru.</span><span class="sxs-lookup"><span data-stu-id="70c79-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="70c79-165">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="70c79-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="70c79-166">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="70c79-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="70c79-167">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="70c79-167">Authentication package</span></span>

<span data-ttu-id="70c79-168">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty ( `Individual` ), aplikace automaticky obdrží odkaz na balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="70c79-169">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="70c79-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="70c79-170">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="70c79-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="70c79-171">Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="70c79-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="70c79-172">`HttpClient` rozšířeného</span><span class="sxs-lookup"><span data-stu-id="70c79-172">`HttpClient` configuration</span></span>

<span data-ttu-id="70c79-173">V `Program.Main` ( `Program.cs` ) <xref:System.Net.Http.HttpClient> je pojmenovaný ( `HostIS.ServerAPI` ) nakonfigurovaný tak, aby poskytoval <xref:System.Net.Http.HttpClient> instance, které zahrnují přístupové tokeny při vytváření žádostí na rozhraní API serveru:</span><span class="sxs-lookup"><span data-stu-id="70c79-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="70c79-174">Pokud konfigurujete Blazor WebAssembly aplikaci tak, aby používala stávající Identity instanci serveru, která není součástí hostovaného Blazor řešení, změňte <xref:System.Net.Http.HttpClient> registraci základní adresy z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adresu URL koncového bodu rozhraní API serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="70c79-175">Podpora autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="70c79-175">API authorization support</span></span>

<span data-ttu-id="70c79-176">Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v rámci [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) balíčku.</span><span class="sxs-lookup"><span data-stu-id="70c79-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="70c79-177">Tato metoda nastaví služby, které aplikace požaduje, aby spolupracovaly se stávajícím autorizačním systémem.</span><span class="sxs-lookup"><span data-stu-id="70c79-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="70c79-178">Ve výchozím nastavení je konfigurace pro aplikaci načtena podle konvence z `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="70c79-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="70c79-179">Podle konvence je ID klienta nastaveno na název sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="70c79-180">Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.</span><span class="sxs-lookup"><span data-stu-id="70c79-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="70c79-181">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="70c79-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="70c79-182">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="70c79-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="70c79-183">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="70c79-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="70c79-184">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="70c79-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="70c79-185">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="70c79-185">LoginDisplay component</span></span>

<span data-ttu-id="70c79-186">`LoginDisplay`Součást ( `Shared/LoginDisplay.razor` ) je vykreslena v `MainLayout` komponentě ( `Shared/MainLayout.razor` ) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="70c79-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="70c79-187">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="70c79-187">For authenticated users:</span></span>
  * <span data-ttu-id="70c79-188">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="70c79-188">Displays the current user name.</span></span>
  * <span data-ttu-id="70c79-189">Nabízí odkaz na stránku profil uživatele v ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="70c79-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="70c79-190">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="70c79-191">Pro anonymní uživatele:</span><span class="sxs-lookup"><span data-stu-id="70c79-191">For anonymous users:</span></span>
  * <span data-ttu-id="70c79-192">Nabízí možnost registrace.</span><span class="sxs-lookup"><span data-stu-id="70c79-192">Offers the option to register.</span></span>
  * <span data-ttu-id="70c79-193">Nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="70c79-193">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="70c79-194">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="70c79-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="70c79-195">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="70c79-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="70c79-196">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="70c79-196">Run the app</span></span>

<span data-ttu-id="70c79-197">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="70c79-197">Run the app from the Server project.</span></span> <span data-ttu-id="70c79-198">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="70c79-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="70c79-199">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="70c79-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="70c79-200">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="70c79-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="70c79-201">Deklarace identity u názvů a rolí pomocí ověřování API</span><span class="sxs-lookup"><span data-stu-id="70c79-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="70c79-202">Vlastní továrna uživatelů</span><span class="sxs-lookup"><span data-stu-id="70c79-202">Custom user factory</span></span>

<span data-ttu-id="70c79-203">V klientské aplikaci vytvořte vlastní objekt pro vytváření uživatelů.</span><span class="sxs-lookup"><span data-stu-id="70c79-203">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="70c79-204">Identity Server odesílá v jedné deklaraci více rolí jako pole JSON `role` .</span><span class="sxs-lookup"><span data-stu-id="70c79-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="70c79-205">Jedna role se pošle jako řetězcová hodnota v deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="70c79-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="70c79-206">Továrna vytvoří jednotlivou `role` deklaraci identity pro každou roli uživatele.</span><span class="sxs-lookup"><span data-stu-id="70c79-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="70c79-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="70c79-207">`CustomUserFactory.cs`:</span></span>

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
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

<span data-ttu-id="70c79-208">V klientské aplikaci Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="70c79-208">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="70c79-209">V serverové aplikaci zavolejte <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> na Identity Tvůrce, který přidá služby související s rolemi:</span><span class="sxs-lookup"><span data-stu-id="70c79-209">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="70c79-210">Konfigurovat Identity Server</span><span class="sxs-lookup"><span data-stu-id="70c79-210">Configure Identity Server</span></span>

<span data-ttu-id="70c79-211">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="70c79-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="70c79-212">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="70c79-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="70c79-213">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="70c79-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="70c79-214">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="70c79-214">API authorization options</span></span>

<span data-ttu-id="70c79-215">V serverové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="70c79-215">In the Server app:</span></span>

* <span data-ttu-id="70c79-216">Nakonfigurujte Identity Server tak, aby `name` uvedl `role` deklarace identity a do tokenu ID a přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="70c79-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="70c79-217">Zabrání výchozímu mapování rolí v obslužné rutině tokenu JWT.</span><span class="sxs-lookup"><span data-stu-id="70c79-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="70c79-218">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="70c79-218">Profile Service</span></span>

<span data-ttu-id="70c79-219">V serverové aplikaci vytvořte `ProfileService` implementaci.</span><span class="sxs-lookup"><span data-stu-id="70c79-219">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="70c79-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="70c79-220">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="70c79-221">V serverové aplikaci Zaregistrujte službu profilů v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="70c79-221">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="70c79-222">Použití autorizačních mechanismů</span><span class="sxs-lookup"><span data-stu-id="70c79-222">Use authorization mechanisms</span></span>

<span data-ttu-id="70c79-223">V klientské aplikaci jsou v tuto chvíli funkční přístupy k komponentám autorizace.</span><span class="sxs-lookup"><span data-stu-id="70c79-223">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="70c79-224">Kterýkoli z autorizačních mechanismů v součástech může použít roli k autorizaci uživatele:</span><span class="sxs-lookup"><span data-stu-id="70c79-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="70c79-225">[ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="70c79-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="70c79-226">[ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="70c79-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="70c79-227">[Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="70c79-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="70c79-228">Podporuje se vícenásobné testy rolí:</span><span class="sxs-lookup"><span data-stu-id="70c79-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="70c79-229">`User.Identity.Name` se naplní v klientské aplikaci pomocí uživatelského jména uživatele, což je obvykle jejich přihlašovací e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="70c79-229">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="70c79-230">Hostování v Azure App Service s vlastní doménou</span><span class="sxs-lookup"><span data-stu-id="70c79-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="70c79-231">Následující pokyny popisují, jak nasadit hostovanou Blazor WebAssembly aplikaci se Identity serverem pro [Azure App Service](https://azure.microsoft.com/services/app-service/) s vlastní doménou.</span><span class="sxs-lookup"><span data-stu-id="70c79-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="70c79-232">Pro tento scénář hostování **nepoužívejte stejný** certifikát pro [ Identity podpisový klíč tokenu serveru](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) a zabezpečenou komunikaci protokolu HTTPS s prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="70c79-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="70c79-233">Používání různých certifikátů pro tyto dvě požadavky je dobrým zvykem zabezpečení, protože pro každý účel izoluje privátní klíče.</span><span class="sxs-lookup"><span data-stu-id="70c79-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="70c79-234">Certifikáty TLS pro komunikaci s prohlížeči se spravují nezávisle, aniž by to ovlivnilo Identity podepisování tokenů serveru.</span><span class="sxs-lookup"><span data-stu-id="70c79-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="70c79-235">Když [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) poskytne certifikát do App Service aplikace pro vlastní doménovou vazbu, Identity Server nemůže získat stejný certifikát od Azure Key Vault pro podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="70c79-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="70c79-236">I když Identity je možné nakonfigurovat server tak, aby používal stejný certifikát TLS z fyzické cesty, je vkládání certifikátů zabezpečení do správy zdrojového kódu **špatným postupem a mělo by se ve většině případů vyhnout**.</span><span class="sxs-lookup"><span data-stu-id="70c79-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="70c79-237">V následujících pokynech se certifikát podepsaný svým držitelem vytvoří v Azure Key Vault výhradně pro Identity Podepisování tokenu serveru.</span><span class="sxs-lookup"><span data-stu-id="70c79-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="70c79-238">IdentityKonfigurace serveru používá certifikát trezoru klíčů prostřednictvím `My`  >  `CurrentUser` úložiště certifikátů aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="70c79-239">Další certifikáty, které se používají pro přenosy HTTPS s vlastními doménami, se vytvářejí a konfigurují odděleně od Identity podpisového certifikátu serveru.</span><span class="sxs-lookup"><span data-stu-id="70c79-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="70c79-240">Konfigurace aplikace, Azure App Service a Azure Key Vault pro hostování s vlastní doménou a HTTPS:</span><span class="sxs-lookup"><span data-stu-id="70c79-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="70c79-241">Vytvořte [plán App Service](/azure/app-service/overview-hosting-plans) s úrovní plánu `Basic B1` nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="70c79-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="70c79-242">App Service vyžaduje, `Basic B1` aby úroveň služby nebo vyšší používala vlastní domény.</span><span class="sxs-lookup"><span data-stu-id="70c79-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="70c79-243">Vytvořte certifikát PFX pro komunikaci zabezpečeného prohlížeče (protokol HTTPS) lokality s běžným názvem plně kvalifikovaného názvu domény (FQDN) webu, který vaše organizace řídí (například `www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="70c79-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="70c79-244">Vytvořte certifikát pomocí:</span><span class="sxs-lookup"><span data-stu-id="70c79-244">Create the certificate with:</span></span>
   * <span data-ttu-id="70c79-245">Použití klíče</span><span class="sxs-lookup"><span data-stu-id="70c79-245">Key uses</span></span>
     * <span data-ttu-id="70c79-246">Ověření digitálního podpisu ( `digitalSignature` )</span><span class="sxs-lookup"><span data-stu-id="70c79-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="70c79-247">Šifrování klíče ( `keyEncipherment` )</span><span class="sxs-lookup"><span data-stu-id="70c79-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="70c79-248">Rozšířené/rozšířené použití klíče</span><span class="sxs-lookup"><span data-stu-id="70c79-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="70c79-249">Ověřování klientů (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="70c79-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="70c79-250">Ověřování serveru (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="70c79-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="70c79-251">Chcete-li vytvořit certifikát, použijte jeden z následujících přístupů nebo jakýkoli jiný vhodný nástroj nebo online službu:</span><span class="sxs-lookup"><span data-stu-id="70c79-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="70c79-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="70c79-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="70c79-253">MakeCert ve Windows</span><span class="sxs-lookup"><span data-stu-id="70c79-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="70c79-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="70c79-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="70c79-255">Poznamenejte si heslo, které se použije později k importu certifikátu do Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="70c79-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="70c79-256">Další informace o Azure Key Vaultch certifikátech najdete v článku [Azure Key Vault: certifikáty](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="70c79-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="70c79-257">Vytvořte novou Azure Key Vault nebo použijte existující Trezor klíčů v předplatném Azure.</span><span class="sxs-lookup"><span data-stu-id="70c79-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="70c79-258">V oblasti **certifikáty** trezoru klíčů importujte certifikát webu PFX.</span><span class="sxs-lookup"><span data-stu-id="70c79-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="70c79-259">Poznamenejte si kryptografický otisk certifikátu, který se v konfiguraci aplikace používá později.</span><span class="sxs-lookup"><span data-stu-id="70c79-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="70c79-260">V Azure Key Vault Vygenerujte nový certifikát podepsaný svým držitelem pro Identity Podepisování tokenu serveru.</span><span class="sxs-lookup"><span data-stu-id="70c79-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="70c79-261">Zadejte **název** certifikátu a **Předmět**.</span><span class="sxs-lookup"><span data-stu-id="70c79-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="70c79-262">**Předmět** je určen jako `CN={COMMON NAME}` , kde `{COMMON NAME}` zástupný symbol je běžný název certifikátu.</span><span class="sxs-lookup"><span data-stu-id="70c79-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="70c79-263">Běžným názvem může být libovolný alfanumerický řetězec.</span><span class="sxs-lookup"><span data-stu-id="70c79-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="70c79-264">Například `CN=IdentityServerSigning` je platný **Předmět**certifikátu.</span><span class="sxs-lookup"><span data-stu-id="70c79-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="70c79-265">Použijte výchozí **Rozšířená nastavení konfigurace zásad** .</span><span class="sxs-lookup"><span data-stu-id="70c79-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="70c79-266">Poznamenejte si kryptografický otisk certifikátu, který se v konfiguraci aplikace používá později.</span><span class="sxs-lookup"><span data-stu-id="70c79-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="70c79-267">V Azure Portal přejděte na Azure App Service a vytvořte novou App Service s následující konfigurací:</span><span class="sxs-lookup"><span data-stu-id="70c79-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="70c79-268">**Publikování** je nastaveno na `Code` .</span><span class="sxs-lookup"><span data-stu-id="70c79-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="70c79-269">**Zásobník modulu runtime** nastavený na modul runtime aplikace</span><span class="sxs-lookup"><span data-stu-id="70c79-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="70c79-270">V poli **SKU a velikost**potvrďte, že je úroveň App Service `Basic B1` nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="70c79-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="70c79-271">App Service vyžaduje, `Basic B1` aby úroveň služby nebo vyšší používala vlastní domény.</span><span class="sxs-lookup"><span data-stu-id="70c79-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="70c79-272">Jakmile Azure vytvoří App Service, otevřete **konfiguraci** aplikace a přidejte nové nastavení aplikace s určením kryptografických otisků certifikátů, které jste si poznamenali dříve.</span><span class="sxs-lookup"><span data-stu-id="70c79-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="70c79-273">Klíč nastavení aplikace je `WEBSITE_LOAD_CERTIFICATES` .</span><span class="sxs-lookup"><span data-stu-id="70c79-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="70c79-274">Kryptografické otisky certifikátů v hodnotě nastavení aplikace oddělte čárkou, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="70c79-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="70c79-275">Klíč: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="70c79-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="70c79-276">Hodnota: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="70c79-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="70c79-277">V Azure Portal je ukládání nastavení aplikace dvoustupňový proces: uložte `WEBSITE_LOAD_CERTIFICATES` nastavení klíč-hodnota a potom v horní části okna vyberte tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="70c79-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="70c79-278">Vyberte **Nastavení TLS/SSL**aplikace.</span><span class="sxs-lookup"><span data-stu-id="70c79-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="70c79-279">Vyberte **certifikáty privátních klíčů (. pfx)**.</span><span class="sxs-lookup"><span data-stu-id="70c79-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="70c79-280">Dvakrát pomocí procesu **import Key Vault certifikátů** importujte certifikát webu pro komunikaci pomocí protokolu HTTPS a Identity podpisový certifikát tokenu serveru podepsaného svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="70c79-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="70c79-281">Přejděte do okna **vlastní domény** .</span><span class="sxs-lookup"><span data-stu-id="70c79-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="70c79-282">Na webu vašeho doménového registrátora nakonfigurujte doménu pomocí **IP adresy** a **ID ověření vlastní domény** .</span><span class="sxs-lookup"><span data-stu-id="70c79-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="70c79-283">Typická konfigurace domény zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="70c79-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="70c79-284">**Záznam** a s **hostitelem** `@` a hodnotou IP adresy z Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="70c79-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="70c79-285">**Záznam TXT** s **hostitelem** `asuid` a hodnotou identifikátoru ověřování vygenerovaných Azure a poskytovaný Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="70c79-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="70c79-286">Ujistěte se, že jste změny uložili na webu vašeho registrátora domény správně.</span><span class="sxs-lookup"><span data-stu-id="70c79-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="70c79-287">Některé weby registrátora vyžadují dvoustupňový proces ukládání záznamů v doméně: jeden nebo více záznamů se ukládá jednotlivě a pomocí samostatného tlačítka aktualizují registraci domény.</span><span class="sxs-lookup"><span data-stu-id="70c79-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="70c79-288">Vraťte se do okna **vlastní domény** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="70c79-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="70c79-289">Vyberte **Přidat vlastní doménu**.</span><span class="sxs-lookup"><span data-stu-id="70c79-289">Select **Add custom domain**.</span></span> <span data-ttu-id="70c79-290">Vyberte možnost **záznam A** .</span><span class="sxs-lookup"><span data-stu-id="70c79-290">Select the **A Record** option.</span></span> <span data-ttu-id="70c79-291">Zadejte doménu a vyberte **ověřit**.</span><span class="sxs-lookup"><span data-stu-id="70c79-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="70c79-292">Pokud jsou záznamy v doméně správné a šířené přes Internet, portál vám umožní vybrat tlačítko **Přidat vlastní doménu** .</span><span class="sxs-lookup"><span data-stu-id="70c79-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="70c79-293">Může trvat několik dní, než se změny v registraci domény šíří v rámci služby DNS (Internet Domain Name Server) po jejich zpracování doménovým registrátorem.</span><span class="sxs-lookup"><span data-stu-id="70c79-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="70c79-294">Pokud se záznamy v doméně během tří pracovních dnů neaktualizují, potvrďte, že záznamy jsou správně nastavené s doménovým registrátorem, a obraťte se na zákaznickou podporu.</span><span class="sxs-lookup"><span data-stu-id="70c79-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="70c79-295">V okně **vlastní domény** je **stav protokolu SSL** pro danou doménu označený `Not Secure` .</span><span class="sxs-lookup"><span data-stu-id="70c79-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="70c79-296">Vyberte odkaz **Přidat vazbu** .</span><span class="sxs-lookup"><span data-stu-id="70c79-296">Select the **Add binding** link.</span></span> <span data-ttu-id="70c79-297">Vyberte certifikát HTTPS lokality z trezoru klíčů pro vlastní doménovou vazbu.</span><span class="sxs-lookup"><span data-stu-id="70c79-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="70c79-298">V aplikaci Visual Studio otevřete soubor nastavení aplikace v projektu *serveru* ( `appsettings.json` nebo `appsettings.Production.json` ).</span><span class="sxs-lookup"><span data-stu-id="70c79-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="70c79-299">V Identity konfiguraci serveru přidejte následující `Key` část.</span><span class="sxs-lookup"><span data-stu-id="70c79-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="70c79-300">Zadejte **Předmět** certifikátu podepsaného svým držitelem pro `Name` klíč.</span><span class="sxs-lookup"><span data-stu-id="70c79-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="70c79-301">V následujícím příkladu je běžný název certifikátu přiřazený v trezoru klíčů `IdentityServerSigning` , což má **za** následek `CN=IdentityServerSigning` :</span><span class="sxs-lookup"><span data-stu-id="70c79-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="70c79-302">V aplikaci Visual Studio vytvořte Azure App Service [profil publikování](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) pro *serverový* projekt.</span><span class="sxs-lookup"><span data-stu-id="70c79-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="70c79-303">V řádku nabídek vyberte: **sestavit**  >  **publikovat**  >  **novou**službu  >  **Azure**  >  **Azure App Service** (Windows nebo Linux).</span><span class="sxs-lookup"><span data-stu-id="70c79-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="70c79-304">Když je sada Visual Studio připojená k předplatnému Azure, můžete si nastavit **zobrazení** prostředků Azure podle **typu prostředku**.</span><span class="sxs-lookup"><span data-stu-id="70c79-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="70c79-305">Přejděte v seznamu **webové aplikace** , vyhledejte App Service pro aplikaci a vyberte ji.</span><span class="sxs-lookup"><span data-stu-id="70c79-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="70c79-306">Vyberte **Dokončit**.</span><span class="sxs-lookup"><span data-stu-id="70c79-306">Select **Finish**.</span></span>
1. <span data-ttu-id="70c79-307">Když se Visual Studio vrátí do okna **publikovat** , automaticky se zjistí závislosti trezoru klíčů a SQL serverch databázových služeb.</span><span class="sxs-lookup"><span data-stu-id="70c79-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="70c79-308">Pro službu trezoru klíčů se nevyžadují žádné změny konfigurace výchozích nastavení.</span><span class="sxs-lookup"><span data-stu-id="70c79-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="70c79-309">Pro účely testování je možné nasadit místní databázi [SQLite](https://www.sqlite.org/index.html) aplikace, která je nakonfigurovaná ve výchozím nastavení Blazor šablonou, a to bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="70c79-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="70c79-310">Konfigurace jiné databáze pro Identity Server v produkčním prostředí je mimo rámec tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="70c79-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="70c79-311">Další informace najdete v tématu prostředky databáze v následujících sadách dokumentace:</span><span class="sxs-lookup"><span data-stu-id="70c79-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="70c79-312">App Service</span><span class="sxs-lookup"><span data-stu-id="70c79-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="70c79-313">Identity WebServer</span><span class="sxs-lookup"><span data-stu-id="70c79-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="70c79-314">V části název profilu nasazení v horní části okna vyberte odkaz **Upravit** .</span><span class="sxs-lookup"><span data-stu-id="70c79-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="70c79-315">Změňte cílovou adresu URL na adresu URL vlastní domény webu (například `https://www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="70c79-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="70c79-316">Uložte nastavení.</span><span class="sxs-lookup"><span data-stu-id="70c79-316">Save the settings.</span></span>
1. <span data-ttu-id="70c79-317">Publikujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70c79-317">Publish the app.</span></span> <span data-ttu-id="70c79-318">Visual Studio otevře okno prohlížeče a požádá ho o web ve své vlastní doméně.</span><span class="sxs-lookup"><span data-stu-id="70c79-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="70c79-319">Dokumentace k Azure obsahuje další podrobnosti o používání služeb Azure a vlastních domén s vazbou TLS v App Service, včetně informací o použití záznamů CNAME namísto záznamů.</span><span class="sxs-lookup"><span data-stu-id="70c79-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="70c79-320">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="70c79-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="70c79-321">Dokumentace k App Service</span><span class="sxs-lookup"><span data-stu-id="70c79-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="70c79-322">Kurz: mapování stávajícího vlastního názvu DNS na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="70c79-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="70c79-323">Zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="70c79-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="70c79-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="70c79-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="70c79-325">Pro každý testovací běh aplikace po změně aplikace, konfigurace aplikace nebo služeb Azure v Azure Portal doporučujeme použít nové soukromé nebo anonymním okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="70c79-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="70c79-326">cookiePři záchodu s z předchozího testovacího běhu může dojít k selhání ověřování nebo autorizaci při testování lokality i v případě, že je Konfigurace lokality správná.</span><span class="sxs-lookup"><span data-stu-id="70c79-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="70c79-327">Další informace o tom, jak nakonfigurovat aplikaci Visual Studio tak, aby otevřela nové v soukromém nebo anonymním okně prohlížeče pro každý testovací běh, najdete v části [ Cookie s a daty o webu](#cookies-and-site-data) .</span><span class="sxs-lookup"><span data-stu-id="70c79-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="70c79-328">Když se v Azure Portal změní konfigurace App Service, obecně se tyto aktualizace projeví rychleji, ale ne okamžitě.</span><span class="sxs-lookup"><span data-stu-id="70c79-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="70c79-329">V některých případech je třeba počkat krátce App Service restartováním, aby se změna konfigurace projevila.</span><span class="sxs-lookup"><span data-stu-id="70c79-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="70c79-330">Pokud řešíte problém s načtením certifikátu, spusťte v prostředí příkazového řádku PowerShellu Azure Portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) následující příkaz.</span><span class="sxs-lookup"><span data-stu-id="70c79-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="70c79-331">Příkaz zobrazí seznam certifikátů, ke kterým může aplikace přistupovat z `My`  >  `CurrentUser` úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="70c79-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="70c79-332">Výstup zahrnuje předměty certifikátů a kryptografické otisky užitečné při ladění aplikace:</span><span class="sxs-lookup"><span data-stu-id="70c79-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="70c79-333">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="70c79-333">Additional resources</span></span>

* [<span data-ttu-id="70c79-334">Nasazení do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="70c79-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="70c79-335">Import certifikátu z Key Vault (dokumentace k Azure)</span><span class="sxs-lookup"><span data-stu-id="70c79-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="70c79-336">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="70c79-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
