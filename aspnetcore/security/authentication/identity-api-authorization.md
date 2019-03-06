---
title: Úvod k ověřování pro jednostránkové aplikace v ASP.NET Core
author: ''
description: Pomocí Identity jednostránkovou aplikaci hostované uvnitř aplikace ASP.NET Core.
ms.author: ''
ms.date: 03/05/2018
uid: security/authentication/identity/spa
ms.openlocfilehash: cf04ec1ff0ae9afea066fd1864ab0a7956ace32c
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346806"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="7e166-103">Ověřování a autorizace pro SPA</span><span class="sxs-lookup"><span data-stu-id="7e166-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="7e166-104">V technologii ASP.NET 3.0 přinášíme podporu pro ověřování v jednostránkové aplikace pomocí naši novou podporu pro ověření rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7e166-104">In ASP.NET 3.0 we are introducing support for authentication in single page applications using our new support for API authorization.</span></span> <span data-ttu-id="7e166-105">Tato podpora je založen na kombinaci ASP.NET Core Identity pro ověřování a ukládání uživatele a Identity serveru pro implementaci Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="7e166-105">This support is based on a combination of ASP.NET Core Identity for authenticating and storing users and Identity Server for implementing Open ID Connect.</span></span>

<span data-ttu-id="7e166-106">Přidali jsme nový parametr ověřování do našich Angular a React šablony, které je podobné tomuto: Parametr ověřování v našich šablon stránky mvc a razor s povolenými hodnotami 'None' a "Individuální".</span><span class="sxs-lookup"><span data-stu-id="7e166-106">We have added a new authentication parameter to our Angular and React templates that is similar to the authentication parameter in our mvc and razor pages templates with allowed values 'None' and 'Individual'.</span></span>

## <a name="create-an-angular-app-with-api-authorization-support"></a><span data-ttu-id="7e166-107">Vytvořit aplikaci Angular díky podpoře rozhraní API autorizace</span><span class="sxs-lookup"><span data-stu-id="7e166-107">Create an Angular app with API authorization support</span></span>

<span data-ttu-id="7e166-108">Pokud chcete vytvořit novou aplikaci Angular s podporou pro ověřování a autorizaci uživatelů, otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7e166-108">To create a new Angular app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="7e166-109">Předchozí příkaz vytvoří aplikace ASP.NET Core s *ClientApp* adresář obsahující aplikaci Angular.</span><span class="sxs-lookup"><span data-stu-id="7e166-109">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the Angular app.</span></span>

## <a name="create-a-react-app-with-api-authorization-support"></a><span data-ttu-id="7e166-110">Vytvoření aplikace s React díky podpoře rozhraní API autorizace</span><span class="sxs-lookup"><span data-stu-id="7e166-110">Create a React app with API authorization support</span></span>

<span data-ttu-id="7e166-111">Pokud chcete vytvořit novou aplikaci React s podporou pro ověřování a autorizaci uživatelů, otevřete příkazové okno a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7e166-111">To create a new React app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="7e166-112">Předchozí příkaz vytvoří aplikace ASP.NET Core s *ClientApp* adresář obsahující aplikaci React.</span><span class="sxs-lookup"><span data-stu-id="7e166-112">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the React app.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="7e166-113">Obecný popis součástí aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e166-113">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="7e166-114">Když jsme zahrnují podporu ověřování jsou několik dodatky k projektu:</span><span class="sxs-lookup"><span data-stu-id="7e166-114">There are several additions to the project when we include support for authentication:</span></span>

### <a name="startup-class"></a><span data-ttu-id="7e166-115">Třída při spuštění</span><span class="sxs-lookup"><span data-stu-id="7e166-115">Startup class</span></span>

<span data-ttu-id="7e166-116">Když se podíváme na kód v třídu pro spuštění vážíme následující zahrnutí:</span><span class="sxs-lookup"><span data-stu-id="7e166-116">If we look at the code in the Startup class below we can appreciate the following inclusions:</span></span>
* <span data-ttu-id="7e166-117">Uvnitř `public void ConfigureServices(IServiceCollection services)`:</span><span class="sxs-lookup"><span data-stu-id="7e166-117">Inside `public void ConfigureServices(IServiceCollection services)`:</span></span>
  * <span data-ttu-id="7e166-118">Identita s výchozí uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7e166-118">Identity with the default UI.</span></span>
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * <span data-ttu-id="7e166-119">Server identit s další Pomocná metoda AddApiAuthorization tohoto nastavení některé výchozí konvence ASP.NET na serveru identit.</span><span class="sxs-lookup"><span data-stu-id="7e166-119">Identity Server with an additional AddApiAuthorization helper method that setups some default ASP.NET Conventions on top of Identity Server.</span></span>
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * <span data-ttu-id="7e166-120">Ověřování pomocí další Pomocná metoda AddIdentityServerJwt, který konfiguruje aplikaci, aby ověřoval tokeny Jwt vytvářených Identity serveru.</span><span class="sxs-lookup"><span data-stu-id="7e166-120">Authentication with an additional AddIdentityServerJwt helper method that configures the application to validate Jwt tokens produced by Identity Server.</span></span> 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* <span data-ttu-id="7e166-121">Uvnitř `public void Configure(IApplicationBuilder app)`:</span><span class="sxs-lookup"><span data-stu-id="7e166-121">Inside `public void Configure(IApplicationBuilder app)`:</span></span>
  * <span data-ttu-id="7e166-122">Ověřovací middleware, který je zodpovědný za ověřují se přihlašovací údaje v příchozím požadavku a nastavení uživatele v kontextu požadavku.</span><span class="sxs-lookup"><span data-stu-id="7e166-122">The authentication middleware that is responsible for validating the credentials in the incoming request and setting the user on the request context.</span></span>
    ```csharp
    app.UseAuthentication();
    ```
  * <span data-ttu-id="7e166-123">Middleware identity serveru, který zveřejňuje koncové body Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="7e166-123">The identity server middleware that exposes the Open ID Connect endpoints.</span></span>
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="7e166-124">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7e166-124">AddApiAuthorization</span></span> 
<span data-ttu-id="7e166-125">Tato pomocná metoda nakonfiguruje Server Identity použít naše podporovanou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="7e166-125">This helper method configures Identity Server to use our supported configuration.</span></span> <span data-ttu-id="7e166-126">Identita serveru je velmi výkonný a rozšiřitelný rámec pro zpracování otázky zabezpečení aplikací, ale ve stejnou dobu, která zveřejňuje spoustu složitost, která nemusíme vědět o nejběžnějších scénářů, takže jsme vyberte sadu konvence a Možnosti konfigurace, který budeme přemýšlet o jsou dobrým výchozím bodem.</span><span class="sxs-lookup"><span data-stu-id="7e166-126">Identity Server is a very powerful and extensible framework for handling application security concerns but at the same time that exposes a lot of complexity that we don't need to know about for the most common scenarios, so we choose a set of conventions and configuration options for you that we consider are a good starting point.</span></span> <span data-ttu-id="7e166-127">Po ověření měnících se požadavků všech možností Identity serveru je stále k dispozici, takže můžete přizpůsobit tak, aby odpovídala vašim potřebám.</span><span class="sxs-lookup"><span data-stu-id="7e166-127">Once your authentication needs change the full power of Identity Server is still available to you so you can customize it to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="7e166-128">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="7e166-128">AddIdentityServerJwt</span></span>
<span data-ttu-id="7e166-129">Tato pomocná metoda nakonfiguruje zásady schéma pro aplikaci jako výchozí obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="7e166-129">This helper method configures a policy scheme for the application as the default authentication handler.</span></span> <span data-ttu-id="7e166-130">Pokud chcete, aby identita zpracování všech požadavků, které jsou určeny pro všechny dílčí cestou do pole Adresa url Identity nakonfigurované "/ Identity" a chcete, aby JwtBearerHandler zpracování všech ostatních požadavků.</span><span class="sxs-lookup"><span data-stu-id="7e166-130">The policy is configured to let identity handle all the requests that go to any subpath in the Identity url space "/Identity" and to let the JwtBearerHandler handle all other requests.</span></span>
<span data-ttu-id="7e166-131">Metoda registruje Addionally `<<ApplicationName>>API` prostředku rozhraní Api serveru identit s výchozí obor `<<ApplicationName>>API` a nakonfiguruje middlewaru tokenu nosiče JWT ověření tokeny vystavené službou serveru identit pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-131">Addionally this method registers an `<<ApplicationName>>API` Api resource with identity server with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by Identity Server for the application.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="7e166-132">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="7e166-132">SampleDataController</span></span>
<span data-ttu-id="7e166-133">Když se podíváte na soubor Controllers\SampleDataController.cs můžete podle našich zkušeností `[Authorize]` atributu použít pro třídu, která označuje, že uživatel musí být povoleno podle výchozí zásady pro přístup k prostředku.</span><span class="sxs-lookup"><span data-stu-id="7e166-133">If we look at the file Controllers\SampleDataController.cs we can observe the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7e166-134">Nakonfigurovat, aby používal výchozí schéma ověřování, který je nastavený podle výchozí zásady autorizace se stane s `AddIdentityServerJwt` na schéma zásad, které už jsme zmínili výše, provádění obslužné rutiny JwtBearer nakonfiguroval tato Pomocná metoda pro obslužnou rutinu výchozí požadavky na aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e166-134">The default authorization policy happens to be configured to use the default authentication scheme which is set up by `AddIdentityServerJwt` to the policy scheme that we mentioned above, making the JwtBearer handler configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7e166-135">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7e166-135">ApplicationDbContext</span></span>
<span data-ttu-id="7e166-136">Když se podíváme na soubor na Data\ApplicationDbContext.cs vidíme stejného DbContext používáme v identitě s tím rozdílem, že rozšiřuje ApiAuthorizationDbContext (více odvozené třídy z IdentityDbContext) zahrnout schéma pro identitu serveru.</span><span class="sxs-lookup"><span data-stu-id="7e166-136">If we look at the file in Data\ApplicationDbContext.cs we can see the same DbContext we use in identity with the exception that it extends ApiAuthorizationDbContext (a more derived class from IdentityDbContext) to include the schema for Identity Server.</span></span>
<span data-ttu-id="7e166-137">Pokud se mají plnou kontrolu nad schéma databáze můžeme jednoduše dědit z jednoho z dostupných tříd Identity DbContext a nakonfigurovat kontextu se zahrnout schéma identity voláním `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` na `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="7e166-137">If we want full control of the database schema we can simply inherit from one of the available Identity DbContext classes and configure the context to include the identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7e166-138">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7e166-138">OidcConfigurationController</span></span>
<span data-ttu-id="7e166-139">Když se podíváte na soubor Controllers\OidcConfigurationController.cs vidíme, koncový bod, který jsme Týmová poskytovat OIDC parametry, které je potřeba použít klienta.</span><span class="sxs-lookup"><span data-stu-id="7e166-139">If we look at the file Controllers\OidcConfigurationController.cs we can see the endpoint that we stand-up to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="7e166-140">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="7e166-140">appsettings.json</span></span>
<span data-ttu-id="7e166-141">Když se podíváte na soubor appsettings.json v kořenovém adresáři projektu, můžeme vidět nový `IdentityServer` část popisující seznam nakonfigurovat klienty a vidíme, že je jednoho klienta.</span><span class="sxs-lookup"><span data-stu-id="7e166-141">If we look at the appsettings.json file on the root of the project, we can see a new `IdentityServer` section that describes the list of configured clients and we can see that there is a single client.</span></span> <span data-ttu-id="7e166-142">Název klienta odpovídá názvu aplikace a mapovat pomocí konvence na parametr ID klienta oAuth.</span><span class="sxs-lookup"><span data-stu-id="7e166-142">The name of the client corresponds to the name of the application and is mapped by convention to the oAuth ClientId parameter.</span></span> <span data-ttu-id="7e166-143">Profil, který označuje, jaký typ aplikace jsme konfigurace a ji interně používáme konvencím jednotky, které zjednodušují proces konfigurace pro server.</span><span class="sxs-lookup"><span data-stu-id="7e166-143">The profile indicates what type of application we are configuring, and we use it internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="7e166-144">Nejsou k dispozici několik profilů je vysvětleno v následující části.</span><span class="sxs-lookup"><span data-stu-id="7e166-144">There are several profiles available explained in the section below.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="7e166-145">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="7e166-145">appsettings.Development.json</span></span>
<span data-ttu-id="7e166-146">Když se podíváte na appsettings. Soubor Development.JSON v kořenovém adresáři projektu, můžeme vidět nový `IdentityServer` oddíl, který popisuje klíč se používá k podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="7e166-146">If we look at the appsettings.Development.json file on the root of the project, we can see a new `IdentityServer` section that describes the key we are using to sign tokens.</span></span> <span data-ttu-id="7e166-147">Při nasazení do produkčního prostředí klíč se musí zřídit a nasadit spolu s aplikace, jak je popsáno níže.</span><span class="sxs-lookup"><span data-stu-id="7e166-147">When deploying to production a key needs to be provisioned and deployed alongside the application as explained below.</span></span>

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a><span data-ttu-id="7e166-148">Obecný popis aplikaci Angular</span><span class="sxs-lookup"><span data-stu-id="7e166-148">General description of the Angular application</span></span>
<span data-ttu-id="7e166-149">Podpora pro ověřování a autorizace rozhraní API v šabloně Angular se nachází v jeho vlastní Angular modulu.</span><span class="sxs-lookup"><span data-stu-id="7e166-149">The support for authentication and API authorization in the Angular template lives in its own Angular module.</span></span> <span data-ttu-id="7e166-150">V části ClientApp\src\api autorizace a se skládá z následujících elementů:</span><span class="sxs-lookup"><span data-stu-id="7e166-150">Under ClientApp\src\api-authorization and it is composed of the following elements:</span></span>
* <span data-ttu-id="7e166-151">3 součásti:</span><span class="sxs-lookup"><span data-stu-id="7e166-151">3 Components:</span></span>
  * <span data-ttu-id="7e166-152">Součást přihlášení: Zpracuje proces přihlášení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-152">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="7e166-153">Součást odhlášení: Zpracovává odhlášení toku pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-153">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="7e166-154">Komponenta nabídky přihlášení: Widget, který zobrazuje aktuálně ověřeného uživatele pomocí odkazů spravovat profil uživatele a odhlášení nebo odkazy na Přihlaste se nebo zaregistrujte, pokud uživatel není ověřen.</span><span class="sxs-lookup"><span data-stu-id="7e166-154">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
* <span data-ttu-id="7e166-155">Trasa guard `AuthorizeGuard` , které mohou být přidány do cesty a vyžaduje, uživatel k ověření před návštěvou trasy.</span><span class="sxs-lookup"><span data-stu-id="7e166-155">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="7e166-156">Protokolu http zachycování `AuthorizeInterceptor` přístupový token, který připojí k cílení na rozhraní API, když je uživatel ověřený odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e166-156">An http interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="7e166-157">Služba `AuthorizeService` , která zpracovává nižší úroveň podrobností v procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.</span><span class="sxs-lookup"><span data-stu-id="7e166-157">A service `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>
* <span data-ttu-id="7e166-158">Angular modul, který definuje trasy, které jsou přidružené k ověřování částí aplikace a poskytuje komponenta nabídky přihlášení, sběrač, ochranného zařízení a služby pro použití na zbývající části aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e166-158">An angular module that defines routes associated with the authentication parts of the application and exposes the login menu component, the interceptor, the guard and the service for consumption from the rest of the application.</span></span>

## <a name="general-description-of-the-react-application"></a><span data-ttu-id="7e166-159">Obecný popis aplikace React</span><span class="sxs-lookup"><span data-stu-id="7e166-159">General description of the React application</span></span>
<span data-ttu-id="7e166-160">Podpora pro ověřování a autorizace rozhraní API v React život šablony v části ClientApp\src\components\api authorization\ a se skládá z následujících elementů:</span><span class="sxs-lookup"><span data-stu-id="7e166-160">The support for authentication and API authorization in the React template lives under ClientApp\src\components\api-authorization\ and it is composed of the following elements:</span></span>
* <span data-ttu-id="7e166-161">4 komponenty:</span><span class="sxs-lookup"><span data-stu-id="7e166-161">4 Components:</span></span>
  * <span data-ttu-id="7e166-162">Součást přihlášení: Zpracuje proces přihlášení pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-162">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="7e166-163">Součást odhlášení: Zpracovává odhlášení toku pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-163">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="7e166-164">Komponenta nabídky přihlášení: Widget, který zobrazuje aktuálně ověřeného uživatele pomocí odkazů spravovat profil uživatele a odhlášení nebo odkazy na Přihlaste se nebo zaregistrujte, pokud uživatel není ověřen.</span><span class="sxs-lookup"><span data-stu-id="7e166-164">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
  * <span data-ttu-id="7e166-165">AuthorizeRoute: Součást trasy, která vyžaduje uživatele k ověření před vykreslením součástí uvedené v parametru komponenty.</span><span class="sxs-lookup"><span data-stu-id="7e166-165">AuthorizeRoute: A route component that requires a user to be authenticated before rendering the component indicated in the Component parameter.</span></span>
  * <span data-ttu-id="7e166-166">Exportovaná `authService` instance třídy `AuthorizeService` , která zpracovává nižší úroveň podrobností v procesu ověřování a zveřejňuje informace o ověřeném uživateli na zbytek aplikace pro použití.</span><span class="sxs-lookup"><span data-stu-id="7e166-166">An exported `authService` instance of class `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>

<span data-ttu-id="7e166-167">Teď, když jsme se seznámili s základních komponent řešení, můžeme provést konkrétní podívejte se na jednotlivé scénáře pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="7e166-167">Now that we've seen the main components of the solution, we can take a specific look at individual scenarios for the application:</span></span>

## <a name="requiring-authorization-on-a-new-api"></a><span data-ttu-id="7e166-168">Vyžadování ověření na nové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="7e166-168">Requiring authorization on a new API</span></span>
<span data-ttu-id="7e166-169">Systém je nakonfigurován připravených k němu jednoduché vyžadují autorizace pro nové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7e166-169">The system is configured out of the box to make it trivial to require authorization for new APIs.</span></span> <span data-ttu-id="7e166-170">Aby bylo možné učinit, jednoduše vytvořte nový řadič a přidejte `[Authorize]` atribut třídy kontroleru nebo na každou akci v rámci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="7e166-170">In order to do so, simply create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protecting-a-client-side-route-angular"></a><span data-ttu-id="7e166-171">Ochrana trasu na straně klienta (Angular)</span><span class="sxs-lookup"><span data-stu-id="7e166-171">Protecting a client-side route (Angular)</span></span>
<span data-ttu-id="7e166-172">Ochrana trasu na straně klienta se provádí tak, že přidáte authorize guard do seznamu chrání ke spuštění při konfiguraci směrování.</span><span class="sxs-lookup"><span data-stu-id="7e166-172">Protecting a client side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="7e166-173">Jako příklad můžete vidět, konfigurace načítat data trasy v hlavní aplikaci angular modulu:</span><span class="sxs-lookup"><span data-stu-id="7e166-173">As an example you can see how the fetch-data route is configured within the main app angular module:</span></span>

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="7e166-174">Je důležité zmínit, že ochrana trasu nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut), ale, že ji pouze znemožní uživateli přejdete na trasu na straně daného klienta, když není ověřený.</span><span class="sxs-lookup"><span data-stu-id="7e166-174">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="7e166-175">Ověření požadavků rozhraní API (Angular)</span><span class="sxs-lookup"><span data-stu-id="7e166-175">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="7e166-176">Ověřování požadavků na rozhraní API hostovaná po straně, že aplikace se provádí automaticky pomocí zachycování klienta HTTP definované aplikací.</span><span class="sxs-lookup"><span data-stu-id="7e166-176">Authenticating requests to APIs hosted along side the application is done automatically through the use of the HTTP client interceptor defined by the application.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="7e166-177">Ochrana trasu na straně klienta (React)</span><span class="sxs-lookup"><span data-stu-id="7e166-177">Protect a client-side route (React)</span></span>

<span data-ttu-id="7e166-178">Ochrana trasu na straně klienta se provádí pomocí komponenty AuthorizeRoute místo prostého komponenty trasy.</span><span class="sxs-lookup"><span data-stu-id="7e166-178">Protecting a client side route is done by using the AuthorizeRoute component instead of the plain Route component.</span></span> <span data-ttu-id="7e166-179">Jako příklad můžete vidět, konfigurace trasy načítat data do komponenty aplikace:</span><span class="sxs-lookup"><span data-stu-id="7e166-179">As an example you can see how the fetch-data route is configured within the App component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="7e166-180">Je důležité zmínit, že ochrana trasu nechrání skutečný koncový bod (které stále vyžadují `[Authorize]` byt aplikovaný atribut), ale, že ji pouze znemožní uživateli přejdete na trasu na straně daného klienta, když není ověřený.</span><span class="sxs-lookup"><span data-stu-id="7e166-180">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="7e166-181">Ověření požadavků rozhraní API (React)</span><span class="sxs-lookup"><span data-stu-id="7e166-181">Authenticate API requests (React)</span></span>

<span data-ttu-id="7e166-182">Ověřování požadavků pomocí react probíhá v první import `authService` z instance `AuthorizeService` a načtení přístupového tokenu z authService a připojíte ho k požadavku, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="7e166-182">Authenticating requests with react is done by first importing the `authService` instance from the `AuthorizeService` and then retrieving the access token from the authService and attaching it to the request as shown below.</span></span> <span data-ttu-id="7e166-183">V součásti react to se obvykle provádí v metodě componentDidMount životního cyklu nebo jako výsledek z některých interakci s uživatelem.</span><span class="sxs-lookup"><span data-stu-id="7e166-183">In react components this is typically done in the componentDidMount lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="7e166-184">Importovat authService do komponenty</span><span class="sxs-lookup"><span data-stu-id="7e166-184">Import the authService into your component</span></span>

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="7e166-185">Načíst a připojit přístupového tokenu do odpovědi</span><span class="sxs-lookup"><span data-stu-id="7e166-185">Retrieve and attach the access token to the response</span></span>

```js
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-into-production"></a><span data-ttu-id="7e166-186">Nasazení do produkčního prostředí</span><span class="sxs-lookup"><span data-stu-id="7e166-186">Deploy into production</span></span>

<span data-ttu-id="7e166-187">Pokud chcete nasadit aplikaci do produkčního prostředí, musíme zřídit několik prostředků:</span><span class="sxs-lookup"><span data-stu-id="7e166-187">In order to deploy the application into production we need to provision several resources:</span></span>
* <span data-ttu-id="7e166-188">Uděluje databázi k ukládání uživatelských účtů identit a server identit.</span><span class="sxs-lookup"><span data-stu-id="7e166-188">A database to store the Identity user accounts and the identity server grants.</span></span>
* <span data-ttu-id="7e166-189">Produkční certifikát používaný k podepisování tokenů.</span><span class="sxs-lookup"><span data-stu-id="7e166-189">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="7e166-190">Neexistují žádné zvláštní požadavky tohoto certifikátu; může být certifikát podepsaný svým držitelem nebo certifikát poskytované prostřednictvím Certifikační autority.</span><span class="sxs-lookup"><span data-stu-id="7e166-190">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="7e166-191">Mohou být generovány prostřednictvím standardních nástrojů jako powershell nebo openssl.</span><span class="sxs-lookup"><span data-stu-id="7e166-191">It can be generated through standard tools like powershell or openssl.</span></span>
  * <span data-ttu-id="7e166-192">Můžete nainstalovat do úložiště certifikátů na cílových počítačích nebo nasadit jako soubor pfx silným heslem.</span><span class="sxs-lookup"><span data-stu-id="7e166-192">It can be installed into the certificate store on the target machines or deployed as a pfx file with a strong password.</span></span>

### <a name="example-deploy-into-azure-websites"></a><span data-ttu-id="7e166-193">Příklad: Nasazení do Azure Websites</span><span class="sxs-lookup"><span data-stu-id="7e166-193">Example: Deploy into Azure Websites</span></span>

<span data-ttu-id="7e166-194">V této části jsme se chystáte nasadit aplikaci do Azure websites pomocí certifikátu uloženého v úložišti certifikátů.</span><span class="sxs-lookup"><span data-stu-id="7e166-194">In this section we are going to deploy the application to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="7e166-195">Potřebujeme upravit aplikaci načíst certicate z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="7e166-195">We need to modify the application to load a certicate from the certificate store.</span></span> <span data-ttu-id="7e166-196">Uděláte to tak, náš plán služby app service musí být alespoň na úrovni standard, když nakonfigurujeme v pozdějším kroku.</span><span class="sxs-lookup"><span data-stu-id="7e166-196">To do so, our app service plan needs to be at least on the standard tier when we configure in a later step.</span></span> <span data-ttu-id="7e166-197">V naší aplikaci jednoduše potřebujeme upravit IdentityServer věnované appsettings.json zahrnout podrobnosti klíče:</span><span class="sxs-lookup"><span data-stu-id="7e166-197">In our application we simply need to modify the IdentityServer section on appsettings.json to include the key details:</span></span>
```json
  "IdentityServer": {
    "Key": {
      "Type": "Store",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "Name": "CN=MyApplication"
    }
  }
}
```
* <span data-ttu-id="7e166-198">Vlastnost name u certifikátu odpovídá rozlišující subjekt certifikátu.</span><span class="sxs-lookup"><span data-stu-id="7e166-198">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="7e166-199">Umístění úložiště představuje kam se mají načíst certifikát (CurrentUrser nebo LocalMachine).</span><span class="sxs-lookup"><span data-stu-id="7e166-199">The store location represents where to load the certificate from (CurrentUrser or LocalMachine).</span></span>
* <span data-ttu-id="7e166-200">Název úložiště představuje název úložiště certifikátů, kde je certifikát uložený v tomto případě odkazuje na úložiště osobní uživatele.</span><span class="sxs-lookup"><span data-stu-id="7e166-200">The store name represents the name of the certificate store where the certificate is stored, in this case it points to the personal user store.</span></span>

<span data-ttu-id="7e166-201">Pokud chcete nasadit na weby Azure, nasazení aplikace podle kroků v [aplikaci nasadit do Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) prostředky Azure potřebné pro vytvoření a nasazení aplikace do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="7e166-201">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="7e166-202">Po této, aplikace se nasazuje do Azure, ale ještě není úplně funkční jak musíme nastavit certifikát, který chcete používat aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e166-202">After doing this, the application is deployed into Azure but is not yet completely functional as we still need to setup the certificate to be used by the application.</span></span> <span data-ttu-id="7e166-203">Uděláte to tak, musíme mít kryptografický otisk certifikátu, budeme používat a postupujte podle kroků popsaných v [načíst vaše certifikáty](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span><span class="sxs-lookup"><span data-stu-id="7e166-203">To do so, we need to have the thumbprint for the certificate we are going to use and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span></span>

<span data-ttu-id="7e166-204">Zatímco tyto kroky zmíníte SSL, je na portálu, kde jsme naše zřízené certifikátu pro použití s naší aplikace můžete nahrát oddíl "Privátní certifikáty".</span><span class="sxs-lookup"><span data-stu-id="7e166-204">While these steps mention SSL, there is a "Private certificates" section on the portal where we can upload our provisioned certificate to use with our app.</span></span>

<span data-ttu-id="7e166-205">Po provedení tohoto kroku jsme měli restartovat naši aplikaci a musí být plně funkční.</span><span class="sxs-lookup"><span data-stu-id="7e166-205">After this step, we should be able to restart our application and it should be completely functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="7e166-206">Další možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="7e166-206">Other configuration options</span></span>
<span data-ttu-id="7e166-207">Naši podporu pro rozhraní API autorizace postavená na Identity serveru se sadou konvence, výchozí hodnoty a vylepšení pro zjednodušení prostředí pro jednostránkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e166-207">Our support for API authorization builds on top of Identity Server with a set of conventions, default values and enhancements to simplify the experience for Single Page Applications.</span></span> <span data-ttu-id="7e166-208">Needless znamená plný výkon serveru identit je k dispozici na pozadí na integrace, které nabízíme nepokrývá váš scénář.</span><span class="sxs-lookup"><span data-stu-id="7e166-208">Needless to say, the full power of Identity Server is available behind the scenes if the integrations that we offer don't cover your scenario.</span></span> <span data-ttu-id="7e166-209">Naše podpora se zaměřuje na čemu říkáme "vlastní" aplikace, kde jsou všechny aplikace vytvoření a nasazení naší organizace.</span><span class="sxs-lookup"><span data-stu-id="7e166-209">Our support is focused on what we call "first-party" applications, where all the applications are created and deployed by our organization.</span></span> <span data-ttu-id="7e166-210">V důsledku nenabízíme podporu pro věci, jako je souhlas nebo federace.</span><span class="sxs-lookup"><span data-stu-id="7e166-210">As such we don't offer support for things like consent or federation.</span></span> <span data-ttu-id="7e166-211">U scénářů doporučujeme používat identitu serveru a postupujte podle jejich dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-211">For those scenarios our recommendation is to use Identity Server and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="7e166-212">Profily aplikací</span><span class="sxs-lookup"><span data-stu-id="7e166-212">Application profiles</span></span>
<span data-ttu-id="7e166-213">Profily aplikací jsou předdefinované konfigurace pro aplikace, které dále určují své parametry.</span><span class="sxs-lookup"><span data-stu-id="7e166-213">Application profiles are predefined configurations for applications that further define their parameters.</span></span> <span data-ttu-id="7e166-214">V tuto chvíli podporujeme dva profily:</span><span class="sxs-lookup"><span data-stu-id="7e166-214">At this time we support two profiles:</span></span>
* <span data-ttu-id="7e166-215">IdentityServerSPA: Představuje jednostránková aplikace hostované vedle serveru identit jako jeden celek.</span><span class="sxs-lookup"><span data-stu-id="7e166-215">IdentityServerSPA: Represents a single page application hosted alongside Identity Server as a single unit.</span></span>
  * <span data-ttu-id="7e166-216">Výchozí hodnota redirect_uri `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="7e166-216">The redirect_uri defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="7e166-217">Výchozí hodnota post_logout_redirect_uri `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="7e166-217">The post_logout_redirect_uri defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="7e166-218">Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-218">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="7e166-219">Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="7e166-219">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="7e166-220">Režim povolený odpovědi je `fragment`.</span><span class="sxs-lookup"><span data-stu-id="7e166-220">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="7e166-221">JEDNOSTRÁNKOVÁ APLIKACE: Představuje jednostránková aplikace, která není hostována serverem Identity.</span><span class="sxs-lookup"><span data-stu-id="7e166-221">SPA: Represents a single page application that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="7e166-222">Zahrnuje sadu obory `openid`, `profile`a každý rozsah definovaný pro rozhraní API v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e166-222">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="7e166-223">Sada povolených typů odpovědi OIDC je `id_token token` nebo každý z nich jednotlivě (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="7e166-223">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="7e166-224">Režim povolený odpovědi je `fragment`.</span><span class="sxs-lookup"><span data-stu-id="7e166-224">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="7e166-225">IdentityServerJwt: Představuje rozhraní API, která je hostována společně s Identity serveru.</span><span class="sxs-lookup"><span data-stu-id="7e166-225">IdentityServerJwt: Represents an API that is hosted alongside with Identity Server.</span></span>
  * <span data-ttu-id="7e166-226">Aplikace má nastavený jeden obor, která jako výchozí název aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e166-226">The application is configured to have a single scope that defaults to the application name.</span></span>
* <span data-ttu-id="7e166-227">API: Představuje rozhraní API, která není hostována serverem Identity.</span><span class="sxs-lookup"><span data-stu-id="7e166-227">API: Represents an API that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="7e166-228">Aplikace má nastavený jeden obor, která jako výchozí název aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e166-228">The application is configured to have a single scope that defaults to the application name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="7e166-229">Konfigurace prostřednictvím AppSettings</span><span class="sxs-lookup"><span data-stu-id="7e166-229">Configuration through AppSettings</span></span>
<span data-ttu-id="7e166-230">Můžeme nakonfigurovat aplikace prostřednictvím našeho systému konfigurace jejich přidáním do seznamu klienty nebo prostředky v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="7e166-230">We can configure the applications through our configuration system by adding them to the list of Clients or Resources respectively.</span></span> 

<span data-ttu-id="7e166-231">Při konfiguraci klientů můžeme nakonfigurovat `redirect_uri` a `post_logout_redirect_uri` jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="7e166-231">When configuring clients we can configure the `redirect_uri` and the `post_logout_redirect_uri` as shown below:</span></span>
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

<span data-ttu-id="7e166-232">Při konfiguraci zdroje můžeme nakonfigurovat obory pro prostředek, jak je znázorněno níže:</span><span class="sxs-lookup"><span data-stu-id="7e166-232">When configuring resources we can configure the scopes for the resource as shown below:</span></span>
```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c",
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="7e166-233">Konfigurace prostřednictvím kódu</span><span class="sxs-lookup"><span data-stu-id="7e166-233">Configuration through code</span></span>
<span data-ttu-id="7e166-234">Můžeme také nakonfigurovat klienty a prostředků prostřednictvím kódu pomocí přetížení AddApiAuthorization, který provede akci ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="7e166-234">We can also configure the clients and resources through code using an overload of AddApiAuthorization that takes an action to configure options.</span></span>
```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA",
        spa => spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
            .WithLogoutRedirectUri("http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource => resource.WithScopes("a", "b", "c"));
});
```
