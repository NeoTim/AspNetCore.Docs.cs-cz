---
title: Sdílení souborů cookie ověřování mezi ASP.NET aplikacemi
author: rick-anderson
description: Naučte se sdílet soubory cookie ověřování mezi ASP.NET 4. x a ASP.NET Core aplikacemi.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/cookie-sharing
ms.openlocfilehash: 9b5bee9fb588ef04efd50aa4a5afc3e53da1b123
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384763"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Sdílení souborů cookie ověřování mezi ASP.NET aplikacemi

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)

Weby se často skládají z jednotlivých vzájemně pracujících webových aplikací. Aby bylo možné zajistit jednotné přihlašování (SSO), musí webové aplikace v rámci lokality sdílet soubory cookie ověřování. Pro podporu tohoto scénáře umožňuje zásobník ochrany dat sdílení Katana souborů cookie a ověřovacích lístků pro ASP.NET Core souborů cookie.

V následujících příkladech:

* Název souboru cookie ověřování je nastaven na společnou hodnotu `.AspNet.SharedCookie`.
* `AuthenticationType` Je`Identity.Application` nastaven buď explicitně, nebo jako výchozí.
* Běžný název aplikace se používá k tomu, aby systém ochrany dat mohl sdílet klíče ochrany dat (`SharedCookieApp`).
* `Identity.Application`slouží jako schéma ověřování. Bez ohledu na to, jaké schéma se používá, se musí používat konzistentně v rámci aplikace sdílené soubory cookie *a napříč* nimi, a to buď jako výchozí schéma, nebo explicitně nastavením. Schéma se používá při šifrování a dešifrování souborů cookie, takže v aplikacích je nutné použít konzistentní schéma.
* Používá se společné umístění úložiště [klíčů pro ochranu dat](xref:security/data-protection/implementation/key-management) .
  * V ASP.NET Core aplikace <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> se používá k nastavení umístění úložiště klíčů.
  * V .NET Frameworkch aplikacích používá middleware pro <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>ověřování souborů cookie implementaci. `DataProtectionProvider`poskytuje služby ochrany dat pro šifrování a dešifrování dat datové části ověřovacího souboru cookie. `DataProtectionProvider` Instance je izolovaná od systému ochrany dat, který používají jiné části aplikace. [DataProtectionProvider. Create (System. IO. DirectoryInfo, Action\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) přijme a <xref:System.IO.DirectoryInfo> určí umístění pro úložiště klíčů ochrany dat.
* `DataProtectionProvider`vyžaduje balíček NuGet [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * V aplikacích ASP.NET Core 2. x odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).
  * V .NET Framework aplikace přidejte odkaz na balíček do [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Nastaví běžný název aplikace.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>Sdílení souborů cookie ověřování pomocí ASP.NET Core identity

Při použití ASP.NET Core identity:

* Klíče ochrany dat a název aplikace se musí sdílet mezi aplikacemi. K <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> metodě v následujících příkladech je poskytováno společné umístění úložiště klíčů. Použijte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> ke konfiguraci společného názvu sdílené aplikace (`SharedCookieApp` v následujících příkladech). Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.
* Použijte metodu <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> rozšíření k nastavení služby ochrany dat pro soubory cookie.
* Výchozí typ ověřování je `Identity.Application`.

V `Startup.ConfigureServices`nástroji:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>Sdílení souborů cookie ověřování bez ASP.NET Core identity

Při přímém použití souborů cookie bez ASP.NET Core identity nakonfigurujte ochranu a ověřování dat `Startup.ConfigureServices`v. V následujícím příkladu je typ ověřování nastaven na `Identity.Application`:

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

## <a name="share-cookies-across-different-base-paths"></a>Sdílení souborů cookie napříč různými základními cestami

Ověřovací soubor cookie používá jako výchozí soubor cookie. [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Pokud se soubor cookie aplikace musí sdílet mezi různými základními cestami `Path` , musí se přepsat:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>Sdílení souborů cookie napříč subdoménami

Při hostování aplikací sdílejících soubory cookie napříč subdoménami zadejte společnou doménu ve vlastnosti [cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) . Chcete-li sdílet soubory cookie `contoso.com`napříč aplikacemi, `first_subdomain.contoso.com` například `second_subdomain.contoso.com`a, zadejte `Cookie.Domain` jako `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Šifrovat klíče ochrany dat v klidovém umístění

V případě nasazení v produkčním prostředí `DataProtectionProvider` nakonfigurujte šifrování klíčů v klidovém formátu pomocí DPAPI nebo certifikátu x509. Další informace naleznete v tématu <xref:security/data-protection/implementation/key-encryption-at-rest>. V následujícím příkladu je k <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>dispozici kryptografický otisk certifikátu:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Sdílení souborů cookie pro ověřování mezi ASP.NET 4. x a ASP.NET Core aplikacemi

Aplikace ASP.NET 4. x, které používají middleware ověřování souborů cookie Katana, se dají nakonfigurovat tak, aby vygenerovaly soubory cookie ověřování, které jsou kompatibilní s middlewarem ověřování souborů cookie ASP.NET Core To umožňuje v několika krocích upgradovat jednotlivé aplikace velké lokality a zajistit tak hladké možnosti jednotného přihlašování napříč lokalitami.

Když aplikace používá middleware ověřování Katana souborů cookie, volá `UseCookieAuthentication` se v souboru *Startup.auth.cs* projektu. Projekty webové aplikace ASP.NET 4. x vytvořené pomocí Visual Studio 2013 a později ve výchozím nastavení používají middleware ověřování souborů cookie Katana. I `UseCookieAuthentication` když je zastaralá a podporovaná pro `UseCookieAuthentication` ASP.NET Core aplikace, volání v aplikaci ASP.NET 4. x, která používá middleware ověřování souborů cookie Katana, je platná.

Aplikace ASP.NET 4. x musí cílit na .NET Framework 4.5.1 nebo novější. V opačném případě se instalace nezbytných balíčků NuGet nezdařila.

Pokud chcete soubory cookie pro ověřování sdílet mezi aplikací ASP.NET 4. x a aplikací ASP.NET Core, nakonfigurujte aplikaci ASP.NET Core tak, jak je uvedeno v části [sdílení ověřovacích souborů cookie mezi ASP.NET Coremi aplikacemi](#share-authentication-cookies-with-aspnet-core-identity) , a pak nakonfigurujte aplikaci ASP.NET 4. x následujícím způsobem.

Zkontrolujte, jestli jsou balíčky aplikace aktualizované na nejnovější verze. Do každé aplikace ASP.NET 4. x nainstalujte balíček [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) .

Vyhledejte a upravte volání na `UseCookieAuthentication`:

* Změňte název souboru cookie tak, aby odpovídal názvu, který používá middleware pro ověřování`.AspNet.SharedCookie` souborů cookie ASP.NET Core (v příkladu).
* V následujícím příkladu je typ ověřování nastaven na `Identity.Application`.
* Zadejte instanci `DataProtectionProvider` inicializovaného do umístění úložiště klíčů pro Common data Protection.
* Ověřte, že je název aplikace nastavený na společný název aplikace používaný všemi aplikacemi, které sdílejí soubory cookie ověřování (`SharedCookieApp` v tomto příkladu).

Pokud není `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` nastavené `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`a, <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> nastavte na deklaraci identity, která rozlišuje jedinečné uživatele.

*App_start/Startup. auth. cs*:

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

Při generování identity uživatele`Identity.Application`musí typ ověřování () odpovídat typu `AuthenticationType` definovanému v sadě s použitím `UseCookieAuthentication` v *app_start/Startup. auth. cs*.

*Models/IdentityModels.cs*:

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

## <a name="use-a-common-user-database"></a>Použití společné uživatelské databáze

Když aplikace používají stejné schéma identity (stejnou verzi identity), ujistěte se, že systém identit pro každou aplikaci je odkazoval na stejnou uživatelskou databázi. V opačném případě systém identit vytvoří selhání za běhu při pokusu o shodu s informacemi v ověřovacím souboru cookie proti informacím v příslušné databázi.

Když se schéma identity mezi aplikacemi liší, obvykle protože aplikace používají různé verze identity, sdílení společné databáze na základě nejnovější verze identity není možné bez nutnosti přemapování a přidání sloupců v schématech identit jiných aplikací. Je často efektivnější upgradovat ostatní aplikace tak, aby používaly nejnovější verzi identity, aby bylo možné sdílet aplikace do společné databáze.

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
