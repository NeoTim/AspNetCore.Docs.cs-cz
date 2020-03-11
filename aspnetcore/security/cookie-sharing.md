---
title: Sdílení souborů cookie ověřování mezi ASP.NET aplikacemi
author: rick-anderson
description: Naučte se sdílet soubory cookie ověřování mezi ASP.NET 4. x a ASP.NET Core aplikacemi.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7e29be22717f0b97fc115ac036cc54e333bed4e2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658170"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Sdílení souborů cookie ověřování mezi ASP.NET aplikacemi

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Weby se často skládají z jednotlivých vzájemně pracujících webových aplikací. Aby bylo možné zajistit jednotné přihlašování (SSO), musí webové aplikace v rámci lokality sdílet soubory cookie ověřování. Pro podporu tohoto scénáře umožňuje zásobník ochrany dat sdílení Katana souborů cookie a ověřovacích lístků pro ASP.NET Core souborů cookie.

V následujících příkladech:

* Název souboru cookie ověřování je nastaven na běžnou hodnotu `.AspNet.SharedCookie`.
* `AuthenticationType` je nastavené na `Identity.Application` buď explicitně, nebo jako výchozí.
* Běžný název aplikace se používá k tomu, aby systém ochrany dat mohl sdílet klíče ochrany dat (`SharedCookieApp`).
* jako schéma ověřování se používá `Identity.Application`. Bez ohledu na to, jaké schéma se používá, se musí používat konzistentně v rámci aplikace sdílené soubory cookie *a napříč* nimi, a to buď jako výchozí schéma, nebo explicitně nastavením. Schéma se používá při šifrování a dešifrování souborů cookie, takže v aplikacích je nutné použít konzistentní schéma.
* Používá se společné umístění úložiště [klíčů pro ochranu dat](xref:security/data-protection/implementation/key-management) .
  * V ASP.NET Corech aplikacích se k nastavení umístění úložiště klíčů používá <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>.
  * V .NET Framework aplikace používá middleware pro ověřování souborů cookie implementaci <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>. `DataProtectionProvider` poskytuje služby ochrany dat pro šifrování a dešifrování dat datové části ověřovacího souboru cookie. Instance `DataProtectionProvider` je izolovaná od systému ochrany dat, který používají jiné části aplikace. [DataProtectionProvider. Create (System. IO. DirectoryInfo, Action\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) přijímá <xref:System.IO.DirectoryInfo> k určení umístění pro úložiště klíčů ochrany dat.
* `DataProtectionProvider` vyžaduje balíček NuGet [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * V aplikacích ASP.NET Core 2. x odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).
  * V .NET Framework aplikace přidejte odkaz na balíček do [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> nastaví běžný název aplikace.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>Sdílení souborů cookie ověřování pomocí ASP.NET Core identity

Při použití ASP.NET Core identity:

* Klíče ochrany dat a název aplikace se musí sdílet mezi aplikacemi. V následujících příkladech je k dispozici společné umístění úložiště klíčů pro metodu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>. Pomocí <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> můžete nakonfigurovat společný název sdílené aplikace (`SharedCookieApp` v následujících příkladech). Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.
* Použijte metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> k nastavení služby ochrany dat pro soubory cookie.
* Výchozí typ ověřování je `Identity.Application`.

V `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>Sdílení souborů cookie ověřování bez ASP.NET Core identity

Při přímém použití souborů cookie bez ASP.NET Core identity nakonfigurujte ochranu a ověřování dat v `Startup.ConfigureServices`. V následujícím příkladu je typ ověřování nastaven na `Identity.Application`:

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

Ověřovací soubor cookie používá jako výchozí soubor cookie. [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Pokud se soubor cookie aplikace musí sdílet mezi různými základními cestami, `Path` musí přepsat:

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

Při hostování aplikací sdílejících soubory cookie napříč subdoménami zadejte společnou doménu ve vlastnosti [cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) . Chcete-li sdílet soubory cookie napříč aplikacemi na `contoso.com`, jako je například `first_subdomain.contoso.com` a `second_subdomain.contoso.com`, zadejte `Cookie.Domain` jako `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Šifrovat klíče ochrany dat v klidovém umístění

V případě nasazení v produkčním prostředí nakonfigurujte `DataProtectionProvider` pro šifrování klíčů v klidovém provozu pomocí DPAPI nebo certifikátu x509. Další informace naleznete v tématu <xref:security/data-protection/implementation/key-encryption-at-rest>. V následujícím příkladu je <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>k dispozici kryptografický otisk certifikátu:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Sdílení souborů cookie pro ověřování mezi ASP.NET 4. x a ASP.NET Core aplikacemi

Aplikace ASP.NET 4. x, které používají middleware ověřování souborů cookie Katana, se dají nakonfigurovat tak, aby vygenerovaly soubory cookie ověřování, které jsou kompatibilní s middlewarem ověřování souborů cookie ASP.NET Core To umožňuje v několika krocích upgradovat jednotlivé aplikace velké lokality a zajistit tak hladké možnosti jednotného přihlašování napříč lokalitami.

Když aplikace používá middleware ověřování Katana souborů cookie, volá `UseCookieAuthentication` v souboru *Startup.auth.cs* projektu. Projekty webové aplikace ASP.NET 4. x vytvořené pomocí Visual Studio 2013 a později ve výchozím nastavení používají middleware ověřování souborů cookie Katana. I když je `UseCookieAuthentication` zastaralá a Nepodporovaná pro ASP.NET Core aplikace, volání `UseCookieAuthentication` v aplikaci ASP.NET 4. x, která používá middleware ověřování souborů cookie Katana, je platné.

Aplikace ASP.NET 4. x musí cílit na .NET Framework 4.5.1 nebo novější. V opačném případě se instalace nezbytných balíčků NuGet nezdařila.

Pokud chcete soubory cookie pro ověřování sdílet mezi aplikací ASP.NET 4. x a aplikací ASP.NET Core, nakonfigurujte aplikaci ASP.NET Core tak, jak je uvedeno v části [sdílení ověřovacích souborů cookie mezi ASP.NET Coremi aplikacemi](#share-authentication-cookies-with-aspnet-core-identity) , a pak nakonfigurujte aplikaci ASP.NET 4. x následujícím způsobem.

Zkontrolujte, jestli jsou balíčky aplikace aktualizované na nejnovější verze. Do každé aplikace ASP.NET 4. x nainstalujte balíček [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) .

Vyhledejte a upravte volání `UseCookieAuthentication`:

* Změňte název souboru cookie tak, aby odpovídal názvu, který používá middleware pro ověřování souborů cookie ASP.NET Core (`.AspNet.SharedCookie` v příkladu).
* V následujícím příkladu je typ ověřování nastaven na `Identity.Application`.
* Zadejte instanci `DataProtectionProvider` inicializovaný do umístění úložiště klíčů pro Common data Protection.
* Ověřte, že je název aplikace nastavený na společný název aplikace používaný všemi aplikacemi, které sdílejí soubory cookie pro ověřování (`SharedCookieApp` v příkladu).

Pokud nenastavíte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` a `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, nastavte <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> na deklaraci identity, která odlišuje jedinečné uživatele.

*App_start/Startup.auth.cs*:

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

Při generování identity uživatele musí typ ověřování (`Identity.Application`) odpovídat typu definovanému v `AuthenticationType` nastaveném pomocí `UseCookieAuthentication` v *app_start/Startup.auth.cs*.

*Modely/IdentityModels. cs*:

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
