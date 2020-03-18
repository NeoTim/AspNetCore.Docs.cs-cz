---
title: Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí serveru identit
author: guardrex
description: Vytvoření nové hostované aplikace v Blazor s ověřováním ze sady Visual Studio, která používá back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: a3993bf635e5a7aae408d72796015f2414e13c14
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434470"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="97163-103">Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí serveru identit</span><span class="sxs-lookup"><span data-stu-id="97163-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="97163-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="97163-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="97163-105">Vytvoření nové hostované aplikace Blazor v aplikaci Visual Studio, která používá [IdentityServer](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="97163-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="97163-106">Pomocí sady Visual Studio vytvořte novou **Blazor aplikaci WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="97163-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="97163-107">Další informace naleznete v tématu <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="97163-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="97163-108">V dialogovém okně **vytvořit novou aplikaci Blazor** vyberte v části **ověřování** možnost **změnit** .</span><span class="sxs-lookup"><span data-stu-id="97163-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="97163-109">Vyberte **jednotlivé uživatelské účty** a potom klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="97163-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="97163-110">Zaškrtněte políčko **ASP.NET Core hostované** v části **Upřesnit** .</span><span class="sxs-lookup"><span data-stu-id="97163-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="97163-111">Vyberte tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="97163-111">Select the **Create** button.</span></span>

<span data-ttu-id="97163-112">Pokud chcete vytvořit aplikaci v příkazovém prostředí, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="97163-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="97163-113">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="97163-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="97163-114">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="97163-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="97163-115">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="97163-115">Server app configuration</span></span>

<span data-ttu-id="97163-116">Následující části popisují přidání do projektu, pokud je zahrnutá Podpora ověřování.</span><span class="sxs-lookup"><span data-stu-id="97163-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="97163-117">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="97163-117">Startup class</span></span>

<span data-ttu-id="97163-118">Třída `Startup` obsahuje následující doplňky:</span><span class="sxs-lookup"><span data-stu-id="97163-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="97163-119">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="97163-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="97163-120">Identita s výchozím uživatelským rozhraním:</span><span class="sxs-lookup"><span data-stu-id="97163-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="97163-121">IdentityServer se další pomocnou metodou <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, která nastaví některé výchozí konvence ASP.NET Core na IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="97163-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="97163-122">Ověřování s dodatečnou podpůrnou metodou <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, která nakonfiguruje aplikaci tak, aby ověřovala tokeny JWT vytvořené pomocí IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="97163-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="97163-123">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="97163-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="97163-124">Middleware ověřování, který zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="97163-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="97163-125">Middleware IdentityServer, který zveřejňuje koncové body Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="97163-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="97163-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="97163-126">AddApiAuthorization</span></span>

<span data-ttu-id="97163-127">Pomocná metoda <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> konfiguruje [IdentityServer](https://identityserver.io/) pro ASP.NET Core scénáře.</span><span class="sxs-lookup"><span data-stu-id="97163-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="97163-128">IdentityServer je výkonná a rozšiřitelná architektura pro zpracování otázek zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="97163-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="97163-129">IdentityServer zpřístupňuje zbytečné složitosti pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="97163-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="97163-130">V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="97163-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="97163-131">Jakmile se vaše potřeby ověřování změní, je stále k dispozici kompletní výkon IdentityServer, aby bylo možné přizpůsobit ověřování podle požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="97163-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="97163-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="97163-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="97163-133">Pomocná metoda <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="97163-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="97163-134">Zásady jsou nakonfigurovány tak, aby umožňovaly identitě zpracovávat všechny požadavky směrované na jakoukoli dílčí cestu v prostoru adres URL `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="97163-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="97163-135"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> zpracovává všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="97163-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="97163-136">Tato metoda navíc:</span><span class="sxs-lookup"><span data-stu-id="97163-136">Additionally, this method:</span></span>

* <span data-ttu-id="97163-137">Zaregistruje prostředek rozhraní `{APPLICATION NAME}API` API s IdentityServer s výchozím rozsahem `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="97163-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="97163-138">Konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="97163-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="97163-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="97163-139">WeatherForecastController</span></span>

<span data-ttu-id="97163-140">V `WeatherForecastController` (*Controllers/WeatherForecastController. cs*) se pro třídu použije atribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="97163-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="97163-141">Atribut označuje, že uživatel musí být autorizovaný na základě výchozích zásad pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="97163-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="97163-142">Výchozí zásada autorizace je nakonfigurovaná tak, aby používala výchozí schéma ověřování, které je nastavené <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> na dříve zmíněné schéma zásad.</span><span class="sxs-lookup"><span data-stu-id="97163-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="97163-143">Pomocná metoda nakonfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako výchozí obslužnou rutinu pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="97163-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="97163-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="97163-144">ApplicationDbContext</span></span>

<span data-ttu-id="97163-145">V `ApplicationDbContext` (*data/ApplicationDbContext. cs*) se stejný <xref:Microsoft.EntityFrameworkCore.DbContext> používá v identitě s výjimkou, že rozšíření <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> pro zahrnutí schématu pro IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="97163-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="97163-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> je odvozen z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="97163-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="97163-147">Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných tříd identity <xref:Microsoft.EntityFrameworkCore.DbContext> a nakonfigurujte kontext tak, aby zahrnoval schéma identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` v metodě `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="97163-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="97163-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="97163-148">OidcConfigurationController</span></span>

<span data-ttu-id="97163-149">V `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*) se koncový bod klienta zřídí pro poskytování parametrů OIDC.</span><span class="sxs-lookup"><span data-stu-id="97163-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="97163-150">Soubory nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="97163-150">App settings files</span></span>

<span data-ttu-id="97163-151">V souboru nastavení aplikace (*appSettings. JSON*) v kořenovém adresáři projektu popisuje část `IdentityServer` seznam konfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="97163-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="97163-152">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="97163-152">In the following example, there's a single client.</span></span> <span data-ttu-id="97163-153">Název klienta odpovídá názvu aplikace a je mapován podle konvence na parametr `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="97163-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="97163-154">Profil indikuje typ aplikace, která se konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="97163-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="97163-155">Profil se interně používá k tomu, aby bylo možné řídit konvence, které zjednodušují proces konfigurace serveru.</span><span class="sxs-lookup"><span data-stu-id="97163-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="97163-156">V souboru nastavení aplikace pro vývojové prostředí (*appSettings. Development. JSON*) v kořenovém adresáři projektu popisuje část `IdentityServer` klíč použitý k podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="97163-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="97163-157">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="97163-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="97163-158">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="97163-158">Authentication package</span></span>

<span data-ttu-id="97163-159">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty (`Individual`), aplikace automaticky obdrží odkaz na balíček pro balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="97163-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="97163-160">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="97163-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="97163-161">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="97163-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="97163-162">V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="97163-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="97163-163">Podpora autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="97163-163">API authorization support</span></span>

<span data-ttu-id="97163-164">Podpora ověřování uživatelů je zapojena do kontejneru služby prostřednictvím metody rozšíření poskytované v balíčku `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="97163-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="97163-165">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s existujícím autorizačním systémem.</span><span class="sxs-lookup"><span data-stu-id="97163-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="97163-166">Ve výchozím nastavení načte konfiguraci aplikace podle konvence z `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="97163-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="97163-167">Podle konvence je ID klienta nastaveno na název sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="97163-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="97163-168">Tato adresa URL může být změněna tak, aby odkazovala na samostatný koncový bod voláním přetížení s možnostmi.</span><span class="sxs-lookup"><span data-stu-id="97163-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="index-page"></a><span data-ttu-id="97163-169">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="97163-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="97163-170">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="97163-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="97163-171">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="97163-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="97163-172">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="97163-172">LoginDisplay component</span></span>

<span data-ttu-id="97163-173">Součást `LoginDisplay` (*Shared/LoginDisplay. Razor*) je vykreslena ve `MainLayout` komponentě (*Shared/MainLayout. Razor*) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="97163-173">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="97163-174">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="97163-174">For authenticated users:</span></span>
  * <span data-ttu-id="97163-175">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="97163-175">Displays the current user name.</span></span>
  * <span data-ttu-id="97163-176">Nabízí odkaz na stránku profilu uživatele v ASP.NET Core identita.</span><span class="sxs-lookup"><span data-stu-id="97163-176">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="97163-177">Nabízí tlačítko pro odhlášení od aplikace.</span><span class="sxs-lookup"><span data-stu-id="97163-177">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="97163-178">Pro anonymní uživatele:</span><span class="sxs-lookup"><span data-stu-id="97163-178">For anonymous users:</span></span>
  * <span data-ttu-id="97163-179">Nabízí možnost registrace.</span><span class="sxs-lookup"><span data-stu-id="97163-179">Offers the option to register.</span></span>
  * <span data-ttu-id="97163-180">Nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="97163-180">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="97163-181">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="97163-181">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="97163-182">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="97163-182">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="97163-183">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="97163-183">Run the app</span></span>

<span data-ttu-id="97163-184">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="97163-184">Run the app from the Server project.</span></span> <span data-ttu-id="97163-185">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="97163-185">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
