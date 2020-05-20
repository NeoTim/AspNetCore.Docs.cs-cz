---
title: Seznámení s ověřováním pro jednostránkové aplikace v ASP.NET Core
author: javiercn
description: Používejte Identity s jednou stránkou, která je hostovaná v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 26d371161bf5f926e50cbc141ccfaac40ee96977
ms.sourcegitcommit: ff5c47beded9264c1395beb9c905f826261f3ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2020
ms.locfileid: "83440175"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="6d6a5-103">Ověřování a autorizace pro jednostránkové</span><span class="sxs-lookup"><span data-stu-id="6d6a5-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="6d6a5-104">ASP.NET Core 3,0 nebo novější nabízí ověřování v aplikacích s jednou stránkou (jednostránkové) pomocí podpory pro autorizaci rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="6d6a5-105">ASP.NET Core Identity pro ověřování a ukládání uživatelů se v kombinaci s [IdentityServer](https://identityserver.io/) pro implementaci otevřeného ID Connect.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="6d6a5-106">Parametr ověřování byl přidán do **úhlových** a **reagujících** šablon projektů, které se podobají parametru ověřování v šablonách projektů **webové aplikace (model-zobrazení-kontroler)** a **webové aplikace** ( Razor stránky).</span><span class="sxs-lookup"><span data-stu-id="6d6a5-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="6d6a5-107">Povolené hodnoty parametrů jsou **none** a **jednotlivce**.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="6d6a5-108">Šablona projektu **reagují. js a Redux** v tuto chvíli nepodporuje parametr ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="6d6a5-109">Vytvoření aplikace s podporou autorizace rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6d6a5-109">Create an app with API authorization support</span></span>

<span data-ttu-id="6d6a5-110">Ověřování a autorizaci uživatelů lze použít s použitím úhlů i reagujících jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="6d6a5-111">Otevřete příkazové prostředí a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="6d6a5-112">**Úhlová**:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="6d6a5-113">**Reakce**:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="6d6a5-114">Předchozí příkaz vytvoří aplikaci ASP.NET Core s adresářem *clientapp* obsahujícím Spa.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="6d6a5-115">Obecný popis ASP.NET Core komponent aplikace</span><span class="sxs-lookup"><span data-stu-id="6d6a5-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="6d6a5-116">Následující části popisují přidání do projektu, pokud je k dispozici podpora ověřování:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="6d6a5-117">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="6d6a5-117">Startup class</span></span>

<span data-ttu-id="6d6a5-118">`Startup`Třída má následující doplňky:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="6d6a5-119">Uvnitř `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="6d6a5-120">s výchozím uživatelským rozhraním:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-120"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="6d6a5-121">IdentityServer s další `AddApiAuthorization` pomocnou metodou, která nastaví některé výchozí konvence ASP.NET Core nad IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-121">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="6d6a5-122">Ověřování s další `AddIdentityServerJwt` pomocnou metodou, která nakonfiguruje aplikaci pro ověření tokenů JWT vyprodukovaných pomocí IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="6d6a5-123">Uvnitř `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="6d6a5-124">Middleware ověřování, který zodpovídá za ověření přihlašovacích údajů požadavku a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="6d6a5-125">Middleware IdentityServer, který zpřístupňuje koncové body připojení Open ID:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="6d6a5-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="6d6a5-126">AddApiAuthorization</span></span>

<span data-ttu-id="6d6a5-127">Tato pomocná metoda nakonfiguruje IdentityServer, aby používala naši podporovanou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="6d6a5-128">IdentityServer je výkonná a rozšiřitelná architektura pro zpracování otázek zabezpečení aplikací.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="6d6a5-129">Ve stejnou chvíli zveřejňuje nepotřebnou složitost v nejběžnějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="6d6a5-130">V důsledku toho jsou k dispozici sady konvencí a možností konfigurace, které jsou považovány za dobrý výchozí bod.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="6d6a5-131">Jakmile se vaše potřeby ověřování změní, je stále k dispozici kompletní výkon IdentityServer, abyste mohli přizpůsobit ověřování podle vašich potřeb.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="6d6a5-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="6d6a5-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="6d6a5-133">Tato pomocná metoda nakonfiguruje schéma zásad pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="6d6a5-134">Zásady jsou nakonfigurované tak, aby pomohly Identity zpracovávat všechny požadavky směrované na jakoukoli dílčí cestu v Identity prostoru URL (/ Identity ).</span><span class="sxs-lookup"><span data-stu-id="6d6a5-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="6d6a5-135">`JwtBearerHandler`Zpracovává všechny ostatní požadavky.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="6d6a5-136">Tato metoda navíc registruje `<<ApplicationName>>API` prostředek rozhraní API s IdentityServer s výchozím rozsahem `<<ApplicationName>>API` a konfiguruje middleware tokenu JWT nosiče k ověření tokenů vydaných IdentityServer pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="6d6a5-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="6d6a5-137">WeatherForecastController</span></span>

<span data-ttu-id="6d6a5-138">V souboru *Controllers\WeatherForecastController.cs* si všimněte `[Authorize]` atributu použitého pro třídu, která indikuje, že uživatel musí být autorizován na základě výchozích zásad pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="6d6a5-139">Výchozí zásady autorizace se budou konfigurovat tak, aby používaly výchozí schéma ověřování, které je nastavené `AddIdentityServerJwt` na základě výše zmíněného schématu zásad, což nastaví `JwtBearerHandler` jako výchozí obslužnou rutinu pro požadavky na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="6d6a5-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="6d6a5-140">ApplicationDbContext</span></span>

<span data-ttu-id="6d6a5-141">V souboru *Data\ApplicationDbContext.cs* si všimněte, že se `DbContext` používá v Identity s výjimkou, kterou rozšiřuje `ApiAuthorizationDbContext` (další odvozená třída z `IdentityDbContext` ) pro zahrnutí schématu pro IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="6d6a5-142">Chcete-li získat úplné řízení schématu databáze, zdědit jednu z dostupných Identity `DbContext` tříd a nakonfigurovat kontext pro zahrnutí Identity schématu voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="6d6a5-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="6d6a5-143">OidcConfigurationController</span></span>

<span data-ttu-id="6d6a5-144">V souboru *Controllers\OidcConfigurationController.cs* si všimněte koncového bodu, který se zřídí, aby sloužil parametrům OIDC, které musí klient použít.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="6d6a5-145">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="6d6a5-145">appsettings.json</span></span>

<span data-ttu-id="6d6a5-146">V souboru *appSettings. JSON* kořenového adresáře projektu je k dispozici nová `IdentityServer` část, která popisuje seznam konfigurovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="6d6a5-147">V následujícím příkladu je jeden klient.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-147">In the following example, there's a single client.</span></span> <span data-ttu-id="6d6a5-148">Název klienta odpovídá názvu aplikace a je mapován podle konvence na `ClientId` parametr OAuth.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="6d6a5-149">Profil indikuje typ aplikace, která se konfiguruje.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="6d6a5-150">Interně se používá k zajištění konvencí, které zjednodušují proces konfigurace serveru.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="6d6a5-151">K dispozici je několik profilů, jak je vysvětleno v části [profily aplikací](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="6d6a5-152">appSettings. Vývoj. JSON</span><span class="sxs-lookup"><span data-stu-id="6d6a5-152">appsettings.Development.json</span></span>

<span data-ttu-id="6d6a5-153">V souboru *appSettings. Soubor Development. JSON* kořenového adresáře projektu obsahuje `IdentityServer` oddíl, který popisuje klíč použitý k podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="6d6a5-154">Při nasazování do produkčního prostředí se musí klíč zřídit a nasadit společně s aplikací, jak je vysvětleno v části [nasazení do produkčního](#deploy-to-production) prostředí.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="6d6a5-155">Obecný popis aplikace z úhlů</span><span class="sxs-lookup"><span data-stu-id="6d6a5-155">General description of the Angular app</span></span>

<span data-ttu-id="6d6a5-156">Podpora ověřování a autorizace rozhraní API v úhlové šabloně se nachází v jeho vlastním úhlovém modulu v adresáři *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="6d6a5-157">Modul se skládá z následujících prvků:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="6d6a5-158">3 součásti:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-158">3 components:</span></span>
  * <span data-ttu-id="6d6a5-159">*Login. Component. TS*: zpracovává tok přihlášení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="6d6a5-160">*logout. Component. TS*: zpracovává tok odhlášení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="6d6a5-161">*Login-menu. Component. TS*: widget, který zobrazuje jednu z následujících sad odkazů:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="6d6a5-162">Správa profilů uživatelů a odhlášení odkazů při ověření uživatele</span><span class="sxs-lookup"><span data-stu-id="6d6a5-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="6d6a5-163">Registrace a přihlášení v odkazech, když se uživatel neověřuje</span><span class="sxs-lookup"><span data-stu-id="6d6a5-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="6d6a5-164">Ochrana trasy `AuthorizeGuard` , kterou je možné přidat do tras a vyžaduje ověření uživatele před návštěvou trasy.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="6d6a5-165">Zachytávací protokol HTTP `AuthorizeInterceptor` , který připojuje přístupový token k odchozím požadavkům HTTP, které cílí na rozhraní API při ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="6d6a5-166">Služba `AuthorizeService` , která zpracovává podrobné informace o procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="6d6a5-167">Úhlový modul, který definuje trasy přidružené k částem ověřování aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="6d6a5-168">Zpřístupňuje komponentu nabídky přihlášení, zachytávací modul, ochranu a službu pro využití ze zbytku aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="6d6a5-169">Obecný popis aplikace s poreakcim</span><span class="sxs-lookup"><span data-stu-id="6d6a5-169">General description of the React app</span></span>

<span data-ttu-id="6d6a5-170">Podpora ověřování a autorizace rozhraní API v šabloně reakce se nachází v adresáři *ClientApp\src\components\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="6d6a5-171">Skládá se z následujících prvků:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="6d6a5-172">4 součásti:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-172">4 components:</span></span>
  * <span data-ttu-id="6d6a5-173">*Login. js*: zpracovává tok přihlášení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="6d6a5-174">*Odhlašovací. js*: zpracovává tok odhlašování aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="6d6a5-175">*LoginMenu. js*: widget, který zobrazuje jednu z následujících sad odkazů:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="6d6a5-176">Správa profilů uživatelů a odhlášení odkazů při ověření uživatele</span><span class="sxs-lookup"><span data-stu-id="6d6a5-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="6d6a5-177">Registrace a přihlášení v odkazech, když se uživatel neověřuje</span><span class="sxs-lookup"><span data-stu-id="6d6a5-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="6d6a5-178">*AuthorizeRoute. js*: komponenta směrování, která vyžaduje ověření uživatele před vykreslením součásti uvedené v `Component` parametru.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="6d6a5-179">Exportovaná `authService` instance třídy `AuthorizeService` , která zpracovává podrobnosti na nižší úrovni procesu ověřování a zpřístupňuje informace o ověřeném uživateli do zbytku aplikace za účelem využití.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="6d6a5-180">Teď, když jste viděli hlavní součásti řešení, se můžete podívat na podrobnější přehled o jednotlivých scénářích aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="6d6a5-181">Vyžadovat autorizaci pro nové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6d6a5-181">Require authorization on a new API</span></span>

<span data-ttu-id="6d6a5-182">Ve výchozím nastavení je systém nakonfigurován tak, aby snadno vyžadoval autorizaci pro nová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="6d6a5-183">Provedete to tak, že vytvoříte nový kontroler a přidáte `[Authorize]` ho do třídy Controller nebo do jakékoli akce v rámci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="6d6a5-184">Přizpůsobení obslužné rutiny ověřování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6d6a5-184">Customize the API authentication handler</span></span>

<span data-ttu-id="6d6a5-185">Chcete-li přizpůsobit konfiguraci obslužné rutiny JWT rozhraní API, nakonfigurujte její <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instanci:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="6d6a5-186">Obslužná rutina tokenu JWT rozhraní API vyvolává události, které umožňují řízení procesu ověřování pomocí `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-186">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="6d6a5-187">Pro zajištění podpory pro autorizaci rozhraní API `AddIdentityServerJwt` Zaregistrujte vlastní obslužné rutiny událostí.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-187">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="6d6a5-188">Chcete-li přizpůsobit zpracování události, zabalte existující obslužnou rutinu události s další logikou podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-188">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="6d6a5-189">Například:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-189">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="6d6a5-190">V předchozím kódu `OnTokenValidated` je obslužná rutina události nahrazena vlastní implementací.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-190">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="6d6a5-191">Tato implementace:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-191">This implementation:</span></span>

1. <span data-ttu-id="6d6a5-192">Volá původní implementaci poskytovanou podporou autorizace rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-192">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="6d6a5-193">Spusťte vlastní logiku.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-193">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="6d6a5-194">Ochrana trasy na straně klienta (úhlové)</span><span class="sxs-lookup"><span data-stu-id="6d6a5-194">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="6d6a5-195">Ochrana směrování na straně klienta se provádí přidáním ochrany autorizace do seznamu ochranných zařízení, která se spustí při konfiguraci trasy.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-195">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="6d6a5-196">Jako příklad můžete vidět, jak `fetch-data` je trasa nakonfigurovaná v rámci modulu Main v hlavní aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-196">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="6d6a5-197">Je důležité si zmínit, že při ochraně trasy se nechrání skutečný koncový bod (který stále vyžaduje `[Authorize]` použití atributu), ale to brání uživateli v přechodu na danou trasu na straně klienta, pokud není ověřen.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-197">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="6d6a5-198">Ověřit požadavky rozhraní API (úhlové)</span><span class="sxs-lookup"><span data-stu-id="6d6a5-198">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="6d6a5-199">Ověřování požadavků na rozhraní API hostovaná společně s aplikací se provádí automaticky pomocí zachytávací klienta HTTP definovaného aplikací.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-199">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="6d6a5-200">Ochrana trasy na straně klienta (reakce)</span><span class="sxs-lookup"><span data-stu-id="6d6a5-200">Protect a client-side route (React)</span></span>

<span data-ttu-id="6d6a5-201">Chránit směrování na straně klienta pomocí `AuthorizeRoute` komponenty místo prosté `Route` součásti.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-201">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="6d6a5-202">Všimněte si například, jak `fetch-data` je trasa nakonfigurovaná v rámci `App` součásti:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-202">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="6d6a5-203">Ochrana trasy:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-203">Protecting a route:</span></span>

* <span data-ttu-id="6d6a5-204">Nechrání skutečný koncový bod (který pořád vyžaduje `[Authorize]` Aplikování atributu).</span><span class="sxs-lookup"><span data-stu-id="6d6a5-204">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="6d6a5-205">Zabrání uživateli v přechodu na danou trasu na straně klienta, pokud není ověřen.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-205">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="6d6a5-206">Ověřit požadavky rozhraní API (reagovat)</span><span class="sxs-lookup"><span data-stu-id="6d6a5-206">Authenticate API requests (React)</span></span>

<span data-ttu-id="6d6a5-207">Ověřování požadavků pomocí reakce je provedeno napřed importem `authService` instance z `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-207">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="6d6a5-208">Přístupový token je načten z `authService` a je připojen k žádosti, jak je uvedeno níže.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-208">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="6d6a5-209">V reakci na komponenty se tato práce obvykle provádí v `componentDidMount` metodě životního cyklu nebo jako výsledek z nějaké interakce s uživatelem.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-209">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="6d6a5-210">Import authService do komponenty</span><span class="sxs-lookup"><span data-stu-id="6d6a5-210">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="6d6a5-211">Načtení a připojení přístupového tokenu k odpovědi</span><span class="sxs-lookup"><span data-stu-id="6d6a5-211">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="6d6a5-212">Nasazení do produkčního prostředí</span><span class="sxs-lookup"><span data-stu-id="6d6a5-212">Deploy to production</span></span>

<span data-ttu-id="6d6a5-213">Pokud chcete nasadit aplikaci do produkčního prostředí, je potřeba zřídit tyto prostředky:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-213">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="6d6a5-214">Databáze pro ukládání Identity uživatelských účtů a IdentityServer grantů.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-214">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="6d6a5-215">Provozní certifikát, který se má použít pro podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-215">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="6d6a5-216">Pro tento certifikát neexistují žádné zvláštní požadavky. může se jednat o certifikát podepsaný svým držitelem nebo certifikát zřízený autoritou certifikační autority.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-216">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="6d6a5-217">Dá se vygenerovat pomocí standardních nástrojů, jako je PowerShell nebo OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-217">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="6d6a5-218">Dá se nainstalovat do úložiště certifikátů na cílových počítačích nebo nasadit jako soubor *. pfx* se silným heslem.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-218">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="6d6a5-219">Příklad: nasazení na Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6d6a5-219">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="6d6a5-220">Tato část popisuje nasazení aplikace, aby Azure App Service pomocí certifikátu uloženého v úložišti certifikátů.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-220">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="6d6a5-221">Pokud chcete aplikaci upravit tak, aby načetla certifikát z úložiště certifikátů, při konfiguraci aplikace v Azure Portal v pozdějším kroku se vyžaduje plán Service úrovně Standard nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-221">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="6d6a5-222">V souboru *appSettings. JSON* aplikace upravte `IdentityServer` oddíl tak, aby obsahoval podrobnosti klíče:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-222">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="6d6a5-223">Název úložiště představuje název úložiště certifikátů, ve kterém je certifikát uložený.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-223">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="6d6a5-224">V tomto případě odkazuje na úložiště osobních uživatelů.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-224">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="6d6a5-225">Umístění úložiště představuje místo, kde se má certifikát načíst ( `CurrentUser` nebo `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="6d6a5-225">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="6d6a5-226">Vlastnost Name u certifikátu odpovídá rozlišujícímu předmětu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-226">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="6d6a5-227">Pokud chcete nasadit Azure App Service, postupujte podle kroků v části [nasazení aplikace do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), která vysvětluje, jak vytvořit potřebné prostředky Azure a nasadit aplikaci do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-227">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="6d6a5-228">Po provedení kroků uvedených v předchozích pokynech se aplikace nasadí do Azure, ale ještě není funkční.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-228">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="6d6a5-229">Certifikát používaný aplikací musí být nakonfigurovaný v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-229">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="6d6a5-230">Vyhledejte kryptografický otisk certifikátu a postupujte podle kroků popsaných v tématu [načtení certifikátů](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="6d6a5-230">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="6d6a5-231">I když tento postup uvádí protokol SSL, existuje část **privátní certifikáty** v Azure Portal, kde můžete nahrát zřízený certifikát pro použití s aplikací.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-231">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="6d6a5-232">Po nakonfigurování aplikace a nastavení aplikace v Azure Portal restartujte aplikaci na portálu.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-232">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="6d6a5-233">Další možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="6d6a5-233">Other configuration options</span></span>

<span data-ttu-id="6d6a5-234">Podpora pro autorizaci rozhraní API se sestavuje na IdentityServer se sadou konvencí, výchozími hodnotami a vylepšeními, které zjednodušují prostředí jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-234">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="6d6a5-235">Bez nutnosti vyslovit, pokud ASP.NET Core integrace nepokrývá váš scénář, je k dispozici plný výkon IdentityServer na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-235">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="6d6a5-236">Podpora ASP.NET Core se zaměřuje na aplikace "první strany", kde se všechny aplikace vytvářejí a nasazují v naší organizaci.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-236">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="6d6a5-237">V takovém případě se podpora nenabízí pro věci, jako je například souhlas nebo federace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-237">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="6d6a5-238">V případě těchto scénářů použijte IdentityServer a postupujte podle jejich dokumentace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-238">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="6d6a5-239">Profily aplikací</span><span class="sxs-lookup"><span data-stu-id="6d6a5-239">Application profiles</span></span>

<span data-ttu-id="6d6a5-240">Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále definují jejich parametry.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-240">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="6d6a5-241">V současné době jsou podporovány následující profily:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-241">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="6d6a5-242">`IdentityServerSPA`: Představuje zabezpečené ověřování na hostitele společně s IdentityServer jako s jednou jednotkou.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-242">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="6d6a5-243">`redirect_uri`Výchozí hodnota je `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-243">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="6d6a5-244">`post_logout_redirect_uri`Výchozí hodnota je `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-244">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="6d6a5-245">Sada oborů zahrnuje `openid` , `profile` a každý obor definovaný pro rozhraní API v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-245">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="6d6a5-246">Sada povolených typů OIDC odezvy je `id_token token` nebo každou z nich jednotlivě ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="6d6a5-246">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="6d6a5-247">Režim povolených odpovědí je `fragment` .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-247">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="6d6a5-248">`SPA`: Představuje zabezpečené zabezpečené ověřování, které není hostované v IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-248">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="6d6a5-249">Sada oborů zahrnuje `openid` , `profile` a každý obor definovaný pro rozhraní API v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-249">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="6d6a5-250">Sada povolených typů OIDC odezvy je `id_token token` nebo každou z nich jednotlivě ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="6d6a5-250">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="6d6a5-251">Režim povolených odpovědí je `fragment` .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-251">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="6d6a5-252">`IdentityServerJwt`: Představuje rozhraní API, které je hostováno společně s IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-252">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="6d6a5-253">Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-253">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="6d6a5-254">`API`: Představuje rozhraní API, které není hostované v IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-254">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="6d6a5-255">Aplikace je nakonfigurovaná tak, aby měla jeden obor, který má výchozí název aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-255">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="6d6a5-256">Konfigurace prostřednictvím AppSettings</span><span class="sxs-lookup"><span data-stu-id="6d6a5-256">Configuration through AppSettings</span></span>

<span data-ttu-id="6d6a5-257">Nakonfigurujte aplikace přes konfigurační systém jejich přidáním do seznamu `Clients` nebo `Resources` .</span><span class="sxs-lookup"><span data-stu-id="6d6a5-257">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="6d6a5-258">Nakonfigurujte jednotlivé vlastnosti klienta `redirect_uri` a `post_logout_redirect_uri` , jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-258">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="6d6a5-259">Při konfiguraci prostředků můžete nakonfigurovat obory pro prostředek, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="6d6a5-259">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="6d6a5-260">Konfigurace prostřednictvím kódu</span><span class="sxs-lookup"><span data-stu-id="6d6a5-260">Configuration through code</span></span>

<span data-ttu-id="6d6a5-261">Můžete také nakonfigurovat klienty a prostředky prostřednictvím kódu pomocí přetížení `AddApiAuthorization` , které provede akci konfigurace možností.</span><span class="sxs-lookup"><span data-stu-id="6d6a5-261">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="6d6a5-262">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6d6a5-262">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
