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
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Sdílet soubory cookie pro ověřování mezi aplikací v ASP.NET

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)

Websites se často skládají z jednotlivých webových aplikací společně fungují. Pokud chcete poskytovat jednotné přihlašování (SSO), musíte webové aplikace v rámci lokality sdílet soubory cookie pro ověřování. Pro podporu tohoto scénáře, zásobník ochrany dat umožňuje sdílení Katana ověřování souborů cookie a lístků pro ověřování souborů cookie pomocí ASP.NET Core.

V následující příklady:

* Název souboru cookie ověřování je nastavena na hodnotu běžné `.AspNet.SharedCookie`.
* `AuthenticationType` Je nastavena na `Identity.Application` buď explicitně nebo implicitně.
* Běžný název aplikace se používá k povolení systém ochrany dat ke sdílení klíče ochrany dat (`SharedCookieApp`).
* `Identity.Application` slouží jako schéma ověřování. Jakékoli schéma se používá, musí být používány konzistentně *v různých i* sdílený soubor cookie aplikace jako výchozí schéma nebo explicitním nastavením. Schéma se používá při šifrování a dešifrování souborů cookie, takže konzistentní schéma musí využívat napříč aplikacemi.
* Společný [klíč ochrany dat](xref:security/data-protection/implementation/key-management) umístění úložiště se používá.
  * V aplikacích ASP.NET Core <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> slouží k nastavení umístění úložiště klíčů.
  * V aplikacích rozhraní .NET Framework, Middleware ověřování souborů Cookie používá provádění <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>. `DataProtectionProvider` poskytuje služby ochrany dat pro šifrování a dešifrování dat datové části souboru cookie ověřování. `DataProtectionProvider` Instance je izolovaná od systém ochrany dat používaný v ostatních částech aplikace. [DataProtectionProvider.Create (System.IO.DirectoryInfo, akce\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) přijímá <xref:System.IO.DirectoryInfo> a zadejte umístění pro ukládání klíčů ochrany dat.
* `DataProtectionProvider` vyžaduje [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) balíček NuGet:
  * V aplikacích ASP.NET Core 2.x, odkazovat [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).
  * V aplikacích rozhraní .NET Framework, přidejte odkaz na balíček pro [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Nastaví běžný název aplikace.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Sdílet soubory cookie pro ověřování mezi aplikací ASP.NET Core

Při použití technologie ASP.NET Core Identity:

* Data ochrany klíčů a název aplikace musí být sdílena mezi aplikacemi. Společné umístění úložiště klíčů je k dispozici na <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> metoda v následujících příkladech. Použití <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> konfigurace běžný název sdílené aplikace (`SharedCookieApp` v následujících příkladech). Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.
* Použití <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metodu rozšíření k nastavení služby ochrany dat pro soubory cookie.
* V následujícím příkladu je typ ověřování nastavený na `Identity.Application` ve výchozím nastavení.

V `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

Při používání souborů cookie bez ASP.NET Core Identity přímo, konfigurovat ochranu dat a ověřování v `Startup.ConfigureServices`. V následujícím příkladu je typ ověřování nastavený na `Identity.Application`:

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

Při hostování aplikací, které sdílení souborů cookie mezi subdomény, zadejte běžné doménu v [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) vlastnost. Ke sdílení souborů cookie mezi aplikacemi na `contoso.com`, jako například `first_subdomain.contoso.com` a `second_subdomain.contoso.com`, zadejte `Cookie.Domain` jako `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Šifrování klíče ochrany dat v klidovém stavu

Pro nasazení v produkčním prostředí, nakonfigurovat `DataProtectionProvider` šifrování klíčů v klidovém stavu pomocí rozhraní DPAPI nebo certifikátu x 509. Další informace naleznete v tématu <xref:security/data-protection/implementation/key-encryption-at-rest>. V následujícím příkladu je poskytnuta kryptografický otisk certifikátu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Sdílet soubory cookie pro ověřování mezi ASP.NET 4.x a aplikací ASP.NET Core

Aplikace ASP.NET 4.x, používající Middleware ověřování souborů Cookie Katana dá Generovat soubory cookie pro ověřování, které jsou kompatibilní s Middlewarem ověřování ASP.NET Core souboru Cookie. Díky tomu upgradem jednotlivých aplikací velkých webů v několika krocích zároveň hladký jednotné přihlašování v lokalitě.

Pokud aplikace používá Katana Middleware ověřování souborů Cookie, volá `UseCookieAuthentication` v projektu *Startup.Auth.cs* souboru. Projektech ASP.NET 4.x webové aplikace vytvořené pomocí sady Visual Studio 2013 a později použít Katana Middleware ověřování souborů Cookie ve výchozím nastavení. I když `UseCookieAuthentication` je zastaralý a pro aplikace ASP.NET Core, volání `UseCookieAuthentication` v ASP.NET 4.x aplikaci, která používá Katana Middleware ověřování souborů Cookie je platný.

Aplikace ASP.NET 4.x musí cílit na rozhraní .NET Framework 4.5.1 nebo novější. V opačném případě potřebné balíčky NuGet nepodaří nainstalovat.

Sdílet soubory cookie pro ověřování mezi aplikace ASP.NET 4.x a aplikace ASP.NET Core, konfiguraci aplikace ASP.NET Core, jak je uvedeno v [sdílet soubory cookie pro ověřování mezi aplikací ASP.NET Core](#share-authentication-cookies-among-aspnet-core-apps) části a pak nakonfigurujte aplikaci ASP.NET 4.x jako následuje.

Potvrďte, že balíčky aplikace se aktualizují na nejnovější verze. Nainstalujte [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) balíček do jednotlivých aplikací ASP.NET 4.x.

Vyhledejte a upravte volání `UseCookieAuthentication`:

* Změňte název souboru cookie na odpovídat názvu, používat Middleware ověřování souborů Cookie základní technologie ASP.NET (`.AspNet.SharedCookie` v příkladu).
* V následujícím příkladu je typ ověřování nastavený na `Identity.Application`.
* Zadat instanci `DataProtectionProvider` inicializovány na společné umístění dat ochrany úložiště klíčů.
* Potvrďte, že je název aplikace nastavený na běžný název aplikaci používat všechny aplikace, které sdílejí soubory cookie pro ověřování (`SharedCookieApp` v příkladu).

Pokud není nastavení `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` a `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, nastavte <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> k deklaraci identity, která odlišuje jedinečných uživatelů.

*App_Start/Startup.auth.cs*:

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

Při generování identitu uživatele, typ ověřování (`Identity.Application`) musí odpovídat typ definovaný v `AuthenticationType` sadu s `UseCookieAuthentication` v *App_Start/Startup.Auth.cs*.

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

## <a name="use-a-common-user-database"></a>Použít běžné uživatelské databázi

Když aplikace použít stejnou identitu schématu (stejnou verzi Identity), potvrďte, že systém Identity pro každé aplikaci, která ukazuje na stejné uživatele databáze. V opačném případě systém identit vytvoří chyby za běhu při pokusí se porovnat informace v souboru cookie pro ověřování proti informací ve své databázi.

Pokud schéma Identity se liší mezi aplikacemi, obvykle vzhledem k tomu aplikace používáte různé verze Identity založené na nejnovější verzi Identity společnou databázi pro sdílení obsahu není možné bez přemapování a přidání sloupců ve schématech Identity jinou aplikaci. Často je efektivnější upgrade dalších aplikací používat nejnovější verzi Identity tak, aby aplikace může sdílet společnou databázi.

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
