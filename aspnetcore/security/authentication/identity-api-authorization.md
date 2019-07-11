---
title: Úvod k ověřování pro jednu stránku aplikace v ASP.NET Core
author: javiercn
description: Použít identitu s jedinou stránku aplikaci hostované uvnitř aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 302a5e10a70e40e75ab9fe4b3e5a98c4e847b822
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815219"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="3bed7-103">Ověřování a autorizace pro SPA</span><span class="sxs-lookup"><span data-stu-id="3bed7-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="3bed7-104">ASP.NET Core 3.0 nebo novější nabízí ověřování v jedné stránky aplikace (SPA) pomocí podpory pro povolení rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3bed7-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="3bed7-105">ASP.NET Core Identity pro ověřování a ukládání uživatelé číslo zkombinuje s [IdentityServer](https://identityserver.io/) pro implementaci Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="3bed7-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="3bed7-106">Parametr ověřování byl přidán do **Angular** a **React** šablony, které je podobné tomuto: Parametr ověřování v projektu **webové aplikace (Model-View-Controller)**  (MVC) a **webovou aplikaci** šablony projektů (stránky Razor).</span><span class="sxs-lookup"><span data-stu-id="3bed7-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="3bed7-107">Parametr povolené hodnoty jsou **žádný** a **jednotlivé**.</span><span class="sxs-lookup"><span data-stu-id="3bed7-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="3bed7-108">**React.js a Reduxem** šablonu projektu nepodporuje parametr ověřování v tuto chvíli.</span><span class="sxs-lookup"><span data-stu-id="3bed7-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="3bed7-109">Vytvoření aplikace s podporou ověřování API</span><span class="sxs-lookup"><span data-stu-id="3bed7-109">Create an app with API authorization support</span></span>

<span data-ttu-id="3bed7-110">Ověřování a autorizace uživatelů je možné s Angular a SPA React.</span><span class="sxs-lookup"><span data-stu-id="3bed7-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="3bed7-111">Otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3bed7-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="3bed7-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="3bed7-112">**Angular**:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="3bed7-113">**React**:</span><span class="sxs-lookup"><span data-stu-id="3bed7-113">**React**:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="3bed7-114">Předchozí příkaz vytvoří aplikace ASP.NET Core s *ClientApp* adresáře, který obsahuje tato jednostránková aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="3bed7-115">Obecný popis součástí aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bed7-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="3bed7-116">Následující části popisují dodatky k projektu při ověřování podpora je součástí:</span><span class="sxs-lookup"><span data-stu-id="3bed7-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="3bed7-117">Třída při spuštění</span><span class="sxs-lookup"><span data-stu-id="3bed7-117">Startup class</span></span>

<span data-ttu-id="3bed7-118">`Startup` Třída má těmito přídavky:</span><span class="sxs-lookup"><span data-stu-id="3bed7-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="3bed7-119">Uvnitř `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="3bed7-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="3bed7-120">Identita s výchozí uživatelské rozhraní:</span><span class="sxs-lookup"><span data-stu-id="3bed7-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="3bed7-121">IdentityServer ještě `AddApiAuthorization` Pomocná metoda tohoto nastavení některé výchozí konvence ASP.NET Core nad IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="3bed7-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="3bed7-122">Ověřování pomocí dalších `AddIdentityServerJwt` Pomocná metoda, která nakonfiguruje aplikaci pro ověření tokenů JWT vytvářených IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="3bed7-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="3bed7-123">Uvnitř `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="3bed7-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="3bed7-124">Middleware ověřování, která zodpovídá za ověření požadavku pověření a nastavení uživatele v kontextu požadavku:</span><span class="sxs-lookup"><span data-stu-id="3bed7-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="3bed7-125">IdentityServer middleware, který zveřejňuje koncové body Open ID Connect:</span><span class="sxs-lookup"><span data-stu-id="3bed7-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="3bed7-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="3bed7-126">AddApiAuthorization</span></span>

<span data-ttu-id="3bed7-127">Tato pomocná metoda nakonfiguruje IdentityServer používat naše podporovanou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="3bed7-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="3bed7-128">IdentityServer je výkonný a rozšiřitelný rámec pro zpracování otázky zabezpečení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="3bed7-129">Ve stejnou dobu, která zveřejní zbytečné složitosti pro nejběžnější scénáře.</span><span class="sxs-lookup"><span data-stu-id="3bed7-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="3bed7-130">V důsledku toho je sada konvence a možnosti konfigurace přesnost je potřeba, které jsou považovány za dobrým výchozím bodem.</span><span class="sxs-lookup"><span data-stu-id="3bed7-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="3bed7-131">Po ověření potřeby změní, je plný výkon IdentityServer stále k dispozici pro přizpůsobení ověřování tak, aby odpovídala vašim potřebám.</span><span class="sxs-lookup"><span data-stu-id="3bed7-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="3bed7-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="3bed7-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="3bed7-133">Tato pomocná metoda nakonfiguruje zásady schéma pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="3bed7-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="3bed7-134">Pokud chcete, aby identita zpracování všech požadavků směrovat do jakékoli podřízená cesta v prostoru adresa URL Identity nakonfigurované "/ Identity".</span><span class="sxs-lookup"><span data-stu-id="3bed7-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="3bed7-135">`JwtBearerHandler` Zpracovává všechny ostatní žádosti.</span><span class="sxs-lookup"><span data-stu-id="3bed7-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="3bed7-136">Kromě toho tato metoda zaregistruje `<<ApplicationName>>API` prostředku rozhraní API s IdentityServer s výchozí obor `<<ApplicationName>>API` a nakonfiguruje middlewaru tokenu nosiče JWT ověření tokeny vystavené službou IdentityServer pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bed7-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="3bed7-137">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="3bed7-137">SampleDataController</span></span>

<span data-ttu-id="3bed7-138">V *Controllers\SampleDataController.cs* souboru, Všimněte si, že `[Authorize]` atributu použít pro třídu, která označuje, že uživatel musí být povoleno podle výchozí zásady pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="3bed7-138">In the *Controllers\SampleDataController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="3bed7-139">Byly konfigurovány k použití výchozího schématu ověřování, který je nastavený podle výchozí zásady autorizace se stane s `AddIdentityServerJwt` na schéma zásad, které jsme zmínili výše, což `JwtBearerHandler` nakonfiguroval tato Pomocná metoda pro obslužnou rutinu výchozí požadavky na aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="3bed7-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="3bed7-140">ApplicationDbContext</span></span>

<span data-ttu-id="3bed7-141">V *Data\ApplicationDbContext.cs* souboru, Všimněte si, že stejné `DbContext` se používá v Identity s výjimkou, která rozšiřuje `ApiAuthorizationDbContext` (více odvozené třídy z `IdentityDbContext`) a zahrnout schéma IdentityServer do.</span><span class="sxs-lookup"><span data-stu-id="3bed7-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="3bed7-142">K získání úplné kontroly schématu databáze, dědit z jednoho z dostupných Identity `DbContext` třídy a konfigurovat kontextu se zahrnout schéma Identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` na `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="3bed7-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="3bed7-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="3bed7-143">OidcConfigurationController</span></span>

<span data-ttu-id="3bed7-144">V *Controllers\OidcConfigurationController.cs* souboru, Všimněte si, že koncový bod, pro kterého je zřízené pro obsluhu OIDC parametry, které je potřeba použít klienta.</span><span class="sxs-lookup"><span data-stu-id="3bed7-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3bed7-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="3bed7-145">appsettings.json</span></span>

<span data-ttu-id="3bed7-146">V *appsettings.json* souboru kořen projektu je nový `IdentityServer` část popisující seznam nakonfigurovat klienty.</span><span class="sxs-lookup"><span data-stu-id="3bed7-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="3bed7-147">V následujícím příkladu je jednoho klienta.</span><span class="sxs-lookup"><span data-stu-id="3bed7-147">In the following example, there's a single client.</span></span> <span data-ttu-id="3bed7-148">Název klienta odpovídá názvu aplikace a mapovat pomocí konvence na OAuth `ClientId` parametru.</span><span class="sxs-lookup"><span data-stu-id="3bed7-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="3bed7-149">Profil, který určuje typ aplikace, která se právě nastavuje.</span><span class="sxs-lookup"><span data-stu-id="3bed7-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="3bed7-150">Používá se interně k jednotce vytváření názvů, které zjednodušují proces konfigurace pro server.</span><span class="sxs-lookup"><span data-stu-id="3bed7-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="3bed7-151">K dispozici několik profilů, jak je vysvětleno v [profily aplikací](#application-profiles) oddílu.</span><span class="sxs-lookup"><span data-stu-id="3bed7-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="3bed7-152">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="3bed7-152">appsettings.Development.json</span></span>

<span data-ttu-id="3bed7-153">V *appsettings. Development.JSON* souboru kořen projektu je `IdentityServer` část popisující klíč používaný k podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="3bed7-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="3bed7-154">Při nasazení do produkčního prostředí, klíč se musí zřídit a nasadit spolu s aplikací, jak je vysvětleno v [nasazení do produkčního prostředí](#deploy-to-production) oddílu.</span><span class="sxs-lookup"><span data-stu-id="3bed7-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="3bed7-155">Obecný popis aplikaci Angular</span><span class="sxs-lookup"><span data-stu-id="3bed7-155">General description of the Angular app</span></span>

<span data-ttu-id="3bed7-156">Ověřování a autorizace API podporují v Angular šablona se nachází v jeho vlastní Angular modulu *ClientApp\src\api autorizace* adresáře.</span><span class="sxs-lookup"><span data-stu-id="3bed7-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="3bed7-157">Modul se skládá z následujících elementů:</span><span class="sxs-lookup"><span data-stu-id="3bed7-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="3bed7-158">3 součásti:</span><span class="sxs-lookup"><span data-stu-id="3bed7-158">3 components:</span></span>
  * <span data-ttu-id="3bed7-159">*Login.Component.TS*: Zpracuje proces přihlášení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="3bed7-160">*logout.Component.TS*: Zpracovává odhlášení toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="3bed7-161">*login-menu.component.ts*: Widget, který zobrazí jedno z následujících sad odkazů:</span><span class="sxs-lookup"><span data-stu-id="3bed7-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="3bed7-162">Správa profilů uživatelů a odkazy, když je uživatel ověřený při odhlášení.</span><span class="sxs-lookup"><span data-stu-id="3bed7-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="3bed7-163">Registrace a přihlášení odkazy, když uživatel není ověřen.</span><span class="sxs-lookup"><span data-stu-id="3bed7-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="3bed7-164">Trasa guard `AuthorizeGuard` , které mohou být přidány do cesty a vyžaduje, uživatel k ověření před návštěvou trasy.</span><span class="sxs-lookup"><span data-stu-id="3bed7-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="3bed7-165">Protokolu HTTP zachycování `AuthorizeInterceptor` přístupový token, který připojí k cílení na rozhraní API, když je uživatel ověřený odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bed7-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="3bed7-166">Služba `AuthorizeService` , který zpracovává podrobnosti nižší úrovně o procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.</span><span class="sxs-lookup"><span data-stu-id="3bed7-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="3bed7-167">Angular modul, který definuje trasy, které jsou přidružené k ověřování části aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="3bed7-168">Zpřístupňuje komponenta nabídky přihlášení, sběrač, ochranného zařízení a služby pro použití na zbývající části aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="3bed7-169">Obecný popis aplikace React</span><span class="sxs-lookup"><span data-stu-id="3bed7-169">General description of the React app</span></span>

<span data-ttu-id="3bed7-170">Podpora pro ověřování a autorizace rozhraní API v šabloně React se nachází v *ClientApp\src\components\api autorizace* adresáře.</span><span class="sxs-lookup"><span data-stu-id="3bed7-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="3bed7-171">Se skládá z následujících elementů:</span><span class="sxs-lookup"><span data-stu-id="3bed7-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="3bed7-172">4 komponenty:</span><span class="sxs-lookup"><span data-stu-id="3bed7-172">4 components:</span></span>
  * <span data-ttu-id="3bed7-173">*Login.js*: Zpracuje proces přihlášení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="3bed7-174">*Logout.js*: Zpracovává odhlášení toku aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="3bed7-175">*LoginMenu.js*: Widget, který zobrazí jedno z následujících sad odkazů:</span><span class="sxs-lookup"><span data-stu-id="3bed7-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="3bed7-176">Správa profilů uživatelů a odkazy, když je uživatel ověřený při odhlášení.</span><span class="sxs-lookup"><span data-stu-id="3bed7-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="3bed7-177">Registrace a přihlášení odkazy, když uživatel není ověřen.</span><span class="sxs-lookup"><span data-stu-id="3bed7-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="3bed7-178">*AuthorizeRoute.js*: Podle postupu součást, která vyžaduje uživatele k ověření před vykreslením komponentu `Component` parametru.</span><span class="sxs-lookup"><span data-stu-id="3bed7-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="3bed7-179">Exportovaná `authService` instance třídy `AuthorizeService` , který zpracovává podrobnosti nižší úrovně o procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.</span><span class="sxs-lookup"><span data-stu-id="3bed7-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="3bed7-180">Teď, když jste viděli základních komponent řešení, můžete využít najdete podrobnější přehled jednotlivých scénářů aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="3bed7-181">Vyžadovat ověření na nové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3bed7-181">Require authorization on a new API</span></span>

<span data-ttu-id="3bed7-182">Ve výchozím nastavení systém je nakonfigurován tak, aby vyžadovala snadno autorizace pro nové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3bed7-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="3bed7-183">Uděláte to tak, vytvořte nový kontroler a přidejte `[Authorize]` atribut třídy kontroleru nebo na každou akci v rámci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3bed7-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="3bed7-184">Ochrana trasu na straně klienta (Angular)</span><span class="sxs-lookup"><span data-stu-id="3bed7-184">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="3bed7-185">Ochrana trasu na straně klienta se provádí tak, že přidáte authorize guard do seznamu chrání ke spuštění při konfiguraci směrování.</span><span class="sxs-lookup"><span data-stu-id="3bed7-185">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="3bed7-186">Jako příklad najdete v tématu Jak `fetch-data` je trasa nakonfigurována v hlavní aplikaci Angular modulu:</span><span class="sxs-lookup"><span data-stu-id="3bed7-186">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="3bed7-187">Je důležité zmínit, že ochrana trasu nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut), ale, že ji pouze znemožní uživateli navigace pro danou trasu na straně klienta, když není ověřený.</span><span class="sxs-lookup"><span data-stu-id="3bed7-187">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="3bed7-188">Ověření požadavků rozhraní API (Angular)</span><span class="sxs-lookup"><span data-stu-id="3bed7-188">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="3bed7-189">Ověřování požadavků na rozhraní API hostovaná aplikace se provádí automaticky pomocí zachycování klienta HTTP definované aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bed7-189">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="3bed7-190">Ochrana trasu na straně klienta (React)</span><span class="sxs-lookup"><span data-stu-id="3bed7-190">Protect a client-side route (React)</span></span>

<span data-ttu-id="3bed7-191">Ochrana trasu na straně klienta pomocí `AuthorizeRoute` komponentu místo prostý `Route` komponenty.</span><span class="sxs-lookup"><span data-stu-id="3bed7-191">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="3bed7-192">Například, Všimněte si, jak `fetch-data` je trasa nakonfigurována v rámci `App` komponenty:</span><span class="sxs-lookup"><span data-stu-id="3bed7-192">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="3bed7-193">Ochrana trasy:</span><span class="sxs-lookup"><span data-stu-id="3bed7-193">Protecting a route:</span></span>

* <span data-ttu-id="3bed7-194">Nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut).</span><span class="sxs-lookup"><span data-stu-id="3bed7-194">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="3bed7-195">Pouze znemožní uživateli navigace pro danou trasu na straně klienta, když není ověřený.</span><span class="sxs-lookup"><span data-stu-id="3bed7-195">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="3bed7-196">Ověření požadavků rozhraní API (React)</span><span class="sxs-lookup"><span data-stu-id="3bed7-196">Authenticate API requests (React)</span></span>

<span data-ttu-id="3bed7-197">Ověřování požadavků pomocí React probíhá v první import `authService` z instance `AuthorizeService`.</span><span class="sxs-lookup"><span data-stu-id="3bed7-197">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="3bed7-198">Přístupový token je načten z `authService` je připojená k požadavku, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="3bed7-198">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="3bed7-199">V součástech React tuto práci se obvykle provádí `componentDidMount` životního cyklu metody nebo jako výsledek z některých interakci s uživatelem.</span><span class="sxs-lookup"><span data-stu-id="3bed7-199">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="3bed7-200">Importovat authService do komponenty</span><span class="sxs-lookup"><span data-stu-id="3bed7-200">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="3bed7-201">Načíst a připojit přístupového tokenu do odpovědi</span><span class="sxs-lookup"><span data-stu-id="3bed7-201">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="3bed7-202">Nasazení do produkčního prostředí</span><span class="sxs-lookup"><span data-stu-id="3bed7-202">Deploy to production</span></span>

<span data-ttu-id="3bed7-203">Nasazení aplikace do produkčního prostředí, třeba zřídí následující prostředky:</span><span class="sxs-lookup"><span data-stu-id="3bed7-203">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="3bed7-204">Databázi k ukládání uživatelských účtů identit a IdentityServer uděluje.</span><span class="sxs-lookup"><span data-stu-id="3bed7-204">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="3bed7-205">Produkční certifikát používaný k podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="3bed7-205">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="3bed7-206">Neexistují žádné zvláštní požadavky tohoto certifikátu; může být certifikát podepsaný svým držitelem nebo certifikát poskytované prostřednictvím Certifikační autority.</span><span class="sxs-lookup"><span data-stu-id="3bed7-206">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="3bed7-207">Mohou být generovány prostřednictvím standardních nástrojů jako PowerShell nebo OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="3bed7-207">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="3bed7-208">Můžete nainstalovat do úložiště certifikátů na cílových počítačích nebo nasadit jako *.pfx* souboru silným heslem.</span><span class="sxs-lookup"><span data-stu-id="3bed7-208">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="3bed7-209">Příklad: Nasazení do Azure Websites</span><span class="sxs-lookup"><span data-stu-id="3bed7-209">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="3bed7-210">Tato část popisuje nasazení aplikace na Azure websites pomocí certifikátu uloženého v úložišti certifikátů.</span><span class="sxs-lookup"><span data-stu-id="3bed7-210">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="3bed7-211">Pokud chcete upravit aplikaci načíst certifikát z úložiště certifikátů, plán služby App Service musí být na alespoň na úrovni Standard při konfiguraci v pozdějším kroku.</span><span class="sxs-lookup"><span data-stu-id="3bed7-211">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="3bed7-212">V aplikaci prvku *appsettings.json* souboru, upravte `IdentityServer` části Zahrnout podrobnosti klíče:</span><span class="sxs-lookup"><span data-stu-id="3bed7-212">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="3bed7-213">Vlastnost name u certifikátu odpovídá rozlišující subjekt certifikátu.</span><span class="sxs-lookup"><span data-stu-id="3bed7-213">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="3bed7-214">Představuje umístění úložiště, kam se načíst certifikát z (`CurrentUser` nebo `LocalMachine`).</span><span class="sxs-lookup"><span data-stu-id="3bed7-214">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="3bed7-215">Název úložiště představuje název úložiště certifikátů, kdy certifikát je uložen.</span><span class="sxs-lookup"><span data-stu-id="3bed7-215">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="3bed7-216">V takovém případě odkazuje na úložiště osobní uživatele.</span><span class="sxs-lookup"><span data-stu-id="3bed7-216">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="3bed7-217">Pokud chcete nasadit na weby Azure, nasazení aplikace podle kroků v [aplikaci nasadit do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) prostředky Azure potřebné pro vytvoření a nasazení aplikace do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="3bed7-217">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="3bed7-218">Po provedení předchozích kroků, aplikace se nasadí do Azure, ale ještě není funkční.</span><span class="sxs-lookup"><span data-stu-id="3bed7-218">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="3bed7-219">Certifikát používaný aplikací je stále potřeba nastavit.</span><span class="sxs-lookup"><span data-stu-id="3bed7-219">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="3bed7-220">Vyhledejte kryptografický otisk certifikátu, který se má použít a postupujte podle kroků popsaných v [načíst vaše certifikáty](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="3bed7-220">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="3bed7-221">Přestože tyto kroky zmiňovat SSL, je **privátní certifikáty** části na portálu, kde můžete nahrát zřízené certifikátu pro použití s aplikací.</span><span class="sxs-lookup"><span data-stu-id="3bed7-221">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="3bed7-222">Po provedení tohoto kroku, restartujte aplikaci a měla by být funkční.</span><span class="sxs-lookup"><span data-stu-id="3bed7-222">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="3bed7-223">Další možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="3bed7-223">Other configuration options</span></span>

<span data-ttu-id="3bed7-224">Podpora rozhraní API autorizace postavená na IdentityServer sadu konvence, výchozí hodnoty a vylepšení prostředí pro SPA zjednodušit.</span><span class="sxs-lookup"><span data-stu-id="3bed7-224">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="3bed7-225">Needless znamená potenciál IdentityServer je k dispozici na pozadí na integrace ASP.NET Core, které nepokrývá váš scénář.</span><span class="sxs-lookup"><span data-stu-id="3bed7-225">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="3bed7-226">Podpora technologie ASP.NET Core se zaměřuje na "vlastní" aplikace Microsoftu, kde jsou všechny aplikace vytvoření a nasazení naší organizace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-226">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="3bed7-227">V důsledku toho není dostupná podpora pro takové věci, třeba souhlas nebo federace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-227">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="3bed7-228">Pro tyto scénáře použití IdentityServer a postupujte podle jejich dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="3bed7-228">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="3bed7-229">Profily aplikací</span><span class="sxs-lookup"><span data-stu-id="3bed7-229">Application profiles</span></span>

<span data-ttu-id="3bed7-230">Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále určují své parametry.</span><span class="sxs-lookup"><span data-stu-id="3bed7-230">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="3bed7-231">V současné době jsou podporovány následující profily:</span><span class="sxs-lookup"><span data-stu-id="3bed7-231">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="3bed7-232">`IdentityServerSPA`: Představuje SPA hostované vedle IdentityServer jako jeden celek.</span><span class="sxs-lookup"><span data-stu-id="3bed7-232">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="3bed7-233">`redirect_uri` Výchozí hodnota je `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="3bed7-233">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="3bed7-234">`post_logout_redirect_uri` Výchozí hodnota je `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="3bed7-234">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="3bed7-235">Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bed7-235">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="3bed7-236">Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="3bed7-236">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="3bed7-237">Režim povolený odpovědi je `fragment`.</span><span class="sxs-lookup"><span data-stu-id="3bed7-237">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="3bed7-238">`SPA`: Představuje SPA, která není hostována s IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="3bed7-238">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="3bed7-239">Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3bed7-239">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="3bed7-240">Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="3bed7-240">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="3bed7-241">Režim povolený odpovědi je `fragment`.</span><span class="sxs-lookup"><span data-stu-id="3bed7-241">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="3bed7-242">`IdentityServerJwt`: Představuje rozhraní API, která je hostována společně s IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="3bed7-242">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="3bed7-243">Aplikace má nastavený jeden obor, která jako výchozí název aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-243">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="3bed7-244">`API`: Představuje rozhraní API, která není hostována s IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="3bed7-244">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="3bed7-245">Aplikace má nastavený jeden obor, která jako výchozí název aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bed7-245">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="3bed7-246">Konfigurace prostřednictvím AppSettings</span><span class="sxs-lookup"><span data-stu-id="3bed7-246">Configuration through AppSettings</span></span>

<span data-ttu-id="3bed7-247">Konfigurace aplikací pomocí konfiguračního systému jejich přidáním do seznamu `Clients` nebo `Resources`.</span><span class="sxs-lookup"><span data-stu-id="3bed7-247">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="3bed7-248">Konfigurovat každý klient `redirect_uri` a `post_logout_redirect_uri` vlastnost, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3bed7-248">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="3bed7-249">Při konfiguraci prostředků, můžete nakonfigurovat obory pro prostředek, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="3bed7-249">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="3bed7-250">Konfigurace prostřednictvím kódu</span><span class="sxs-lookup"><span data-stu-id="3bed7-250">Configuration through code</span></span>

<span data-ttu-id="3bed7-251">Můžete taky nakonfigurovat klienty a prostředků prostřednictvím kódu pomocí přetížení `AddApiAuthorization` , která má akci, kterou chcete nakonfigurovat možnosti.</span><span class="sxs-lookup"><span data-stu-id="3bed7-251">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3bed7-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3bed7-252">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
