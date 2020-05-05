---
title: Sdílení souborů cookie ověřování mezi ASP.NET aplikacemi
author: rick-anderson
description: Naučte se sdílet soubory cookie ověřování mezi ASP.NET 4. x a ASP.NET Core aplikacemi.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: a6aac53008e634e87b18bd34b3d2babdad74ae50
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776139"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="a5639-103">Sdílení souborů cookie ověřování mezi ASP.NET aplikacemi</span><span class="sxs-lookup"><span data-stu-id="a5639-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="a5639-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a5639-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a5639-105">Weby se často skládají z jednotlivých vzájemně pracujících webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="a5639-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="a5639-106">Aby bylo možné zajistit jednotné přihlašování (SSO), musí webové aplikace v rámci lokality sdílet soubory cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="a5639-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="a5639-107">Pro podporu tohoto scénáře umožňuje zásobník ochrany dat sdílení Katana souborů cookie a ověřovacích lístků pro ASP.NET Core souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="a5639-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="a5639-108">V následujících příkladech:</span><span class="sxs-lookup"><span data-stu-id="a5639-108">In the examples that follow:</span></span>

* <span data-ttu-id="a5639-109">Název souboru cookie ověřování je nastaven na společnou hodnotu `.AspNet.SharedCookie`.</span><span class="sxs-lookup"><span data-stu-id="a5639-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="a5639-110">`AuthenticationType` Je nastaven `Identity.Application` buď explicitně, nebo jako výchozí.</span><span class="sxs-lookup"><span data-stu-id="a5639-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="a5639-111">Běžný název aplikace se používá k tomu, aby systém ochrany dat mohl sdílet klíče ochrany dat (`SharedCookieApp`).</span><span class="sxs-lookup"><span data-stu-id="a5639-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="a5639-112">`Identity.Application`slouží jako schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="a5639-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="a5639-113">Bez ohledu na to, jaké schéma se používá, se musí používat konzistentně v rámci aplikace sdílené soubory cookie *a napříč* nimi, a to buď jako výchozí schéma, nebo explicitně nastavením.</span><span class="sxs-lookup"><span data-stu-id="a5639-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="a5639-114">Schéma se používá při šifrování a dešifrování souborů cookie, takže v aplikacích je nutné použít konzistentní schéma.</span><span class="sxs-lookup"><span data-stu-id="a5639-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="a5639-115">Používá se společné umístění úložiště [klíčů pro ochranu dat](xref:security/data-protection/implementation/key-management) .</span><span class="sxs-lookup"><span data-stu-id="a5639-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="a5639-116">V ASP.NET Core aplikace <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> se používá k nastavení umístění úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="a5639-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="a5639-117">V .NET Frameworkch aplikacích používá middleware pro <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>ověřování souborů cookie implementaci.</span><span class="sxs-lookup"><span data-stu-id="a5639-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="a5639-118">`DataProtectionProvider`poskytuje služby ochrany dat pro šifrování a dešifrování dat datové části ověřovacího souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="a5639-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="a5639-119">`DataProtectionProvider` Instance je izolovaná od systému ochrany dat, který používají jiné části aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5639-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="a5639-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) přijme a <xref:System.IO.DirectoryInfo> určí umístění pro úložiště klíčů ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="a5639-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="a5639-121">`DataProtectionProvider`vyžaduje balíček NuGet [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="a5639-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="a5639-122">V aplikacích ASP.NET Core 2. x odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a5639-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="a5639-123">V .NET Framework aplikace přidejte odkaz na balíček do [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="a5639-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="a5639-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Nastaví běžný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5639-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a><span data-ttu-id="a5639-125">Sdílení souborů cookie ověřování pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="a5639-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="a5639-126">Při použití ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="a5639-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="a5639-127">Klíče ochrany dat a název aplikace se musí sdílet mezi aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="a5639-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="a5639-128">K <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> metodě v následujících příkladech je poskytováno společné umístění úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="a5639-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="a5639-129">Použijte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> ke konfiguraci společného názvu sdílené aplikace (`SharedCookieApp` v následujících příkladech).</span><span class="sxs-lookup"><span data-stu-id="a5639-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="a5639-130">Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="a5639-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="a5639-131">Použijte metodu <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> rozšíření k nastavení služby ochrany dat pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="a5639-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="a5639-132">Výchozí typ ověřování je `Identity.Application`.</span><span class="sxs-lookup"><span data-stu-id="a5639-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="a5639-133">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a5639-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a><span data-ttu-id="a5639-134">Sdílet ověřování souborů cookie bez ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="a5639-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="a5639-135">Při použití souborů cookie přímo bez IdentityASP.NET Core nakonfigurujte ochranu a ověřování dat v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a5639-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a5639-136">V následujícím příkladu je typ ověřování nastaven na `Identity.Application`:</span><span class="sxs-lookup"><span data-stu-id="a5639-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

## <a name="share-cookies-across-different-base-paths"></a><span data-ttu-id="a5639-137">Sdílení souborů cookie napříč různými základními cestami</span><span class="sxs-lookup"><span data-stu-id="a5639-137">Share cookies across different base paths</span></span>

<span data-ttu-id="a5639-138">Ověřovací soubor cookie používá jako výchozí soubor cookie. [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="a5639-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="a5639-139">Pokud se soubor cookie aplikace musí sdílet mezi různými základními cestami `Path` , musí se přepsat:</span><span class="sxs-lookup"><span data-stu-id="a5639-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a><span data-ttu-id="a5639-140">Sdílení souborů cookie napříč subdoménami</span><span class="sxs-lookup"><span data-stu-id="a5639-140">Share cookies across subdomains</span></span>

<span data-ttu-id="a5639-141">Při hostování aplikací sdílejících soubory cookie napříč subdoménami zadejte společnou doménu ve vlastnosti [cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="a5639-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="a5639-142">Chcete-li sdílet soubory cookie `contoso.com`napříč aplikacemi, `first_subdomain.contoso.com` například `second_subdomain.contoso.com`a, zadejte `Cookie.Domain` jako `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="a5639-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="a5639-143">Šifrovat klíče ochrany dat v klidovém umístění</span><span class="sxs-lookup"><span data-stu-id="a5639-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="a5639-144">V případě nasazení v produkčním prostředí `DataProtectionProvider` nakonfigurujte šifrování klíčů v klidovém formátu pomocí DPAPI nebo certifikátu x509.</span><span class="sxs-lookup"><span data-stu-id="a5639-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="a5639-145">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="a5639-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="a5639-146">V následujícím příkladu je k <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>dispozici kryptografický otisk certifikátu:</span><span class="sxs-lookup"><span data-stu-id="a5639-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="a5639-147">Sdílení souborů cookie pro ověřování mezi ASP.NET 4. x a ASP.NET Core aplikacemi</span><span class="sxs-lookup"><span data-stu-id="a5639-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="a5639-148">Aplikace ASP.NET 4. x, které používají middleware ověřování souborů cookie Katana, se dají nakonfigurovat tak, aby vygenerovaly soubory cookie ověřování, které jsou kompatibilní s middlewarem ověřování souborů cookie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5639-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="a5639-149">To umožňuje v několika krocích upgradovat jednotlivé aplikace velké lokality a zajistit tak hladké možnosti jednotného přihlašování napříč lokalitami.</span><span class="sxs-lookup"><span data-stu-id="a5639-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="a5639-150">Když aplikace používá middleware ověřování Katana souborů cookie, volá `UseCookieAuthentication` se v souboru *Startup.auth.cs* projektu.</span><span class="sxs-lookup"><span data-stu-id="a5639-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="a5639-151">Projekty webové aplikace ASP.NET 4. x vytvořené pomocí Visual Studio 2013 a později ve výchozím nastavení používají middleware ověřování souborů cookie Katana.</span><span class="sxs-lookup"><span data-stu-id="a5639-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="a5639-152">I `UseCookieAuthentication` když je zastaralá a podporovaná pro `UseCookieAuthentication` ASP.NET Core aplikace, volání v aplikaci ASP.NET 4. x, která používá middleware ověřování souborů cookie Katana, je platná.</span><span class="sxs-lookup"><span data-stu-id="a5639-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="a5639-153">Aplikace ASP.NET 4. x musí cílit na .NET Framework 4.5.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="a5639-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="a5639-154">V opačném případě se instalace nezbytných balíčků NuGet nezdařila.</span><span class="sxs-lookup"><span data-stu-id="a5639-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="a5639-155">Pokud chcete soubory cookie pro ověřování sdílet mezi aplikací ASP.NET 4. x a aplikací ASP.NET Core, nakonfigurujte aplikaci ASP.NET Core tak, jak je uvedeno v části [sdílení ověřovacích souborů cookie mezi ASP.NET Coremi aplikacemi](#share-authentication-cookies-with-aspnet-core-identity) , a pak nakonfigurujte aplikaci ASP.NET 4. x následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="a5639-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="a5639-156">Zkontrolujte, jestli jsou balíčky aplikace aktualizované na nejnovější verze.</span><span class="sxs-lookup"><span data-stu-id="a5639-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="a5639-157">Do každé aplikace ASP.NET 4. x nainstalujte balíček [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) .</span><span class="sxs-lookup"><span data-stu-id="a5639-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="a5639-158">Vyhledejte a upravte volání na `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="a5639-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="a5639-159">Změňte název souboru cookie tak, aby odpovídal názvu, který používá middleware pro ověřování`.AspNet.SharedCookie` souborů cookie ASP.NET Core (v příkladu).</span><span class="sxs-lookup"><span data-stu-id="a5639-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="a5639-160">V následujícím příkladu je typ ověřování nastaven na `Identity.Application`.</span><span class="sxs-lookup"><span data-stu-id="a5639-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="a5639-161">Zadejte instanci `DataProtectionProvider` inicializovaného do umístění úložiště klíčů pro Common data Protection.</span><span class="sxs-lookup"><span data-stu-id="a5639-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="a5639-162">Ověřte, že je název aplikace nastavený na společný název aplikace používaný všemi aplikacemi, které sdílejí soubory cookie ověřování (`SharedCookieApp` v tomto příkladu).</span><span class="sxs-lookup"><span data-stu-id="a5639-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="a5639-163">Pokud není `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` nastavené `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`a, <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> nastavte na deklaraci identity, která rozlišuje jedinečné uživatele.</span><span class="sxs-lookup"><span data-stu-id="a5639-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="a5639-164">*App_start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5639-164">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="a5639-165">Při generování identity uživatele musí typ ověřování`Identity.Application`() odpovídat typu definovanému `AuthenticationType` `UseCookieAuthentication` v *app_start/Startup.auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="a5639-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="a5639-166">*Modely/IdentityModels. cs*:</span><span class="sxs-lookup"><span data-stu-id="a5639-166">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="a5639-167">Použití společné uživatelské databáze</span><span class="sxs-lookup"><span data-stu-id="a5639-167">Use a common user database</span></span>

<span data-ttu-id="a5639-168">Když aplikace používají stejné Identity schéma (stejné verze Identity), zkontrolujte, že je Identity systém pro každou aplikaci odkazován na stejnou uživatelskou databázi.</span><span class="sxs-lookup"><span data-stu-id="a5639-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="a5639-169">V opačném případě systém identit vytvoří selhání za běhu při pokusu o shodu s informacemi v ověřovacím souboru cookie proti informacím v příslušné databázi.</span><span class="sxs-lookup"><span data-stu-id="a5639-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="a5639-170">Když se Identity schéma mezi aplikacemi liší, obvykle protože aplikace používají různé Identity verze, sdílení společné databáze na základě nejnovější verze Identity není možné bez nutnosti přemapování a přidávání sloupců do Identity schémat jiných aplikací.</span><span class="sxs-lookup"><span data-stu-id="a5639-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="a5639-171">Je často efektivnější upgradovat ostatní aplikace tak, aby používaly nejnovější Identity verzi, aby bylo možné sdílet aplikace do společné databáze.</span><span class="sxs-lookup"><span data-stu-id="a5639-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5639-172">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a5639-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
