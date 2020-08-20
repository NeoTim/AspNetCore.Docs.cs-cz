---
title: Sdílení ověřování cookie s mezi ASP.NET aplikacemi
author: rick-anderson
description: Přečtěte si, jak sdílet ověřování cookie s mezi ASP.NET 4. x a ASP.NET Core aplikacemi.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 6ac808d11790ae27e82606b442ff215d95b93e41
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631365"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="659f1-103">Sdílení ověřování cookie s mezi ASP.NET aplikacemi</span><span class="sxs-lookup"><span data-stu-id="659f1-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="659f1-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="659f1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="659f1-105">Weby se často skládají z jednotlivých vzájemně pracujících webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="659f1-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="659f1-106">Aby bylo možné zajistit jednotné přihlašování (SSO), musí webové aplikace v rámci lokality sdílet ověřování cookie s.</span><span class="sxs-lookup"><span data-stu-id="659f1-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="659f1-107">Pro podporu tohoto scénáře umožňuje zásobník ochrany dat sdílení Katana cookie ověřování a ověřovacích cookie lístků pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="659f1-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="659f1-108">V následujících příkladech:</span><span class="sxs-lookup"><span data-stu-id="659f1-108">In the examples that follow:</span></span>

* <span data-ttu-id="659f1-109">Název ověřování cookie je nastaven na běžnou hodnotu `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="659f1-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="659f1-110">`AuthenticationType`Je nastaven `Identity.Application` buď explicitně, nebo jako výchozí.</span><span class="sxs-lookup"><span data-stu-id="659f1-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="659f1-111">Běžný název aplikace se používá k tomu, aby systém ochrany dat mohl sdílet klíče ochrany dat ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="659f1-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="659f1-112">`Identity.Application` slouží jako schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="659f1-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="659f1-113">Bez ohledu na to, jaké schéma se používá, je potřeba ho konzistentně používat *v rámci a napříč* sdílenými cookie aplikacemi buď jako výchozí schéma, nebo explicitně nastavením.</span><span class="sxs-lookup"><span data-stu-id="659f1-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="659f1-114">Schéma se používá při šifrování a dešifrování cookie s, takže v aplikacích je nutné použít konzistentní schéma.</span><span class="sxs-lookup"><span data-stu-id="659f1-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="659f1-115">Používá se společné umístění úložiště [klíčů pro ochranu dat](xref:security/data-protection/implementation/key-management) .</span><span class="sxs-lookup"><span data-stu-id="659f1-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="659f1-116">V ASP.NET Core aplikace <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> se používá k nastavení umístění úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="659f1-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="659f1-117">V .NET Framework aplikace Cookie používá middleware ověřování implementaci <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="659f1-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="659f1-118">`DataProtectionProvider` poskytuje služby ochrany dat pro šifrování a dešifrování cookie dat datové části ověřování.</span><span class="sxs-lookup"><span data-stu-id="659f1-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="659f1-119">`DataProtectionProvider`Instance je izolovaná od systému ochrany dat, který používají jiné části aplikace.</span><span class="sxs-lookup"><span data-stu-id="659f1-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="659f1-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) přijme a <xref:System.IO.DirectoryInfo> určí umístění pro úložiště klíčů ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="659f1-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="659f1-121">`DataProtectionProvider` vyžaduje balíček NuGet [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="659f1-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="659f1-122">V aplikacích ASP.NET Core 2. x odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="659f1-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="659f1-123">V .NET Framework aplikace přidejte odkaz na balíček do [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="659f1-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="659f1-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Nastaví běžný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="659f1-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="659f1-125">Sdílet ověřování cookie s ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="659f1-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="659f1-126">Při použití ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="659f1-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="659f1-127">Klíče ochrany dat a název aplikace se musí sdílet mezi aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="659f1-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="659f1-128">K <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> metodě v následujících příkladech je poskytováno společné umístění úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="659f1-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="659f1-129">Použijte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> ke konfiguraci společného názvu sdílené aplikace ( `SharedCookieApp` v následujících příkladech).</span><span class="sxs-lookup"><span data-stu-id="659f1-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="659f1-130">Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="659f1-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="659f1-131">Použijte <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metodu rozšíření k nastavení služby ochrany dat pro cookie s.</span><span class="sxs-lookup"><span data-stu-id="659f1-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="659f1-132">Výchozí typ ověřování je `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="659f1-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="659f1-133">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="659f1-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="659f1-134">Sdílet ověřování cookie s bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="659f1-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="659f1-135">Při použití cookie s přímo bez ASP.NET Core Identity toho nakonfigurujte ochranu a ověřování dat v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="659f1-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="659f1-136">V následujícím příkladu je typ ověřování nastaven na `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="659f1-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="659f1-137">Sdílet cookie s v různých základních cestách</span><span class="sxs-lookup"><span data-stu-id="659f1-137">Share cookies across different base paths</span></span>

<span data-ttu-id="659f1-138">Ověřování cookie používá jako výchozí hodnotu [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ Cookie . Cesta](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="659f1-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="659f1-139">Pokud musí být aplikace cookie sdílená mezi různými základními cestami, `Path` musí být přepsána:</span><span class="sxs-lookup"><span data-stu-id="659f1-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="659f1-140">Sdílet cookie s mezi subdoménami</span><span class="sxs-lookup"><span data-stu-id="659f1-140">Share cookies across subdomains</span></span>

<span data-ttu-id="659f1-141">Při hostování aplikací, které sdílí cookie s mezi subdoménami, určete v nástroji společnou doménu [ Cookie . Doménová](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="659f1-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="659f1-142">Pokud chcete sdílet cookie s napříč aplikacemi `contoso.com` , například `first_subdomain.contoso.com` a `second_subdomain.contoso.com` , zadejte `Cookie.Domain` jako `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="659f1-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="659f1-143">Šifrovat klíče ochrany dat v klidovém umístění</span><span class="sxs-lookup"><span data-stu-id="659f1-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="659f1-144">V případě nasazení v produkčním `DataProtectionProvider` prostředí nakonfigurujte šifrování klíčů v klidovém formátu pomocí DPAPI nebo certifikátu x509.</span><span class="sxs-lookup"><span data-stu-id="659f1-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="659f1-145">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="659f1-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="659f1-146">V následujícím příkladu je k dispozici kryptografický otisk certifikátu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="659f1-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="659f1-147">Sdílení ověřování cookie s mezi ASP.NET 4. x a ASP.NET Core aplikacemi</span><span class="sxs-lookup"><span data-stu-id="659f1-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="659f1-148">Aplikace ASP.NET 4. x, které používají Cookie middleware pro ověřování Katana, se dají nakonfigurovat tak, aby vygenerovaly ověřování cookie s, které jsou kompatibilní s Cookie middlewarem ověřování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="659f1-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="659f1-149">To umožňuje v několika krocích upgradovat jednotlivé aplikace velké lokality a zajistit tak hladké možnosti jednotného přihlašování napříč lokalitami.</span><span class="sxs-lookup"><span data-stu-id="659f1-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="659f1-150">Když aplikace používá Cookie middleware ověřování Katana, volá `UseCookieAuthentication` v souboru *Startup.auth.cs* projektu.</span><span class="sxs-lookup"><span data-stu-id="659f1-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="659f1-151">Projekty webové aplikace ASP.NET 4. x vytvořené pomocí Visual Studio 2013 a později Cookie ve výchozím nastavení používají middleware Katana Authentication.</span><span class="sxs-lookup"><span data-stu-id="659f1-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="659f1-152">I když `UseCookieAuthentication` je zastaralá a podporovaná pro ASP.NET Core aplikace, volání `UseCookieAuthentication` v aplikaci ASP.NET 4. x, která používá Cookie middleware ověřování Katana, je platná.</span><span class="sxs-lookup"><span data-stu-id="659f1-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="659f1-153">Aplikace ASP.NET 4. x musí cílit na .NET Framework 4.5.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="659f1-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="659f1-154">V opačném případě se instalace nezbytných balíčků NuGet nezdařila.</span><span class="sxs-lookup"><span data-stu-id="659f1-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="659f1-155">Pokud chcete sdílet ověřování cookie mezi aplikací ASP.NET 4. x a aplikací ASP.NET Core, nakonfigurujte aplikaci ASP.NET Core tak, jak je uvedeno v části [sdílení ověřování cookie s ASP.NET Core aplikací](#share-authentication-cookies-with-aspnet-core-identity) , a pak nakonfigurujte aplikaci ASP.NET 4. x následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="659f1-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="659f1-156">Zkontrolujte, jestli jsou balíčky aplikace aktualizované na nejnovější verze.</span><span class="sxs-lookup"><span data-stu-id="659f1-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="659f1-157">Do každé aplikace ASP.NET 4. x nainstalujte balíček [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) .</span><span class="sxs-lookup"><span data-stu-id="659f1-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="659f1-158">Vyhledejte a upravte volání na `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="659f1-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="659f1-159">Změňte cookie název tak, aby odpovídal názvu používanému Cookie middleware ověřování ASP.NET Core ( `.AspNet.SharedCookie` v příkladu).</span><span class="sxs-lookup"><span data-stu-id="659f1-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="659f1-160">V následujícím příkladu je typ ověřování nastaven na `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="659f1-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="659f1-161">Zadejte instanci `DataProtectionProvider` inicializovaného do umístění úložiště klíčů pro Common data Protection.</span><span class="sxs-lookup"><span data-stu-id="659f1-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="659f1-162">Ověřte, že je název aplikace nastavený na společný název aplikace používaný všemi aplikacemi, které sdílí ověřování cookie s ( `SharedCookieApp` v tomto příkladu).</span><span class="sxs-lookup"><span data-stu-id="659f1-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="659f1-163">Pokud není `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` nastavené a, nastavte <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> na deklaraci identity, která rozlišuje jedinečné uživatele.</span><span class="sxs-lookup"><span data-stu-id="659f1-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="659f1-164">*App_start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="659f1-164">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="659f1-165">Při generování identity uživatele musí typ ověřování ( `Identity.Application` ) odpovídat typu definovanému v `AuthenticationType` `UseCookieAuthentication` *app_start/Startup.auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="659f1-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="659f1-166">*Modely/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="659f1-166">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="659f1-167">Použití společné uživatelské databáze</span><span class="sxs-lookup"><span data-stu-id="659f1-167">Use a common user database</span></span>

<span data-ttu-id="659f1-168">Když aplikace používají stejné Identity schéma (stejné verze Identity ), zkontrolujte, že Identity je systém pro každou aplikaci odkazován na stejnou uživatelskou databázi.</span><span class="sxs-lookup"><span data-stu-id="659f1-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="659f1-169">V opačném případě systém identit vytvoří selhání za běhu při pokusu o shodu s informacemi v ověřování cookie proti informacím v příslušné databázi.</span><span class="sxs-lookup"><span data-stu-id="659f1-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="659f1-170">Když se Identity schéma mezi aplikacemi liší, obvykle protože aplikace používají různé Identity verze, sdílení společné databáze na základě nejnovější verze Identity není možné bez nutnosti přemapování a přidávání sloupců do schémat jiných aplikací Identity .</span><span class="sxs-lookup"><span data-stu-id="659f1-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="659f1-171">Je často efektivnější upgradovat ostatní aplikace tak, aby používaly nejnovější Identity verzi, aby bylo možné sdílet aplikace do společné databáze.</span><span class="sxs-lookup"><span data-stu-id="659f1-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="659f1-172">Další materiály</span><span class="sxs-lookup"><span data-stu-id="659f1-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
