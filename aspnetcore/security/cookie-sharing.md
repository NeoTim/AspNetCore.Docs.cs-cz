---
title: Sdílení souborů cookie mezi aplikacemi s technologií ASP.NET a ASP.NET Core
author: rick-anderson
description: Zjistěte, jak sdílet soubory cookie pro ověřování mezi ASP.NET 4.x a ASP.NET Core aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2017
uid: security/cookie-sharing
ms.openlocfilehash: ed3496db3f7a63a704f0e57faef6b2f6c085a8bd
ms.sourcegitcommit: b4c7b1a4c48dec0865f27874275c73da1f75e918
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228595"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a><span data-ttu-id="fe7cf-103">Sdílení souborů cookie mezi aplikacemi s technologií ASP.NET a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe7cf-103">Share cookies among apps with ASP.NET and ASP.NET Core</span></span>

<span data-ttu-id="fe7cf-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe7cf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fe7cf-105">Websites se často skládají z jednotlivých webových aplikací společně fungují.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="fe7cf-106">Pokud chcete poskytovat jednotné přihlašování (SSO), musíte webové aplikace v rámci lokality sdílet soubory cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="fe7cf-107">Pro podporu tohoto scénáře, zásobník ochrany dat umožňuje sdílení Katana ověřování souborů cookie a lístků pro ověřování souborů cookie pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="fe7cf-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fe7cf-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fe7cf-109">Ukázka ilustruje soubor cookie pro sdílení obsahu napříč tři aplikace, které používala ověřování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-109">The sample illustrates cookie sharing across three apps that use cookie authentication:</span></span>

* <span data-ttu-id="fe7cf-110">Aplikace ASP.NET Core 2.0 Razor Pages bez použití [ASP.NET Core Identity](xref:security/authentication/identity)</span><span class="sxs-lookup"><span data-stu-id="fe7cf-110">ASP.NET Core 2.0 Razor Pages app without using [ASP.NET Core Identity](xref:security/authentication/identity)</span></span>
* <span data-ttu-id="fe7cf-111">Aplikace MVC ASP.NET Core 2.0 s ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="fe7cf-111">ASP.NET Core 2.0 MVC app with ASP.NET Core Identity</span></span>
* <span data-ttu-id="fe7cf-112">Aplikace MVC rozhraní ASP.NET Framework 4.6.1 s ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="fe7cf-112">ASP.NET Framework 4.6.1 MVC app with ASP.NET Identity</span></span>

<span data-ttu-id="fe7cf-113">V následující příklady:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-113">In the examples that follow:</span></span>

* <span data-ttu-id="fe7cf-114">Název souboru cookie ověřování je nastavena na hodnotu běžné `.AspNet.SharedCookie`.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-114">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="fe7cf-115">`AuthenticationType` Je nastavena na `Identity.Application` buď explicitně nebo implicitně.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-115">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="fe7cf-116">Běžný název aplikace se používá k povolení systém ochrany dat ke sdílení klíče ochrany dat (`SharedCookieApp`).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-116">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="fe7cf-117">`Identity.Application` slouží jako schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-117">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="fe7cf-118">Jakékoli schéma se používá, musí být používány konzistentně *v různých i* sdílený soubor cookie aplikace jako výchozí schéma nebo explicitním nastavením.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-118">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="fe7cf-119">Schéma se používá při šifrování a dešifrování souborů cookie, takže konzistentní schéma musí využívat napříč aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-119">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="fe7cf-120">Společný [klíč ochrany dat](xref:security/data-protection/implementation/key-management) umístění úložiště se používá.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-120">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span> <span data-ttu-id="fe7cf-121">Ukázková aplikace používá složka s názvem *Správce klíčů* v kořenovém adresáři řešení pro uložení dat ochrany klíčů.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-121">The sample app uses a folder named *KeyRing* at the root of the solution to hold the data protection keys.</span></span>
* <span data-ttu-id="fe7cf-122">V aplikacích ASP.NET Core [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) slouží k nastavení umístění úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-122">In the ASP.NET Core apps, [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) is used to set the key storage location.</span></span> <span data-ttu-id="fe7cf-123">[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) slouží ke konfiguraci běžný název sdílené aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-123">[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) is used to configure a common shared app name.</span></span>
* <span data-ttu-id="fe7cf-124">V aplikaci rozhraní .NET Framework, middleware ověřování souborů cookie používá provádění [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-124">In the .NET Framework app, the cookie authentication middleware uses an implementation of [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider).</span></span> <span data-ttu-id="fe7cf-125">`DataProtectionProvider` poskytuje služby ochrany dat pro šifrování a dešifrování dat datové části souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-125">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="fe7cf-126">`DataProtectionProvider` Instance je izolovaná od systém ochrany dat používaný v ostatních částech aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-126">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span>
  * <span data-ttu-id="fe7cf-127">[DataProtectionProvider.Create (System.IO.DirectoryInfo, akce\<IDataProtectionBuilder >)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__) přijímá [DirectoryInfo](/dotnet/api/system.io.directoryinfo) a zadejte umístění pro ukládání klíčů ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-127">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__) accepts a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) to specify the location for data protection key storage.</span></span> <span data-ttu-id="fe7cf-128">Ukázková aplikace poskytuje cestu *Správce klíčů* složku `DirectoryInfo`.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-128">The sample app provides the path of the *KeyRing* folder to `DirectoryInfo`.</span></span> <span data-ttu-id="fe7cf-129">[DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) nastaví běžný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-129">[DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) sets the common app name.</span></span>
  * <span data-ttu-id="fe7cf-130">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) vyžaduje [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-130">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package.</span></span> <span data-ttu-id="fe7cf-131">Chcete-li získat tento balíček pro ASP.NET Core 2.1 a novější aplikace, odkazovat [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-131">To obtain this package for ASP.NET Core 2.1 and later apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="fe7cf-132">Při cílení na rozhraní .NET Framework, přidejte odkaz na balíček pro `Microsoft.AspNetCore.DataProtection.Extensions`.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-132">When targeting the .NET Framework, add a package reference to `Microsoft.AspNetCore.DataProtection.Extensions`.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="fe7cf-133">Sdílet soubory cookie pro ověřování mezi aplikací ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe7cf-133">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="fe7cf-134">Při použití technologie ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-134">When using ASP.NET Core Identity:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="fe7cf-135">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="fe7cf-135">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="fe7cf-136">V `ConfigureServices` metody, použijte [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) metodu rozšíření k nastavení služby ochrany dat pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-136">In the `ConfigureServices` method, use the [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) extension method to set up the data protection service for cookies.</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

<span data-ttu-id="fe7cf-137">Data ochrany klíčů a název aplikace musí být sdílena mezi aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-137">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="fe7cf-138">V ukázkových aplikací `GetKeyRingDirInfo` vrátí společné umístění úložiště klíčů pro [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) metody.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-138">In the sample apps, `GetKeyRingDirInfo` returns the common key storage location to the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) method.</span></span> <span data-ttu-id="fe7cf-139">Použití [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) konfigurace běžný název sdílené aplikace (`SharedCookieApp` v ukázce).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-139">Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) to configure a common shared app name (`SharedCookieApp` in the sample).</span></span> <span data-ttu-id="fe7cf-140">Další informace najdete v tématu [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-140">For more information, see [Configuring Data Protection](xref:security/data-protection/configuration/overview).</span></span>

<span data-ttu-id="fe7cf-141">Při hostování aplikací, které sdílení souborů cookie mezi subdomény, zadejte běžné doménu v [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) property.</span></span> <span data-ttu-id="fe7cf-142">Ke sdílení souborů cookie mezi aplikacemi na `contoso.com`, jako například `first_subdomain.contoso.com` a `second_subdomain.contoso.com`, zadejte `Cookie.Domain` jako `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

<span data-ttu-id="fe7cf-143">Najdete v článku *CookieAuthWithIdentity.Core* projekt [ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:tutorials/index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-143">See the *CookieAuthWithIdentity.Core* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:tutorials/index#how-to-download-a-sample)).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="fe7cf-144">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="fe7cf-144">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="fe7cf-145">V `Configure` metody, použijte [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) nastavení:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-145">In the `Configure` method, use the [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) to set up:</span></span>

* <span data-ttu-id="fe7cf-146">Službu ochrany dat pro soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-146">The data protection service for cookies.</span></span>
* <span data-ttu-id="fe7cf-147">`AuthenticationScheme` Tak, aby odpovídaly ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-147">The `AuthenticationScheme` to match ASP.NET 4.x.</span></span>

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
{
    options.Cookies.ApplicationCookie.AuthenticationScheme = 
        "ApplicationCookie";

    var protectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"));

    options.Cookies.ApplicationCookie.DataProtectionProvider = 
        protectionProvider;

    options.Cookies.ApplicationCookie.TicketDataFormat = 
        new TicketDataFormat(protectionProvider.CreateProtector(
            "Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", 
            "Cookies", 
            "v2"));
});
```

---

<span data-ttu-id="fe7cf-148">Při použití souborů cookie přímo:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-148">When using cookies directly:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="fe7cf-149">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="fe7cf-149">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

<span data-ttu-id="fe7cf-150">Data ochrany klíčů a název aplikace musí být sdílena mezi aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-150">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="fe7cf-151">V ukázkových aplikací `GetKeyRingDirInfo` vrátí společné umístění úložiště klíčů pro [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) metody.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-151">In the sample apps, `GetKeyRingDirInfo` returns the common key storage location to the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) method.</span></span> <span data-ttu-id="fe7cf-152">Použití [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) konfigurace běžný název sdílené aplikace (`SharedCookieApp` v ukázce).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-152">Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) to configure a common shared app name (`SharedCookieApp` in the sample).</span></span> <span data-ttu-id="fe7cf-153">Další informace najdete v tématu [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-153">For more information, see [Configuring Data Protection](xref:security/data-protection/configuration/overview).</span></span>

<span data-ttu-id="fe7cf-154">Při hostování aplikací, které sdílení souborů cookie mezi subdomény, zadejte běžné doménu v [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-154">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) property.</span></span> <span data-ttu-id="fe7cf-155">Ke sdílení souborů cookie mezi aplikacemi na `contoso.com`, jako například `first_subdomain.contoso.com` a `second_subdomain.contoso.com`, zadejte `Cookie.Domain` jako `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-155">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

<span data-ttu-id="fe7cf-156">Najdete v článku *CookieAuth.Core* projekt [ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:tutorials/index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-156">See the *CookieAuth.Core* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:tutorials/index#how-to-download-a-sample)).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="fe7cf-157">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="fe7cf-157">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
});
```

---

## <a name="encrypting-data-protection-keys-at-rest"></a><span data-ttu-id="fe7cf-158">Šifrování klíče ochrany dat v klidovém stavu</span><span class="sxs-lookup"><span data-stu-id="fe7cf-158">Encrypting data protection keys at rest</span></span>

<span data-ttu-id="fe7cf-159">Pro nasazení v produkčním prostředí, nakonfigurovat `DataProtectionProvider` šifrování klíčů v klidovém stavu pomocí rozhraní DPAPI nebo certifikátu x 509.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-159">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="fe7cf-160">Zobrazit [klíč šifrování v Rest](xref:security/data-protection/implementation/key-encryption-at-rest) Další informace.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-160">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="fe7cf-161">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="fe7cf-161">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("thumbprint");
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="fe7cf-162">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="fe7cf-162">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = DataProtectionProvider.Create(
        new DirectoryInfo(@"PATH_TO_KEY_RING"),
        configure =>
        {
            configure.ProtectKeysWithCertificate("thumbprint");
        })
});
```

---

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="fe7cf-163">Sdílení souborů cookie ověřování mezi ASP.NET 4.x a aplikací ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe7cf-163">Sharing authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="fe7cf-164">Aplikace ASP.NET 4.x, která používá Katana middleware ověřování souborů cookie můžete nakonfigurovat, aby vygenerovalo soubory cookie pro ověřování, které jsou kompatibilní s ASP.NET Core middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-164">ASP.NET 4.x apps which use Katana cookie authentication middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="fe7cf-165">Díky tomu upgradem jednotlivých aplikací velkých lokality piecemeal zároveň hladký jednotné přihlašování v lokalitě.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-165">This allows upgrading a large site's individual apps piecemeal while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="fe7cf-166">Pokud aplikace používá Katana middleware ověřování souborů cookie, volá `UseCookieAuthentication` v projektu *Startup.Auth.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-166">When an app uses Katana cookie authentication middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="fe7cf-167">Projektech ASP.NET 4.x webové aplikace vytvořené pomocí sady Visual Studio 2013 a později použít Katana middleware ověřování souborů cookie ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-167">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana cookie authentication middleware by default.</span></span> <span data-ttu-id="fe7cf-168">I když `UseCookieAuthentication` je zastaralý a pro aplikace ASP.NET Core, volání `UseCookieAuthentication` v aplikaci ASP.NET 4.x, který používá Katana middleware ověřování souborů cookie je neplatný.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-168">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana cookie authentication middleware is valid.</span></span>

<span data-ttu-id="fe7cf-169">Aplikace ASP.NET 4.x musí cílit na rozhraní .NET Framework 4.5.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-169">An ASP.NET 4.x app must target .NET Framework 4.5.1 or higher.</span></span> <span data-ttu-id="fe7cf-170">V opačném případě potřebné balíčky NuGet nepodaří nainstalovat.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-170">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="fe7cf-171">Sdílet soubory cookie pro ověřování mezi aplikace ASP.NET 4.x a aplikace ASP.NET Core, konfiguraci aplikace ASP.NET Core, jak je uvedeno výše a potom konfiguraci aplikace ASP.NET 4.x pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-171">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated above, then configure the ASP.NET 4.x app by following these steps:</span></span>

1. <span data-ttu-id="fe7cf-172">Nainstalujte balíček [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) do jednotlivých aplikací ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-172">Install the package [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) into each ASP.NET 4.x app.</span></span>

2. <span data-ttu-id="fe7cf-173">V *Startup.Auth.cs*, vyhledejte volání `UseCookieAuthentication` a upravte následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-173">In *Startup.Auth.cs*, locate the call to `UseCookieAuthentication` and modify it as follows.</span></span> <span data-ttu-id="fe7cf-174">Změňte název souboru cookie tak, aby odpovídaly název používaný aplikací ASP.NET Core middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-174">Change the cookie name to match the name used by the ASP.NET Core cookie authentication middleware.</span></span> <span data-ttu-id="fe7cf-175">Zadat instanci `DataProtectionProvider` inicializovány na společné umístění dat ochrany úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-175">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span> <span data-ttu-id="fe7cf-176">Ujistěte se, že je název aplikace nastavený na běžný název aplikaci používat všechny aplikace, které sdílení souborů cookie, `SharedCookieApp` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-176">Make sure that the app name is set to the common app name used by all apps that share cookies, `SharedCookieApp` in the sample app.</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

<span data-ttu-id="fe7cf-177">Najdete v článku *CookieAuthWithIdentity.NETFramework* projekt [ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:tutorials/index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fe7cf-177">See the *CookieAuthWithIdentity.NETFramework* project in the [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([how to download](xref:tutorials/index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fe7cf-178">Při generování identitu uživatele, typ ověřování, který musí odpovídat typ definovaný v `AuthenticationType` sadu s `UseCookieAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-178">When generating a user identity, the authentication type must match the type defined in `AuthenticationType` set with `UseCookieAuthentication`.</span></span>

<span data-ttu-id="fe7cf-179">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="fe7cf-179">*Models/IdentityModels.cs*:</span></span>

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a><span data-ttu-id="fe7cf-180">Použít běžné uživatelské databázi</span><span class="sxs-lookup"><span data-stu-id="fe7cf-180">Use a common user database</span></span>

<span data-ttu-id="fe7cf-181">Potvrďte, že systém identity pro každé aplikaci, která ukazuje na stejné uživatele databáze.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-181">Confirm that the identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="fe7cf-182">V opačném případě systém identit vytvoří chyby za běhu při pokusí se porovnat informace v souboru cookie pro ověřování proti informací ve své databázi.</span><span class="sxs-lookup"><span data-stu-id="fe7cf-182">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe7cf-183">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fe7cf-183">Additional resources</span></span>

<xref:host-and-deploy/web-farm>
