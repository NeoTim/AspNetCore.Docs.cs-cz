---
title: Ukázka SameSite souboru cookie pro ASP.NET Core 2,1 MVC
author: rick-anderson
description: Ukázka SameSite souboru cookie pro ASP.NET Core 2,1 MVC
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: 4239321531f3a7696a15b1dea164450ea0860c2b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409056"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="6d7fd-103">Ukázka SameSite souboru cookie pro ASP.NET Core 2,1 MVC</span><span class="sxs-lookup"><span data-stu-id="6d7fd-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="6d7fd-104">ASP.NET Core 2,1 obsahuje integrovanou podporu pro atribut [SameSite](https://www.owasp.org/index.php/SameSite) , ale byla zapsána do původního standardu.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="6d7fd-105">[Opravené chování](https://github.com/dotnet/aspnetcore/issues/8212) změnilo význam `SameSite.None` pro vygenerování atributu sameSite s hodnotou `None` , místo aby vůbec generoval hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="6d7fd-106">Pokud nechcete vygenerovat hodnotu, můžete nastavit `SameSite` vlastnost na soubor cookie na hodnotu-1.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="6d7fd-107">Zápis atributu SameSite</span><span class="sxs-lookup"><span data-stu-id="6d7fd-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="6d7fd-108">Následuje příklad, jak napsat atribut SameSite v souboru cookie:</span><span class="sxs-lookup"><span data-stu-id="6d7fd-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="6d7fd-109">Nastavení ověřování souborů cookie a souborů cookie stavu relace</span><span class="sxs-lookup"><span data-stu-id="6d7fd-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="6d7fd-110">Ověřování souborů cookie, stav relace a [různé další komponenty](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) nastavují své možnosti sameSite prostřednictvím možností souborů cookie, například</span><span class="sxs-lookup"><span data-stu-id="6d7fd-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="6d7fd-111">V předchozím kódu oba ověřování souborů cookie a stav relace nastaví jejich atribut sameSite na, který vygeneruje `None` atribut s `None` hodnotou a také nastaví atribut Secure na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="6d7fd-112">Spuštění ukázky</span><span class="sxs-lookup"><span data-stu-id="6d7fd-112">Run the sample</span></span>

<span data-ttu-id="6d7fd-113">Pokud spustíte [ukázkový projekt](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), načtěte ladicí program prohlížeče na úvodní stránce a použijte jej k zobrazení kolekce souborů cookie pro daný web.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="6d7fd-114">Uděláte to tak, že kliknete na tlačítko Edge a Chrome a `F12` potom vyberete `Application` kartu a kliknete na adresu URL webu pod `Cookies` možností v `Storage` části.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Seznam souborů cookie ladicího programu prohlížeče](BrowserDebugger.png)

<span data-ttu-id="6d7fd-116">Můžete vidět z obrázku, který je vytvořen souborem cookie vytvořeným v ukázce po kliknutí na tlačítko vytvořit soubor cookie SameSite má hodnotu atributu SameSite `Lax` , která odpovídá hodnotě nastavené v [ukázkovém kódu](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="6d7fd-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="6d7fd-117">Zachycení souborů cookie</span><span class="sxs-lookup"><span data-stu-id="6d7fd-117">Intercepting cookies</span></span>

<span data-ttu-id="6d7fd-118">Aby bylo možné zachytit soubory cookie, abyste mohli upravit hodnotu None podle podpory v agentovi prohlížeče uživatele, musíte použít `CookiePolicy` middleware.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="6d7fd-119">Toto musí být umístěno do kanálu požadavků HTTP **před** všemi komponentami, které zapisují soubory cookie a nakonfigurované v rámci `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="6d7fd-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="6d7fd-120">Pro vložení do kanálu použití `app.UseCookiePolicy()` v `Configure(IApplicationBuilder, IHostingEnvironment)` metodě v [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="6d7fd-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="6d7fd-121">Například:</span><span class="sxs-lookup"><span data-stu-id="6d7fd-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="6d7fd-122">Pak v případě, že `ConfigureServices(IServiceCollection services)` jsou soubory cookie připojeny nebo smazány, v části Konfigurace zásad souborů cookie, které se mají volat na pomocnou třídu.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="6d7fd-123">Například:</span><span class="sxs-lookup"><span data-stu-id="6d7fd-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="6d7fd-124">Pomocná funkce `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="6d7fd-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="6d7fd-125">Je volána, když jsou soubory cookie připojeny k požadavku nebo odstraněny z požadavku.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="6d7fd-126">Kontroluje, zda `SameSite` je vlastnost nastavena na hodnotu `None` .</span><span class="sxs-lookup"><span data-stu-id="6d7fd-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="6d7fd-127">Pokud `SameSite` je nastaven na `None` a aktuální uživatelský agent je známý, že není podporována hodnota atributu None.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="6d7fd-128">Tato kontrolu se provádí pomocí třídy [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="6d7fd-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="6d7fd-129">Nastaví, aby `SameSite` neemitoval hodnotu nastavením vlastnosti na.`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="6d7fd-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="6d7fd-130">Cílení na .NET Framework</span><span class="sxs-lookup"><span data-stu-id="6d7fd-130">Targeting .NET Framework</span></span>

<span data-ttu-id="6d7fd-131">ASP.NET Core a System. Web (ASP.NET Classic) mají nezávislé implementace SameSite.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="6d7fd-132">Opravy SameSite KB pro .NET Framework nejsou vyžadovány, pokud používáte ASP.NET Core a není požadován požadavek na verzi SameSite (.NET 4.7.2) pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="6d7fd-133">ASP.NET Core v rozhraní .NET vyžaduje aktualizaci závislostí balíčku NuGet, aby se získaly příslušné opravy.</span><span class="sxs-lookup"><span data-stu-id="6d7fd-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="6d7fd-134">Chcete-li získat ASP.NET Core změny .NET Framework Ujistěte se, že máte přímý odkaz na opravené balíčky a verze (2.1.14 nebo novější verze 2,1).</span><span class="sxs-lookup"><span data-stu-id="6d7fd-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="6d7fd-135">Další informace</span><span class="sxs-lookup"><span data-stu-id="6d7fd-135">More Information</span></span>
 
<span data-ttu-id="6d7fd-136">[Aktualizace](https://www.chromium.org/updates/same-site) 
 pro Chrome [Dokumentace k](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 
 ASP.NET Core SameSite [Oznámení o změně ASP.NET Core 2,1 SameSite](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="6d7fd-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>