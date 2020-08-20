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
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Sdílení ověřování cookie s mezi ASP.NET aplikacemi

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Weby se často skládají z jednotlivých vzájemně pracujících webových aplikací. Aby bylo možné zajistit jednotné přihlašování (SSO), musí webové aplikace v rámci lokality sdílet ověřování cookie s. Pro podporu tohoto scénáře umožňuje zásobník ochrany dat sdílení Katana cookie ověřování a ověřovacích cookie lístků pro ASP.NET Core.

V následujících příkladech:

* Název ověřování cookie je nastaven na běžnou hodnotu `.AspNet.SharedCookie` .
* `AuthenticationType`Je nastaven `Identity.Application` buď explicitně, nebo jako výchozí.
* Běžný název aplikace se používá k tomu, aby systém ochrany dat mohl sdílet klíče ochrany dat ( `SharedCookieApp` ).
* `Identity.Application` slouží jako schéma ověřování. Bez ohledu na to, jaké schéma se používá, je potřeba ho konzistentně používat *v rámci a napříč* sdílenými cookie aplikacemi buď jako výchozí schéma, nebo explicitně nastavením. Schéma se používá při šifrování a dešifrování cookie s, takže v aplikacích je nutné použít konzistentní schéma.
* Používá se společné umístění úložiště [klíčů pro ochranu dat](xref:security/data-protection/implementation/key-management) .
  * V ASP.NET Core aplikace <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> se používá k nastavení umístění úložiště klíčů.
  * V .NET Framework aplikace Cookie používá middleware ověřování implementaci <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` poskytuje služby ochrany dat pro šifrování a dešifrování cookie dat datové části ověřování. `DataProtectionProvider`Instance je izolovaná od systému ochrany dat, který používají jiné části aplikace. [DataProtectionProvider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) přijme a <xref:System.IO.DirectoryInfo> určí umístění pro úložiště klíčů ochrany dat.
* `DataProtectionProvider` vyžaduje balíček NuGet [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * V aplikacích ASP.NET Core 2. x odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).
  * V .NET Framework aplikace přidejte odkaz na balíček do [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Nastaví běžný název aplikace.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Sdílet ověřování cookie s ASP.NET Core Identity

Při použití ASP.NET Core Identity :

* Klíče ochrany dat a název aplikace se musí sdílet mezi aplikacemi. K <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> metodě v následujících příkladech je poskytováno společné umístění úložiště klíčů. Použijte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> ke konfiguraci společného názvu sdílené aplikace ( `SharedCookieApp` v následujících příkladech). Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.
* Použijte <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metodu rozšíření k nastavení služby ochrany dat pro cookie s.
* Výchozí typ ověřování je `Identity.Application` .

V `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Sdílet ověřování cookie s bez ASP.NET Core Identity

Při použití cookie s přímo bez ASP.NET Core Identity toho nakonfigurujte ochranu a ověřování dat v `Startup.ConfigureServices` . V následujícím příkladu je typ ověřování nastaven na `Identity.Application` :

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

## <a name="share-no-loccookies-across-different-base-paths"></a>Sdílet cookie s v různých základních cestách

Ověřování cookie používá jako výchozí hodnotu [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ Cookie . Cesta](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Pokud musí být aplikace cookie sdílená mezi různými základními cestami, `Path` musí být přepsána:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Sdílet cookie s mezi subdoménami

Při hostování aplikací, které sdílí cookie s mezi subdoménami, určete v nástroji společnou doménu [ Cookie . Doménová](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) vlastnost. Pokud chcete sdílet cookie s napříč aplikacemi `contoso.com` , například `first_subdomain.contoso.com` a `second_subdomain.contoso.com` , zadejte `Cookie.Domain` jako `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Šifrovat klíče ochrany dat v klidovém umístění

V případě nasazení v produkčním `DataProtectionProvider` prostředí nakonfigurujte šifrování klíčů v klidovém formátu pomocí DPAPI nebo certifikátu x509. Další informace naleznete v tématu <xref:security/data-protection/implementation/key-encryption-at-rest>. V následujícím příkladu je k dispozici kryptografický otisk certifikátu <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Sdílení ověřování cookie s mezi ASP.NET 4. x a ASP.NET Core aplikacemi

Aplikace ASP.NET 4. x, které používají Cookie middleware pro ověřování Katana, se dají nakonfigurovat tak, aby vygenerovaly ověřování cookie s, které jsou kompatibilní s Cookie middlewarem ověřování ASP.NET Core. To umožňuje v několika krocích upgradovat jednotlivé aplikace velké lokality a zajistit tak hladké možnosti jednotného přihlašování napříč lokalitami.

Když aplikace používá Cookie middleware ověřování Katana, volá `UseCookieAuthentication` v souboru *Startup.auth.cs* projektu. Projekty webové aplikace ASP.NET 4. x vytvořené pomocí Visual Studio 2013 a později Cookie ve výchozím nastavení používají middleware Katana Authentication. I když `UseCookieAuthentication` je zastaralá a podporovaná pro ASP.NET Core aplikace, volání `UseCookieAuthentication` v aplikaci ASP.NET 4. x, která používá Cookie middleware ověřování Katana, je platná.

Aplikace ASP.NET 4. x musí cílit na .NET Framework 4.5.1 nebo novější. V opačném případě se instalace nezbytných balíčků NuGet nezdařila.

Pokud chcete sdílet ověřování cookie mezi aplikací ASP.NET 4. x a aplikací ASP.NET Core, nakonfigurujte aplikaci ASP.NET Core tak, jak je uvedeno v části [sdílení ověřování cookie s ASP.NET Core aplikací](#share-authentication-cookies-with-aspnet-core-identity) , a pak nakonfigurujte aplikaci ASP.NET 4. x následujícím způsobem.

Zkontrolujte, jestli jsou balíčky aplikace aktualizované na nejnovější verze. Do každé aplikace ASP.NET 4. x nainstalujte balíček [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) .

Vyhledejte a upravte volání na `UseCookieAuthentication` :

* Změňte cookie název tak, aby odpovídal názvu používanému Cookie middleware ověřování ASP.NET Core ( `.AspNet.SharedCookie` v příkladu).
* V následujícím příkladu je typ ověřování nastaven na `Identity.Application` .
* Zadejte instanci `DataProtectionProvider` inicializovaného do umístění úložiště klíčů pro Common data Protection.
* Ověřte, že je název aplikace nastavený na společný název aplikace používaný všemi aplikacemi, které sdílí ověřování cookie s ( `SharedCookieApp` v tomto příkladu).

Pokud není `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` nastavené a, nastavte <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> na deklaraci identity, která rozlišuje jedinečné uživatele.

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

Při generování identity uživatele musí typ ověřování ( `Identity.Application` ) odpovídat typu definovanému v `AuthenticationType` `UseCookieAuthentication` *app_start/Startup.auth.cs*.

*Modely/ Identity Models.cs*:

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

Když aplikace používají stejné Identity schéma (stejné verze Identity ), zkontrolujte, že Identity je systém pro každou aplikaci odkazován na stejnou uživatelskou databázi. V opačném případě systém identit vytvoří selhání za běhu při pokusu o shodu s informacemi v ověřování cookie proti informacím v příslušné databázi.

Když se Identity schéma mezi aplikacemi liší, obvykle protože aplikace používají různé Identity verze, sdílení společné databáze na základě nejnovější verze Identity není možné bez nutnosti přemapování a přidávání sloupců do schémat jiných aplikací Identity . Je často efektivnější upgradovat ostatní aplikace tak, aby používaly nejnovější Identity verzi, aby bylo možné sdílet aplikace do společné databáze.

## <a name="additional-resources"></a>Další materiály

* <xref:host-and-deploy/web-farm>
