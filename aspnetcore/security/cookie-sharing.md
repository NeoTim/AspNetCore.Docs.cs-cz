---
title: Sdílet soubory cookie pro ověřování mezi aplikací v ASP.NET
author: rick-anderson
description: Zjistěte, jak sdílet soubory cookie pro ověřování mezi ASP.NET 4.x a ASP.NET Core aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2019
uid: security/cookie-sharing
ms.openlocfilehash: b2f906ac97fe79b2a66a5ab709bcbcb03ab8cc39
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223913"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="1d48c-103">Sdílet soubory cookie pro ověřování mezi aplikací v ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1d48c-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="1d48c-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d48c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1d48c-105">Websites se často skládají z jednotlivých webových aplikací společně fungují.</span><span class="sxs-lookup"><span data-stu-id="1d48c-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="1d48c-106">Pokud chcete poskytovat jednotné přihlašování (SSO), musíte webové aplikace v rámci lokality sdílet soubory cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="1d48c-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="1d48c-107">Pro podporu tohoto scénáře, zásobník ochrany dat umožňuje sdílení Katana ověřování souborů cookie a lístků pro ověřování souborů cookie pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1d48c-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="1d48c-108">V následující příklady:</span><span class="sxs-lookup"><span data-stu-id="1d48c-108">In the examples that follow:</span></span>

* <span data-ttu-id="1d48c-109">Název souboru cookie ověřování je nastavena na hodnotu běžné `.AspNet.SharedCookie`.</span><span class="sxs-lookup"><span data-stu-id="1d48c-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="1d48c-110">`AuthenticationType` Je nastavena na `Identity.Application` buď explicitně nebo implicitně.</span><span class="sxs-lookup"><span data-stu-id="1d48c-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="1d48c-111">Běžný název aplikace se používá k povolení systém ochrany dat ke sdílení klíče ochrany dat (`SharedCookieApp`).</span><span class="sxs-lookup"><span data-stu-id="1d48c-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="1d48c-112">`Identity.Application` slouží jako schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="1d48c-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="1d48c-113">Jakékoli schéma se používá, musí být používány konzistentně *v různých i* sdílený soubor cookie aplikace jako výchozí schéma nebo explicitním nastavením.</span><span class="sxs-lookup"><span data-stu-id="1d48c-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="1d48c-114">Schéma se používá při šifrování a dešifrování souborů cookie, takže konzistentní schéma musí využívat napříč aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="1d48c-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="1d48c-115">Společný [klíč ochrany dat](xref:security/data-protection/implementation/key-management) umístění úložiště se používá.</span><span class="sxs-lookup"><span data-stu-id="1d48c-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="1d48c-116">V aplikacích ASP.NET Core <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> slouží k nastavení umístění úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="1d48c-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="1d48c-117">V aplikacích rozhraní .NET Framework, Middleware ověřování souborů Cookie používá provádění <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span><span class="sxs-lookup"><span data-stu-id="1d48c-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="1d48c-118">`DataProtectionProvider` poskytuje služby ochrany dat pro šifrování a dešifrování dat datové části souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="1d48c-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="1d48c-119">`DataProtectionProvider` Instance je izolovaná od systém ochrany dat používaný v ostatních částech aplikace.</span><span class="sxs-lookup"><span data-stu-id="1d48c-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="1d48c-120">[DataProtectionProvider.Create (System.IO.DirectoryInfo, akce\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) přijímá <xref:System.IO.DirectoryInfo> a zadejte umístění pro ukládání klíčů ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="1d48c-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="1d48c-121">`DataProtectionProvider` vyžaduje [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="1d48c-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="1d48c-122">V aplikacích ASP.NET Core 2.x, odkazovat [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1d48c-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="1d48c-123">V aplikacích rozhraní .NET Framework, přidejte odkaz na balíček pro [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="1d48c-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="1d48c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Nastaví běžný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="1d48c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="1d48c-125">Sdílet soubory cookie pro ověřování mezi aplikací ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d48c-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="1d48c-126">Při použití technologie ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="1d48c-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1d48c-127">Data ochrany klíčů a název aplikace musí být sdílena mezi aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="1d48c-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="1d48c-128">Společné umístění úložiště klíčů je k dispozici na <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> metoda v následujících příkladech.</span><span class="sxs-lookup"><span data-stu-id="1d48c-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="1d48c-129">Použití <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> konfigurace běžný název sdílené aplikace (`SharedCookieApp` v následujících příkladech).</span><span class="sxs-lookup"><span data-stu-id="1d48c-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="1d48c-130">Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="1d48c-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="1d48c-131">Použití <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metodu rozšíření k nastavení služby ochrany dat pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="1d48c-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="1d48c-132">V následujícím příkladu je typ ověřování nastavený na `Identity.Application` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1d48c-132">In the following example, the authentication type is set to `Identity.Application` by default.</span></span>

<span data-ttu-id="1d48c-133">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1d48c-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="1d48c-134">Při používání souborů cookie bez ASP.NET Core Identity přímo, konfigurovat ochranu dat a ověřování v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1d48c-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1d48c-135">V následujícím příkladu je typ ověřování nastavený na `Identity.Application`:</span><span class="sxs-lookup"><span data-stu-id="1d48c-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

<span data-ttu-id="1d48c-136">Při hostování aplikací, které sdílení souborů cookie mezi subdomény, zadejte běžné doménu v [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1d48c-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="1d48c-137">Ke sdílení souborů cookie mezi aplikacemi na `contoso.com`, jako například `first_subdomain.contoso.com` a `second_subdomain.contoso.com`, zadejte `Cookie.Domain` jako `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="1d48c-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="1d48c-138">Šifrování klíče ochrany dat v klidovém stavu</span><span class="sxs-lookup"><span data-stu-id="1d48c-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="1d48c-139">Pro nasazení v produkčním prostředí, nakonfigurovat `DataProtectionProvider` šifrování klíčů v klidovém stavu pomocí rozhraní DPAPI nebo certifikátu x 509.</span><span class="sxs-lookup"><span data-stu-id="1d48c-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="1d48c-140">Další informace naleznete v tématu <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="1d48c-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="1d48c-141">V následujícím příkladu je poskytnuta kryptografický otisk certifikátu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span><span class="sxs-lookup"><span data-stu-id="1d48c-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="1d48c-142">Sdílet soubory cookie pro ověřování mezi ASP.NET 4.x a aplikací ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d48c-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="1d48c-143">Aplikace ASP.NET 4.x, používající Middleware ověřování souborů Cookie Katana dá Generovat soubory cookie pro ověřování, které jsou kompatibilní s Middlewarem ověřování ASP.NET Core souboru Cookie.</span><span class="sxs-lookup"><span data-stu-id="1d48c-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="1d48c-144">Díky tomu upgradem jednotlivých aplikací velkých webů v několika krocích zároveň hladký jednotné přihlašování v lokalitě.</span><span class="sxs-lookup"><span data-stu-id="1d48c-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="1d48c-145">Pokud aplikace používá Katana Middleware ověřování souborů Cookie, volá `UseCookieAuthentication` v projektu *Startup.Auth.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="1d48c-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="1d48c-146">Projektech ASP.NET 4.x webové aplikace vytvořené pomocí sady Visual Studio 2013 a později použít Katana Middleware ověřování souborů Cookie ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1d48c-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="1d48c-147">I když `UseCookieAuthentication` je zastaralý a pro aplikace ASP.NET Core, volání `UseCookieAuthentication` v ASP.NET 4.x aplikaci, která používá Katana Middleware ověřování souborů Cookie je platný.</span><span class="sxs-lookup"><span data-stu-id="1d48c-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="1d48c-148">Aplikace ASP.NET 4.x musí cílit na rozhraní .NET Framework 4.5.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="1d48c-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="1d48c-149">V opačném případě potřebné balíčky NuGet nepodaří nainstalovat.</span><span class="sxs-lookup"><span data-stu-id="1d48c-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="1d48c-150">Sdílet soubory cookie pro ověřování mezi aplikace ASP.NET 4.x a aplikace ASP.NET Core, konfiguraci aplikace ASP.NET Core, jak je uvedeno v [sdílet soubory cookie pro ověřování mezi aplikací ASP.NET Core](#share-authentication-cookies-among-aspnet-core-apps) části a pak nakonfigurujte aplikaci ASP.NET 4.x jako následuje.</span><span class="sxs-lookup"><span data-stu-id="1d48c-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="1d48c-151">Potvrďte, že balíčky aplikace se aktualizují na nejnovější verze.</span><span class="sxs-lookup"><span data-stu-id="1d48c-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="1d48c-152">Nainstalujte [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) balíček do jednotlivých aplikací ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="1d48c-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="1d48c-153">Vyhledejte a upravte volání `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="1d48c-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="1d48c-154">Změňte název souboru cookie na odpovídat názvu, používat Middleware ověřování souborů Cookie základní technologie ASP.NET (`.AspNet.SharedCookie` v příkladu).</span><span class="sxs-lookup"><span data-stu-id="1d48c-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="1d48c-155">V následujícím příkladu je typ ověřování nastavený na `Identity.Application`.</span><span class="sxs-lookup"><span data-stu-id="1d48c-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="1d48c-156">Zadat instanci `DataProtectionProvider` inicializovány na společné umístění dat ochrany úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="1d48c-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="1d48c-157">Potvrďte, že je název aplikace nastavený na běžný název aplikaci používat všechny aplikace, které sdílejí soubory cookie pro ověřování (`SharedCookieApp` v příkladu).</span><span class="sxs-lookup"><span data-stu-id="1d48c-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="1d48c-158">Pokud není nastavení `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` a `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, nastavte <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> k deklaraci identity, která odlišuje jedinečných uživatelů.</span><span class="sxs-lookup"><span data-stu-id="1d48c-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="1d48c-159">*App_Start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="1d48c-159">*App_Start/Startup.Auth.cs*:</span></span>

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
            DataProtectionProvider.Create({PATH TO COMMON KEY RING FOLDER},
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

<span data-ttu-id="1d48c-160">Při generování identitu uživatele, typ ověřování (`Identity.Application`) musí odpovídat typ definovaný v `AuthenticationType` sadu s `UseCookieAuthentication` v *App_Start/Startup.Auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="1d48c-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="1d48c-161">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="1d48c-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="1d48c-162">Použít běžné uživatelské databázi</span><span class="sxs-lookup"><span data-stu-id="1d48c-162">Use a common user database</span></span>

<span data-ttu-id="1d48c-163">Když aplikace použít stejnou identitu schématu (stejnou verzi Identity), potvrďte, že systém Identity pro každé aplikaci, která ukazuje na stejné uživatele databáze.</span><span class="sxs-lookup"><span data-stu-id="1d48c-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="1d48c-164">V opačném případě systém identit vytvoří chyby za běhu při pokusí se porovnat informace v souboru cookie pro ověřování proti informací ve své databázi.</span><span class="sxs-lookup"><span data-stu-id="1d48c-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="1d48c-165">Pokud schéma Identity se liší mezi aplikacemi, obvykle vzhledem k tomu aplikace používáte různé verze Identity založené na nejnovější verzi Identity společnou databázi pro sdílení obsahu není možné bez přemapování a přidání sloupců ve schématech Identity jinou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1d48c-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="1d48c-166">Často je efektivnější upgrade dalších aplikací používat nejnovější verzi Identity tak, aby aplikace může sdílet společnou databázi.</span><span class="sxs-lookup"><span data-stu-id="1d48c-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d48c-167">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1d48c-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
