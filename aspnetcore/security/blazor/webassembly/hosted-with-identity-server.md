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
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="9596b-103">Zabezpečení ASP.NET Blazor aplikace hostované pomocí webové sestavy Core S identitou</span><span class="sxs-lookup"><span data-stu-id="9596b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="9596b-104">[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9596b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="9596b-105">Vytvoření nové Blazor hostované aplikace v sadě Visual Studio, která používá [IdentityServer](https://identityserver.io/) k ověřování uživatelů a volání rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="9596b-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="9596b-106">Pomocí sady Visual Studio \*\* Blazor \*\* vytvořte novou aplikaci WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9596b-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="9596b-107">Další informace naleznete v tématu <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="9596b-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="9596b-108">V **dialogovém Blazor okně Vytvořit novou aplikaci** vyberte **Změnit** v části **Ověřování.**</span><span class="sxs-lookup"><span data-stu-id="9596b-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="9596b-109">Vyberte **jednotlivé uživatelské účty** následované **ok**.</span><span class="sxs-lookup"><span data-stu-id="9596b-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="9596b-110">Zaškrtněte **políčko ASP.NET Jádro hostované** v části **Upřesnit.**</span><span class="sxs-lookup"><span data-stu-id="9596b-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="9596b-111">Vyberte tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9596b-111">Select the **Create** button.</span></span>

<span data-ttu-id="9596b-112">Chcete-li aplikaci vytvořit v příkazovém prostředí, proveďte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9596b-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="9596b-113">Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ).</span><span class="sxs-lookup"><span data-stu-id="9596b-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9596b-114">Název složky se také stane součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="9596b-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9596b-115">Konfigurace serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="9596b-115">Server app configuration</span></span>

<span data-ttu-id="9596b-116">Následující části popisují dodatky k projektu, pokud je zahrnuta podpora ověřování.</span><span class="sxs-lookup"><span data-stu-id="9596b-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="9596b-117">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="9596b-117">Startup class</span></span>

<span data-ttu-id="9596b-118">Třída `Startup` má následující dodatky:</span><span class="sxs-lookup"><span data-stu-id="9596b-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="9596b-119">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9596b-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="9596b-120">Identita s výchozím ui:</span><span class="sxs-lookup"><span data-stu-id="9596b-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="9596b-121">IdentityServer s <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> další pomocnou metodou, která nastaví některé výchozí ASP.NET základní konvence nad IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="9596b-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="9596b-122">Ověřování s <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> další pomocnou metodou, která konfiguruje aplikaci k ověření tokenů JWT vytvořených serverem IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="9596b-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="9596b-123">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9596b-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="9596b-124">Ověřovací middleware, který je zodpovědný za ověření pověření požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="9596b-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="9596b-125">Middleware IdentityServer, který zveřejňuje koncové body Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="9596b-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="9596b-126">Přidat oprávnění api</span><span class="sxs-lookup"><span data-stu-id="9596b-126">AddApiAuthorization</span></span>

<span data-ttu-id="9596b-127">Pomocná <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metoda konfiguruje [IdentityServer](https://identityserver.io/) pro scénáře ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9596b-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="9596b-128">IdentityServer je výkonný a rozšiřitelný rámec pro zpracování problémů se zabezpečením aplikací.</span><span class="sxs-lookup"><span data-stu-id="9596b-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="9596b-129">IdentityServer zveřejňuje zbytečné složitosti pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="9596b-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="9596b-130">V důsledku toho je k dispozici sada konvencí a možností konfigurace, které považujeme za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="9596b-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="9596b-131">Jakmile se vaše ověřování potřebuje změnit, plný výkon IdentityServer je stále k dispozici přizpůsobit ověřování tak, aby vyhovovaly požadavkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="9596b-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="9596b-132">PřidatIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="9596b-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="9596b-133">Pomocná <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metoda konfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="9596b-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="9596b-134">Zásada je nakonfigurována tak, aby identita mohla zpracovávat všechny požadavky `/Identity`směrované na libovolnou podcestu v prostoru adresy URL identity .</span><span class="sxs-lookup"><span data-stu-id="9596b-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="9596b-135">Zpracovává <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="9596b-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="9596b-136">Navíc tato metoda:</span><span class="sxs-lookup"><span data-stu-id="9596b-136">Additionally, this method:</span></span>

* <span data-ttu-id="9596b-137">Registruje `{APPLICATION NAME}API` prostředek rozhraní API s IdentityServer `{APPLICATION NAME}API`s výchozím rozsahem .</span><span class="sxs-lookup"><span data-stu-id="9596b-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="9596b-138">Konfiguruje Middleware tokenu Nosiče JWT tak, aby ověřoval tokeny vydané serverem IdentityServer pro danou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9596b-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="9596b-139">Kontrollet weatherforecastu</span><span class="sxs-lookup"><span data-stu-id="9596b-139">WeatherForecastController</span></span>

<span data-ttu-id="9596b-140">V `WeatherForecastController` souboru (*Controllers/WeatherForecastController.cs*) je [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atribut použit pro třídu.</span><span class="sxs-lookup"><span data-stu-id="9596b-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="9596b-141">Atribut označuje, že uživatel musí být autorizován na základě výchozí zásady pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="9596b-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="9596b-142">Výchozí zásady autorizace jsou nakonfigurovány tak, aby <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> používaly výchozí schéma ověřování, které je nastaveno podle schématu zásad, které bylo uvedeno dříve.</span><span class="sxs-lookup"><span data-stu-id="9596b-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="9596b-143">Pomocná metoda konfiguruje jako výchozí obslužná <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> rutina pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9596b-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="9596b-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="9596b-144">ApplicationDbContext</span></span>

<span data-ttu-id="9596b-145">V `ApplicationDbContext` (*Data/ApplicationDbContext.cs*) <xref:Microsoft.EntityFrameworkCore.DbContext> se totéž používá v identitě <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> s výjimkou, že se rozšiřuje zahrnout schéma pro IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="9596b-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="9596b-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>je odvozen <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>od .</span><span class="sxs-lookup"><span data-stu-id="9596b-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="9596b-147">Chcete-li získat úplnou kontrolu nad schématem databáze, dědit z jedné z dostupných tříd identity <xref:Microsoft.EntityFrameworkCore.DbContext> `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` a `OnModelCreating` nakonfigurovat kontext zahrnout schéma identity voláním v metodě.</span><span class="sxs-lookup"><span data-stu-id="9596b-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="9596b-148">Řadič konfigurace Oidc</span><span class="sxs-lookup"><span data-stu-id="9596b-148">OidcConfigurationController</span></span>

<span data-ttu-id="9596b-149">V `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*) je zřízen koncový bod klienta, který slouží parametrům OIDC.</span><span class="sxs-lookup"><span data-stu-id="9596b-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="9596b-150">Soubory nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="9596b-150">App settings files</span></span>

<span data-ttu-id="9596b-151">V souboru nastavení aplikace (*appsettings.json*) `IdentityServer` v kořenovém adresáři projektu popisuje část seznam nakonfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="9596b-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="9596b-152">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="9596b-152">In the following example, there's a single client.</span></span> <span data-ttu-id="9596b-153">Název klienta odpovídá názvu aplikace a je mapován podle `ClientId` konvence na parametr OAuth.</span><span class="sxs-lookup"><span data-stu-id="9596b-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="9596b-154">Profil označuje nakonfigurovaný typ aplikace.</span><span class="sxs-lookup"><span data-stu-id="9596b-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="9596b-155">Profil se používá interně k pohonu konvence, které zjednodušují proces konfigurace pro server.</span><span class="sxs-lookup"><span data-stu-id="9596b-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="9596b-156">V souboru nastavení aplikace vývojové prostředí (*nastavení aplikace. Development.json*) v kořenovém adresáři projektu, `IdentityServer` část popisuje klíč používaný k podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="9596b-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="9596b-157">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="9596b-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9596b-158">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="9596b-158">Authentication package</span></span>

<span data-ttu-id="9596b-159">Když je aplikace vytvořena pro použití`Individual`individuálních uživatelských účtů ( `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ), aplikace automaticky obdrží odkaz na balíček pro balíček v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9596b-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="9596b-160">Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.</span><span class="sxs-lookup"><span data-stu-id="9596b-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9596b-161">Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="9596b-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="9596b-162">Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.</span><span class="sxs-lookup"><span data-stu-id="9596b-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="9596b-163">Podpora autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="9596b-163">API authorization support</span></span>

<span data-ttu-id="9596b-164">Podpora pro ověřování uživatelů je zapojen do kontejneru služby metodou `Microsoft.AspNetCore.Components.WebAssembly.Authentication` rozšíření poskytované uvnitř balíčku.</span><span class="sxs-lookup"><span data-stu-id="9596b-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="9596b-165">Tato metoda nastaví všechny služby potřebné pro aplikaci pro interakci s existujícím systémem autorizace.</span><span class="sxs-lookup"><span data-stu-id="9596b-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="9596b-166">Ve výchozím nastavení načte konfiguraci aplikace `_configuration/{client-id}`podle konvence z .</span><span class="sxs-lookup"><span data-stu-id="9596b-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="9596b-167">Podle konvence id klienta je nastavena na název sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="9596b-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="9596b-168">Tuto adresu URL lze změnit tak, aby přecšití na samostatný koncový bod voláním přetížení s možnostmi.</span><span class="sxs-lookup"><span data-stu-id="9596b-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="9596b-169">Importuje soubor</span><span class="sxs-lookup"><span data-stu-id="9596b-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9596b-170">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="9596b-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="9596b-171">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="9596b-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9596b-172">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="9596b-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9596b-173">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9596b-173">LoginDisplay component</span></span>

<span data-ttu-id="9596b-174">Komponenta `LoginDisplay` (*Shared/LoginDisplay.razor*) je `MainLayout` vykreslena v komponentě (*Shared/MainLayout.razor*) a spravuje následující chování:</span><span class="sxs-lookup"><span data-stu-id="9596b-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="9596b-175">Pro ověřené uživatele:</span><span class="sxs-lookup"><span data-stu-id="9596b-175">For authenticated users:</span></span>
  * <span data-ttu-id="9596b-176">Zobrazí aktuální uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="9596b-176">Displays the current user name.</span></span>
  * <span data-ttu-id="9596b-177">Nabízí odkaz na stránku profilu uživatele v ASP.NET základní identity.</span><span class="sxs-lookup"><span data-stu-id="9596b-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="9596b-178">Nabízí tlačítko pro odhlášení z aplikace.</span><span class="sxs-lookup"><span data-stu-id="9596b-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="9596b-179">Pro anonymní uživatele:</span><span class="sxs-lookup"><span data-stu-id="9596b-179">For anonymous users:</span></span>
  * <span data-ttu-id="9596b-180">Nabízí možnost registrace.</span><span class="sxs-lookup"><span data-stu-id="9596b-180">Offers the option to register.</span></span>
  * <span data-ttu-id="9596b-181">Nabízí možnost přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="9596b-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="9596b-182">Ověřovací komponenta</span><span class="sxs-lookup"><span data-stu-id="9596b-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9596b-183">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="9596b-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9596b-184">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="9596b-184">Run the app</span></span>

<span data-ttu-id="9596b-185">Spusťte aplikaci z projektu Server.</span><span class="sxs-lookup"><span data-stu-id="9596b-185">Run the app from the Server project.</span></span> <span data-ttu-id="9596b-186">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumníku řešení** a vyberte tlačítko **Spustit** na panelu nástrojů nebo spusťte aplikaci z nabídky **Ladění.**</span><span class="sxs-lookup"><span data-stu-id="9596b-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9596b-187">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9596b-187">Additional resources</span></span>

* [<span data-ttu-id="9596b-188">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="9596b-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
