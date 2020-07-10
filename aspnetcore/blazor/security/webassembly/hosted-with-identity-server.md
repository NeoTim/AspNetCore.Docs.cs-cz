---
title: Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core se Identity serverem
author: guardrex
description: Vytvoření nové Blazor hostované aplikace s ověřováním z aplikace Visual Studio, která používá back-end [ Identity serveru](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 1e5b4e37acd11280ec41c137426ecc4776d231be
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176248"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="75c36-103">Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core se Identity serverem</span><span class="sxs-lookup"><span data-stu-id="75c36-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="75c36-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="75c36-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="75c36-105">Tento článek vysvětluje, jak vytvořit novou Blazor hostovanou aplikaci, která používá [ Identity Server](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="75c36-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="75c36-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="75c36-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="75c36-107">Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="75c36-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="75c36-108">Po výběru šablony \*\* Blazor WebAssembly aplikace\*\* v dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** vyberte v části **ověřování**možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="75c36-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="75c36-109">Vyberte **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty v aplikaci** pro ukládání uživatelů v aplikaci pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="75c36-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="75c36-110">Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .</span><span class="sxs-lookup"><span data-stu-id="75c36-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="75c36-111">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="75c36-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="75c36-112">Pokud chcete vytvořit nový Blazor WebAssembly projekt s mechanismem ověřování v prázdné složce, zadejte `Individual` ověřovací mechanismus s `-au|--auth` možností ukládat uživatele v rámci aplikace pomocí [Identity](xref:security/authentication/identity) systému ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="75c36-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="75c36-113">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="75c36-113">Placeholder</span></span>  | <span data-ttu-id="75c36-114">Příklad</span><span class="sxs-lookup"><span data-stu-id="75c36-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="75c36-115">Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="75c36-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="75c36-116">Další informace najdete v tématu [`dotnet new`](/dotnet/core/tools/dotnet-new) Průvodce .NET Core.</span><span class="sxs-lookup"><span data-stu-id="75c36-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="75c36-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="75c36-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="75c36-118">Vytvoření nového Blazor WebAssembly projektu s mechanismem ověřování:</span><span class="sxs-lookup"><span data-stu-id="75c36-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="75c36-119">V kroku **Konfigurace nového Blazor WebAssembly aplikace** vyberte v rozevíracím seznamu **ověřování** možnost **individuální ověřování (v aplikaci)** .</span><span class="sxs-lookup"><span data-stu-id="75c36-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="75c36-120">Aplikace se vytvoří pro jednotlivé uživatele uložené v aplikaci pomocí ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="75c36-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="75c36-121">Zaškrtněte políčko **ASP.NET Core hostované** .</span><span class="sxs-lookup"><span data-stu-id="75c36-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="75c36-122">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="75c36-122">Server app configuration</span></span>

<span data-ttu-id="75c36-123">Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.</span><span class="sxs-lookup"><span data-stu-id="75c36-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="75c36-124">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="75c36-124">Startup class</span></span>

<span data-ttu-id="75c36-125">`Startup`Třída obsahuje následující doplňky.</span><span class="sxs-lookup"><span data-stu-id="75c36-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="75c36-126">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="75c36-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="75c36-127">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="75c36-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity<span data-ttu-id="75c36-128">Server s další <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnou metodou, která nastaví výchozí konvence ASP.NET Core na Identity serveru:</span><span class="sxs-lookup"><span data-stu-id="75c36-128">Server with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="75c36-129">Ověřování s další <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnou metodou, která nakonfiguruje aplikaci k ověření tokenů JWT vyprodukovaných Identity serverem:</span><span class="sxs-lookup"><span data-stu-id="75c36-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="75c36-130">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="75c36-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="75c36-131">IdentityMiddleware serveru zpřístupňuje koncové body otevřeného ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="75c36-131">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="75c36-132">Middleware ověřování zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="75c36-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="75c36-133">Middleware autorizace povoluje možnosti autorizace:</span><span class="sxs-lookup"><span data-stu-id="75c36-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="75c36-134">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="75c36-134">AddApiAuthorization</span></span>

<span data-ttu-id="75c36-135"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Pomocná metoda konfiguruje [ Identity Server](https://identityserver.io/) pro ASP.NET Core scénáře.</span><span class="sxs-lookup"><span data-stu-id="75c36-135">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> Identity<span data-ttu-id="75c36-136">Server je výkonné a rozšiřitelné rozhraní pro zpracování otázek zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="75c36-136">Server is a powerful and extensible framework for handling app security concerns.</span></span> Identity<span data-ttu-id="75c36-137">Server zveřejňuje nepotřebnou složitost pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="75c36-137">Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="75c36-138">V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="75c36-138">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="75c36-139">Jakmile se vaše potřeby ověřování změní, Identity je k dispozici plná síla serveru pro přizpůsobení ověřování podle požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="75c36-139">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="75c36-140">Přidat Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="75c36-140">AddIdentityServerJwt</span></span>

<span data-ttu-id="75c36-141"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="75c36-141">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="75c36-142">Zásady jsou nakonfigurovány tak, aby umožňovaly Identity zpracování všech požadavků směrovaných na jakoukoli dílčí cestu v Identity prostoru URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="75c36-142">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="75c36-143"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Zpracovává všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="75c36-143">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="75c36-144">Tato metoda navíc:</span><span class="sxs-lookup"><span data-stu-id="75c36-144">Additionally, this method:</span></span>

* <span data-ttu-id="75c36-145">Zaregistruje `{APPLICATION NAME}API` prostředek rozhraní API se Identity serverem s výchozím oborem `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="75c36-145">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="75c36-146">Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných Identity serverem pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="75c36-146">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="75c36-147">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="75c36-147">WeatherForecastController</span></span>

<span data-ttu-id="75c36-148">V rozhraní `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) je atribut použit pro třídu.</span><span class="sxs-lookup"><span data-stu-id="75c36-148">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="75c36-149">Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="75c36-149">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="75c36-150">Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené nástrojem <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="75c36-150">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="75c36-151">Pomocná metoda se konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužná rutina pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="75c36-151">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="75c36-152">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="75c36-152">ApplicationDbContext</span></span>

<span data-ttu-id="75c36-153">V rozhraní `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ) se <xref:Microsoft.EntityFrameworkCore.DbContext> rozšiřuje <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> na zahrnutí schématu pro Identity Server.</span><span class="sxs-lookup"><span data-stu-id="75c36-153">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="75c36-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="75c36-154"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="75c36-155">Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity <xref:Microsoft.EntityFrameworkCore.DbContext> tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodě.</span><span class="sxs-lookup"><span data-stu-id="75c36-155">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="75c36-156">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="75c36-156">OidcConfigurationController</span></span>

<span data-ttu-id="75c36-157">V rozhraní `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ) se koncový bod klienta zřídí pro poskytování OIDC parametrů.</span><span class="sxs-lookup"><span data-stu-id="75c36-157">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="75c36-158">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="75c36-158">App settings</span></span>

<span data-ttu-id="75c36-159">V souboru nastavení aplikace ( `appsettings.json` ) v kořenovém adresáři projektu `IdentityServer` obsahuje část seznam konfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="75c36-159">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="75c36-160">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="75c36-160">In the following example, there's a single client.</span></span> <span data-ttu-id="75c36-161">Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth.</span><span class="sxs-lookup"><span data-stu-id="75c36-161">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="75c36-162">Profil indikuje typ aplikace, která se konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="75c36-162">The profile indicates the app type being configured.</span></span> <span data-ttu-id="75c36-163">Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru.</span><span class="sxs-lookup"><span data-stu-id="75c36-163">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="75c36-164">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="75c36-164">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="75c36-165">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="75c36-165">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="75c36-166">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="75c36-166">Authentication package</span></span>

<span data-ttu-id="75c36-167">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty ( `Individual` ), aplikace automaticky obdrží odkaz na balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="75c36-167">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="75c36-168">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="75c36-168">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="75c36-169">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="75c36-169">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a><span data-ttu-id="75c36-170">`HttpClient`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="75c36-170">`HttpClient` configuration</span></span>

<span data-ttu-id="75c36-171">V `Program.Main` ( `Program.cs` ) <xref:System.Net.Http.HttpClient> je pojmenovaný ( `HostIS.ServerAPI` ) nakonfigurovaný tak, aby poskytoval <xref:System.Net.Http.HttpClient> instance, které zahrnují přístupové tokeny při vytváření žádostí na rozhraní API serveru:</span><span class="sxs-lookup"><span data-stu-id="75c36-171">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="75c36-172">Pokud konfigurujete Blazor WebAssembly aplikaci tak, aby používala stávající Identity instanci serveru, která není součástí Blazor hostovaného řešení, změňte <xref:System.Net.Http.HttpClient> registraci základní adresy z <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) na adresu URL koncového bodu rozhraní API serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="75c36-172">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a Blazor Hosted solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="75c36-173">Podpora autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="75c36-173">API authorization support</span></span>

<span data-ttu-id="75c36-174">Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v rámci [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="75c36-174">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="75c36-175">Tato metoda nastaví služby, které aplikace požaduje, aby spolupracovaly se stávajícím autorizačním systémem.</span><span class="sxs-lookup"><span data-stu-id="75c36-175">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="75c36-176">Ve výchozím nastavení je konfigurace pro aplikaci načtena podle konvence z `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="75c36-176">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="75c36-177">Podle konvence je ID klienta nastaveno na název sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="75c36-177">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="75c36-178">Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.</span><span class="sxs-lookup"><span data-stu-id="75c36-178">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="75c36-179">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="75c36-179">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="75c36-180">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="75c36-180">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="75c36-181">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="75c36-181">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="75c36-182">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="75c36-182">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="75c36-183">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="75c36-183">LoginDisplay component</span></span>

<span data-ttu-id="75c36-184">`LoginDisplay`Součást ( `Shared/LoginDisplay.razor` ) je vykreslena v `MainLayout` komponentě ( `Shared/MainLayout.razor` ) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="75c36-184">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="75c36-185">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="75c36-185">For authenticated users:</span></span>
  * <span data-ttu-id="75c36-186">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="75c36-186">Displays the current user name.</span></span>
  * <span data-ttu-id="75c36-187">Nabízí odkaz na stránku profil uživatele v ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="75c36-187">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="75c36-188">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="75c36-188">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="75c36-189">Pro anonymní uživatele:</span><span class="sxs-lookup"><span data-stu-id="75c36-189">For anonymous users:</span></span>
  * <span data-ttu-id="75c36-190">Nabízí možnost registrace.</span><span class="sxs-lookup"><span data-stu-id="75c36-190">Offers the option to register.</span></span>
  * <span data-ttu-id="75c36-191">Nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="75c36-191">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="75c36-192">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="75c36-192">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="75c36-193">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="75c36-193">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="75c36-194">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="75c36-194">Run the app</span></span>

<span data-ttu-id="75c36-195">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="75c36-195">Run the app from the Server project.</span></span> <span data-ttu-id="75c36-196">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="75c36-196">When using Visual Studio, either:</span></span>

* <span data-ttu-id="75c36-197">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="75c36-197">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="75c36-198">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="75c36-198">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="75c36-199">Deklarace identity u názvů a rolí pomocí ověřování API</span><span class="sxs-lookup"><span data-stu-id="75c36-199">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="75c36-200">Vlastní továrna uživatelů</span><span class="sxs-lookup"><span data-stu-id="75c36-200">Custom user factory</span></span>

<span data-ttu-id="75c36-201">V klientské aplikaci vytvořte vlastní objekt pro vytváření uživatelů.</span><span class="sxs-lookup"><span data-stu-id="75c36-201">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="75c36-202">Server odesílá v jedné deklaraci více rolí jako pole JSON `role` .</span><span class="sxs-lookup"><span data-stu-id="75c36-202"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="75c36-203">Jedna role se pošle jako řetězcová hodnota v deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="75c36-203">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="75c36-204">Továrna vytvoří jednotlivou `role` deklaraci identity pro každou roli uživatele.</span><span class="sxs-lookup"><span data-stu-id="75c36-204">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="75c36-205">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="75c36-205">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="75c36-206">V klientské aplikaci Zaregistrujte továrnu v `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="75c36-206">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="75c36-207">V serverové aplikaci zavolejte <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> na Identity Tvůrce, který přidá služby související s rolemi:</span><span class="sxs-lookup"><span data-stu-id="75c36-207">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="75c36-208">Konfigurovat Identity Server</span><span class="sxs-lookup"><span data-stu-id="75c36-208">Configure Identity Server</span></span>

<span data-ttu-id="75c36-209">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="75c36-209">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="75c36-210">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="75c36-210">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="75c36-211">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="75c36-211">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="75c36-212">Možnosti autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="75c36-212">API authorization options</span></span>

<span data-ttu-id="75c36-213">V serverové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="75c36-213">In the Server app:</span></span>

* <span data-ttu-id="75c36-214">Nakonfigurujte Identity Server tak, aby `name` uvedl `role` deklarace identity a do tokenu ID a přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="75c36-214">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="75c36-215">Zabrání výchozímu mapování rolí v obslužné rutině tokenu JWT.</span><span class="sxs-lookup"><span data-stu-id="75c36-215">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="75c36-216">Profilová služba</span><span class="sxs-lookup"><span data-stu-id="75c36-216">Profile Service</span></span>

<span data-ttu-id="75c36-217">V serverové aplikaci vytvořte `ProfileService` implementaci.</span><span class="sxs-lookup"><span data-stu-id="75c36-217">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="75c36-218">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="75c36-218">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="75c36-219">V serverové aplikaci Zaregistrujte službu profilů v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="75c36-219">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="75c36-220">Použití autorizačních mechanismů</span><span class="sxs-lookup"><span data-stu-id="75c36-220">Use authorization mechanisms</span></span>

<span data-ttu-id="75c36-221">V klientské aplikaci jsou v tuto chvíli funkční přístupy k komponentám autorizace.</span><span class="sxs-lookup"><span data-stu-id="75c36-221">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="75c36-222">Kterýkoli z autorizačních mechanismů v součástech může použít roli k autorizaci uživatele:</span><span class="sxs-lookup"><span data-stu-id="75c36-222">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="75c36-223">[ `AuthorizeView` součást](xref:blazor/security/index#authorizeview-component) (příklad: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="75c36-223">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="75c36-224">[ `[Authorize]` direktiva atributu](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (příklad: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="75c36-224">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="75c36-225">[Procedurální logika](xref:blazor/security/index#procedural-logic) (příklad: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="75c36-225">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="75c36-226">Podporuje se vícenásobné testy rolí:</span><span class="sxs-lookup"><span data-stu-id="75c36-226">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="75c36-227">`User.Identity.Name`se naplní v klientské aplikaci pomocí uživatelského jména uživatele, což je obvykle jejich přihlašovací e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="75c36-227">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="75c36-228">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="75c36-228">Additional resources</span></span>

* [<span data-ttu-id="75c36-229">Nasazení do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="75c36-229">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="75c36-230">Import certifikátu z Key Vault (dokumentace k Azure)</span><span class="sxs-lookup"><span data-stu-id="75c36-230">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="75c36-231">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="75c36-231">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
