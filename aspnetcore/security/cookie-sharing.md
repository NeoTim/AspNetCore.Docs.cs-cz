---
title: Sdílení souborů cookie mezi aplikacemi s technologií ASP.NET a ASP.NET Core
author: rick-anderson
description: Zjistěte, jak sdílet soubory cookie pro ověřování mezi ASP.NET 4.x a ASP.NET Core aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7a049ed8787808e228859afc051b8697a6261c21
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068307"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a>Sdílení souborů cookie mezi aplikacemi s technologií ASP.NET a ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)

Websites se často skládají z jednotlivých webových aplikací společně fungují. Pokud chcete poskytovat jednotné přihlašování (SSO), musíte webové aplikace v rámci lokality sdílet soubory cookie pro ověřování. Pro podporu tohoto scénáře, zásobník ochrany dat umožňuje sdílení Katana ověřování souborů cookie a lístků pro ověřování souborů cookie pomocí ASP.NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:index#how-to-download-a-sample))

Ukázka ilustruje soubor cookie pro sdílení obsahu napříč tři aplikace, které používala ověřování souborů cookie:

* Aplikace ASP.NET Core 2.0 Razor Pages bez použití [ASP.NET Core Identity](xref:security/authentication/identity)
* Aplikace MVC ASP.NET Core 2.0 s ASP.NET Core Identity
* Aplikace MVC rozhraní ASP.NET Framework 4.6.1 s ASP.NET Identity

V následující příklady:

* Název souboru cookie ověřování je nastavena na hodnotu běžné `.AspNet.SharedCookie`.
* `AuthenticationType` Je nastavena na `Identity.Application` buď explicitně nebo implicitně.
* Běžný název aplikace se používá k povolení systém ochrany dat ke sdílení klíče ochrany dat (`SharedCookieApp`).
* `Identity.Application` slouží jako schéma ověřování. Jakékoli schéma se používá, musí být používány konzistentně *v různých i* sdílený soubor cookie aplikace jako výchozí schéma nebo explicitním nastavením. Schéma se používá při šifrování a dešifrování souborů cookie, takže konzistentní schéma musí využívat napříč aplikacemi.
* Společný [klíč ochrany dat](xref:security/data-protection/implementation/key-management) umístění úložiště se používá. Ukázková aplikace používá složka s názvem *Správce klíčů* v kořenovém adresáři řešení pro uložení dat ochrany klíčů.
* V aplikacích ASP.NET Core [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) slouží k nastavení umístění úložiště klíčů. [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) slouží ke konfiguraci běžný název sdílené aplikace.
* V aplikaci rozhraní .NET Framework, middleware ověřování souborů cookie používá provádění [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider). `DataProtectionProvider` poskytuje služby ochrany dat pro šifrování a dešifrování dat datové části souboru cookie ověřování. `DataProtectionProvider` Instance je izolovaná od systém ochrany dat používaný v ostatních částech aplikace.
  * [DataProtectionProvider.Create (System.IO.DirectoryInfo, akce\<IDataProtectionBuilder >)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__) přijímá [DirectoryInfo](/dotnet/api/system.io.directoryinfo) a zadejte umístění pro ukládání klíčů ochrany dat. Ukázková aplikace poskytuje cestu *Správce klíčů* složku `DirectoryInfo`. [DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) nastaví běžný název aplikace.
  * [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) vyžaduje [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) balíček NuGet. Chcete-li získat tento balíček pro ASP.NET Core 2.1 a novější aplikace, odkazovat [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Při cílení na rozhraní .NET Framework, přidejte odkaz na balíček pro `Microsoft.AspNetCore.DataProtection.Extensions`.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Sdílet soubory cookie pro ověřování mezi aplikací ASP.NET Core

Při použití technologie ASP.NET Core Identity:

::: moniker range=">= aspnetcore-2.0"

V `ConfigureServices` metody, použijte [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) metodu rozšíření k nastavení služby ochrany dat pro soubory cookie.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

Data ochrany klíčů a název aplikace musí být sdílena mezi aplikacemi. V ukázkových aplikací `GetKeyRingDirInfo` vrátí společné umístění úložiště klíčů pro [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) metody. Použití [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) konfigurace běžný název sdílené aplikace (`SharedCookieApp` v ukázce). Další informace najdete v tématu [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview).

Při hostování aplikací, které sdílení souborů cookie mezi subdomény, zadejte běžné doménu v [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) vlastnost. Ke sdílení souborů cookie mezi aplikacemi na `contoso.com`, jako například `first_subdomain.contoso.com` a `second_subdomain.contoso.com`, zadejte `Cookie.Domain` jako `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

Najdete v článku *CookieAuthWithIdentity.Core* projekt [ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

V `Configure` metody, použijte [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) nastavení:

* Službu ochrany dat pro soubory cookie.
* `AuthenticationScheme` Tak, aby odpovídaly ASP.NET 4.x.

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

::: moniker-end

Při použití souborů cookie přímo:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

Data ochrany klíčů a název aplikace musí být sdílena mezi aplikacemi. V ukázkových aplikací `GetKeyRingDirInfo` vrátí společné umístění úložiště klíčů pro [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) metody. Použití [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) konfigurace běžný název sdílené aplikace (`SharedCookieApp` v ukázce). Další informace najdete v tématu [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview).

Při hostování aplikací, které sdílení souborů cookie mezi subdomény, zadejte běžné doménu v [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) vlastnost. Ke sdílení souborů cookie mezi aplikacemi na `contoso.com`, jako například `first_subdomain.contoso.com` a `second_subdomain.contoso.com`, zadejte `Cookie.Domain` jako `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

Najdete v článku *CookieAuth.Core* projekt [ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
});
```

::: moniker-end

## <a name="encrypting-data-protection-keys-at-rest"></a>Šifrování klíče ochrany dat v klidovém stavu

Pro nasazení v produkčním prostředí, nakonfigurovat `DataProtectionProvider` šifrování klíčů v klidovém stavu pomocí rozhraní DPAPI nebo certifikátu x 509. Zobrazit [klíč šifrování v Rest](xref:security/data-protection/implementation/key-encryption-at-rest) Další informace.

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("thumbprint");
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

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

::: moniker-end

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Sdílení souborů cookie ověřování mezi ASP.NET 4.x a aplikací ASP.NET Core

Aplikace ASP.NET 4.x, která používá Katana middleware ověřování souborů cookie můžete nakonfigurovat, aby vygenerovalo soubory cookie pro ověřování, které jsou kompatibilní s ASP.NET Core middleware ověřování souborů cookie. Díky tomu upgradem jednotlivých aplikací velkých lokality piecemeal zároveň hladký jednotné přihlašování v lokalitě.

Pokud aplikace používá Katana middleware ověřování souborů cookie, volá `UseCookieAuthentication` v projektu *Startup.Auth.cs* souboru. Projektech ASP.NET 4.x webové aplikace vytvořené pomocí sady Visual Studio 2013 a později použít Katana middleware ověřování souborů cookie ve výchozím nastavení. I když `UseCookieAuthentication` je zastaralý a pro aplikace ASP.NET Core, volání `UseCookieAuthentication` v aplikaci ASP.NET 4.x, který používá Katana middleware ověřování souborů cookie je neplatný.

Aplikace ASP.NET 4.x musí cílit na rozhraní .NET Framework 4.5.1 nebo novější. V opačném případě potřebné balíčky NuGet nepodaří nainstalovat.

Sdílet soubory cookie pro ověřování mezi aplikace ASP.NET 4.x a aplikace ASP.NET Core, konfiguraci aplikace ASP.NET Core, jak je uvedeno výše a potom konfiguraci aplikace ASP.NET 4.x pomocí následujících kroků:

1. Nainstalujte balíček [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) do jednotlivých aplikací ASP.NET 4.x.

2. V *Startup.Auth.cs*, vyhledejte volání `UseCookieAuthentication` a upravte následujícím způsobem. Změňte název souboru cookie tak, aby odpovídaly název používaný aplikací ASP.NET Core middleware ověřování souborů cookie. Zadat instanci `DataProtectionProvider` inicializovány na společné umístění dat ochrany úložiště klíčů. Ujistěte se, že je název aplikace nastavený na běžný název aplikaci používat všechny aplikace, které sdílení souborů cookie, `SharedCookieApp` v ukázkové aplikaci.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

Najdete v článku *CookieAuthWithIdentity.NETFramework* projekt [ukázkový kód](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([stažení](xref:index#how-to-download-a-sample)).

Při generování identitu uživatele, typ ověřování, který musí odpovídat typ definovaný v `AuthenticationType` sadu s `UseCookieAuthentication`.

*Models/IdentityModels.cs*:

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a>Použít běžné uživatelské databázi

Potvrďte, že systém identity pro každé aplikaci, která ukazuje na stejné uživatele databáze. V opačném případě systém identit vytvoří chyby za běhu při pokusí se porovnat informace v souboru cookie pro ověřování proti informací ve své databázi.

## <a name="additional-resources"></a>Další zdroje

<xref:host-and-deploy/web-farm>
