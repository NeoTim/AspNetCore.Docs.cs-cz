---
title: Co je nového v ASP.NET Core 2.0
author: rick-anderson
description: Informace o nových funkcích v ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2019
uid: aspnetcore-2.0
ms.openlocfilehash: dac5a48042a71774cb884e30a166be68bee69691
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048107"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="84265-103">Co je nového v ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="84265-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="84265-104">Tento článek se soustředí na nejdůležitější změny provedené v ASP.NET Core 2.0, odkazy na relevantní dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="84265-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="84265-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="84265-105">Razor Pages</span></span>

<span data-ttu-id="84265-106">Stránky Razor je nová funkce služby ASP.NET Core MVC, která provádí kódování zaměřené na stránce scénáře jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="84265-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="84265-107">Další informace najdete v tématu Úvod a kurzu:</span><span class="sxs-lookup"><span data-stu-id="84265-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="84265-108">Úvod do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="84265-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="84265-109">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="84265-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="84265-110">ASP.NET Core Microsoft.aspnetcore.all</span><span class="sxs-lookup"><span data-stu-id="84265-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="84265-111">Nové Microsoft.aspnetcore.all ASP.NET Core zahrnuje všechny balíčky provedli a podporuje ASP.NET Core a Entity Framework Core týmy společně s jejich závislostmi interní a 3. stran.</span><span class="sxs-lookup"><span data-stu-id="84265-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="84265-112">Už nemusíte vybrat jednotlivé ASP.NET Core funkce balíčkem.</span><span class="sxs-lookup"><span data-stu-id="84265-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="84265-113">Všechny funkce jsou součástí [metabalíček](https://www.nuget.org/packages/Microsoft.AspNetCore.All) balíčku.</span><span class="sxs-lookup"><span data-stu-id="84265-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="84265-114">Tento balíček použít výchozí šablony.</span><span class="sxs-lookup"><span data-stu-id="84265-114">The default templates use this package.</span></span>

<span data-ttu-id="84265-115">Další informace najdete v tématu [metabalíček Microsoft.aspnetcore.all pro ASP.NET Core 2.0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="84265-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="84265-116">Modul runtime Store</span><span class="sxs-lookup"><span data-stu-id="84265-116">Runtime Store</span></span>

<span data-ttu-id="84265-117">Aplikace, které používají `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all automaticky využívat nové Store Runtime jádra .NET.</span><span class="sxs-lookup"><span data-stu-id="84265-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="84265-118">Store obsahuje všechny prostředky modulu runtime potřebné ke spouštění aplikací ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="84265-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="84265-119">Při použití `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all žádné prostředky z balíčků odkazovaných ASP.NET Core NuGet jsou nasazeny s aplikací, protože se již nacházejí v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="84265-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="84265-120">Prostředky v modulu Runtime Store jsou také předkompilované zlepšit dobu spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="84265-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="84265-121">Další informace najdete v tématu [úložiště modulu Runtime](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="84265-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="84265-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="84265-122">.NET Standard 2.0</span></span>

<span data-ttu-id="84265-123">ASP.NET Core 2.0 balíčky cílit na .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="84265-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="84265-124">Balíčky mohou odkazovat další knihovny .NET Standard 2.0, a můžete spustit na .NET Standard 2.0 vyhovující implementace .NET, včetně .NET Core 2.0 a .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="84265-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="84265-125">`Microsoft.AspNetCore.All` Microsoft.aspnetcore.all cílí na .NET Core 2.0 pouze, protože je určen pro použití s Store modulu Runtime .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="84265-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="84265-126">Aktualizace konfigurace</span><span class="sxs-lookup"><span data-stu-id="84265-126">Configuration update</span></span>

<span data-ttu-id="84265-127">`IConfiguration` Instance služby kontejneru přidá ve výchozím nastavení v ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="84265-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="84265-128">`IConfiguration` ve službách kontejneru usnadňuje pro aplikace pro načtení hodnoty konfigurace z kontejneru.</span><span class="sxs-lookup"><span data-stu-id="84265-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="84265-129">Informace o stavu plánované dokumentaci najdete v tématu [problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="84265-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="84265-130">Protokolování aktualizace</span><span class="sxs-lookup"><span data-stu-id="84265-130">Logging update</span></span>

<span data-ttu-id="84265-131">V technologii ASP.NET Core 2.0 je ve výchozím nastavení protokolování součástí závislost injektor (DI).</span><span class="sxs-lookup"><span data-stu-id="84265-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="84265-132">Přidat poskytovatele a konfigurovat filtrování v *Program.cs* místo v souboru *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="84265-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="84265-133">A ve výchozím nastavení `ILoggerFactory` podporuje filtrování způsobem, který vám umožní používat flexibilní jedním z přístupů pro poskytovatele křížové filtrování a filtrování konkrétního zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="84265-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="84265-134">Další informace najdete v tématu [Úvod k protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="84265-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="84265-135">Aktualizace ověřování</span><span class="sxs-lookup"><span data-stu-id="84265-135">Authentication update</span></span>

<span data-ttu-id="84265-136">Konfigurace ověřování pro aplikaci s využitím DI usnadňuje nový model ověřování.</span><span class="sxs-lookup"><span data-stu-id="84265-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="84265-137">Nové šablony jsou dostupné pro konfiguraci ověřování pro webové aplikace a webové rozhraní API pomocí [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="84265-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="84265-138">Informace o stavu plánované dokumentaci najdete v tématu [problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="84265-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="84265-139">Aktualizace identity</span><span class="sxs-lookup"><span data-stu-id="84265-139">Identity update</span></span>

<span data-ttu-id="84265-140">Provedli jsme je snazší zajistit zabezpečení webového rozhraní API pomocí Identity v ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="84265-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="84265-141">Můžete získat přístupové tokeny pro přístup pomocí webového rozhraní API [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="84265-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="84265-142">Další informace o ověřování změny ve verzi 2.0 naleznete na následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="84265-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="84265-143">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84265-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="84265-144">Povolit generování kódu QR pro aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84265-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="84265-145">Ověřování a identitu migrovat do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="84265-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="84265-146">Šablon SPA</span><span class="sxs-lookup"><span data-stu-id="84265-146">SPA templates</span></span>

<span data-ttu-id="84265-147">Jeden šablony projektu stránka aplikace (SPA) pro Angular, Aurelia, knihovnou Knockout.js, React.js a React.js s Reduxem jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="84265-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="84265-148">Aktualizovali jsme Angular šablony Angular 4.</span><span class="sxs-lookup"><span data-stu-id="84265-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="84265-149">Jsou k dispozici ve výchozím nastavení; šablony Angular a React informace o tom, jak získat další šablony najdete v tématu [vytvořte nový projekt SPA](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="84265-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="84265-150">Informace o tom, jak vytvářet aplikace SPA v ASP.NET Core najdete v tématu <xref:client-side/spa-services>.</span><span class="sxs-lookup"><span data-stu-id="84265-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="84265-151">Vylepšení kestrel</span><span class="sxs-lookup"><span data-stu-id="84265-151">Kestrel improvements</span></span>

<span data-ttu-id="84265-152">Webový server Kestrel má nové funkce, které usnadňují vhodnější jako server přístupem k Internetu.</span><span class="sxs-lookup"><span data-stu-id="84265-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="84265-153">Počet možnosti konfigurace serveru omezení přidány `KestrelServerOptions` vaší třídy nový `Limits` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="84265-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="84265-154">Přidáte omezení pro následující:</span><span class="sxs-lookup"><span data-stu-id="84265-154">Add limits for the following:</span></span>

* <span data-ttu-id="84265-155">Maximální počet klientských připojení</span><span class="sxs-lookup"><span data-stu-id="84265-155">Maximum client connections</span></span>
* <span data-ttu-id="84265-156">Velikost textu maximální požadavku</span><span class="sxs-lookup"><span data-stu-id="84265-156">Maximum request body size</span></span>
* <span data-ttu-id="84265-157">Minimální požadavek tělo přenosová rychlost</span><span class="sxs-lookup"><span data-stu-id="84265-157">Minimum request body data rate</span></span>

<span data-ttu-id="84265-158">Další informace najdete v tématu [Kestrel webového serveru provedení v ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="84265-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="84265-159">Přejmenovat soubor HTTP.sys WebListener</span><span class="sxs-lookup"><span data-stu-id="84265-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="84265-160">Balíčky `Microsoft.AspNetCore.Server.WebListener` a `Microsoft.Net.Http.Server` byly sloučeny do nového balíčku `Microsoft.AspNetCore.Server.HttpSys`.</span><span class="sxs-lookup"><span data-stu-id="84265-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="84265-161">Obory názvů jsou aktualizované tak, aby odpovídaly.</span><span class="sxs-lookup"><span data-stu-id="84265-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="84265-162">Další informace najdete v tématu [implementaci serveru HTTP.sys web v ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="84265-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="84265-163">Vylepšená podpora hlavičky protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="84265-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="84265-164">Při použití MVC přenášet `FileStreamResult` nebo `FileContentResult`, teď máte možnost nastavit `ETag` nebo `LastModified` datum přenosu obsahu.</span><span class="sxs-lookup"><span data-stu-id="84265-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="84265-165">Můžete nastavit tyto hodnoty na vrácený obsah s kódem podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="84265-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="84265-166">Soubor vrátil návštěvnících bude doplněn správné hlavičky HTTP pro `ETag` a `LastModified` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="84265-166">The file returned to your visitors will be decorated with the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="84265-167">Pokud návštěvníka žádosti o obsah aplikace se požadavek na zjištění rozsahu, ASP.NET Core rozpozná požadavek a zpracovává hlavičky.</span><span class="sxs-lookup"><span data-stu-id="84265-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="84265-168">Pokud požadovaný obsah může doručit částečně, ASP.NET Core odpovídajícím způsobem přeskočí a vrátí jenom požadované sadě bajtů.</span><span class="sxs-lookup"><span data-stu-id="84265-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="84265-169">Není nutné k zápisu do metody pro přizpůsobení nebo zpracování této funkce; všechny speciální obslužné rutiny To se automaticky postará služba za vás.</span><span class="sxs-lookup"><span data-stu-id="84265-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="84265-170">Hostování spuštění a Application Insights</span><span class="sxs-lookup"><span data-stu-id="84265-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="84265-171">Hostitelská prostředí lze nyní vložení navíc závislosti a spouštění kódu během spuštění aplikace, aniž by museli explicitně věděly, nebo volat všechny metody aplikace.</span><span class="sxs-lookup"><span data-stu-id="84265-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="84265-172">Tato funkce slouží k povolení určitých prostředí pro funkce "světla nahoru" jedinečné do daného prostředí bez aplikace nepotřebuje vědět předem.</span><span class="sxs-lookup"><span data-stu-id="84265-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="84265-173">V technologii ASP.NET Core 2.0, tato funkce slouží k automaticky povolit diagnostiku Application Insights při ladění v sadě Visual Studio a (po vyjádření výslovného souhlasu) při spuštění v Azure App Services.</span><span class="sxs-lookup"><span data-stu-id="84265-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="84265-174">V důsledku toho šablony projektu už přidat balíčky Application Insights a kódu ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="84265-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="84265-175">Informace o stavu plánované dokumentaci najdete v tématu [problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="84265-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="84265-176">Automatické použití tokenů proti padělání</span><span class="sxs-lookup"><span data-stu-id="84265-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="84265-177">ASP.NET Core se povedlo vždy s kódováním HTML obsah ve výchozím nastavení, ale s novou verzí pořízení umožňující přidat další krok útokům podvržení žádosti padělání (XSRF).</span><span class="sxs-lookup"><span data-stu-id="84265-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="84265-178">ASP.NET Core se teď generování tokenů proti padělání ve výchozím nastavení a ověření na akce POST formuláře a stránky bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="84265-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="84265-179">Další informace najdete v tématu [útokům zabránilo webů žádosti padělání (XSRF/CSRF)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="84265-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="84265-180">Automatická předkompilace</span><span class="sxs-lookup"><span data-stu-id="84265-180">Automatic precompilation</span></span>

<span data-ttu-id="84265-181">Předkompilace Razor zobrazení je standardně během publikování, snížení velikosti výstup publikování a aplikace času spuštění.</span><span class="sxs-lookup"><span data-stu-id="84265-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="84265-182">Další informace najdete v tématu [kompilace zobrazení Razor a předkompilace v ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="84265-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="84265-183">Razor podporu pro C# 7.1</span><span class="sxs-lookup"><span data-stu-id="84265-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="84265-184">Pro práci s nový kompilátor Roslyn se aktualizoval zobrazovací modul Razor.</span><span class="sxs-lookup"><span data-stu-id="84265-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="84265-185">Který zahrnuje podporu pro funkce C# 7.1 jako výchozí výrazy odvodit názvy řazené kolekce členů a porovnávání vzorů s obecnými typy.</span><span class="sxs-lookup"><span data-stu-id="84265-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="84265-186">Použití jazyka C# 7.1 ve vašem projektu, přidejte následující vlastnost v souboru projektu a pak znovu načíst řešení:</span><span class="sxs-lookup"><span data-stu-id="84265-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="84265-187">Informace o stavu funkce jazyka C# 7.1, naleznete v tématu [úložiště Roslyn GitHub](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="84265-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="84265-188">Další aktualizace dokumentace pro 2.0</span><span class="sxs-lookup"><span data-stu-id="84265-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="84265-189">Visual Studio publikačních profilů pro nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84265-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="84265-190">Správa klíčů</span><span class="sxs-lookup"><span data-stu-id="84265-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="84265-191">Konfigurace ověřování sítě Facebook</span><span class="sxs-lookup"><span data-stu-id="84265-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="84265-192">Konfigurace ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="84265-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="84265-193">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="84265-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="84265-194">Konfigurace ověřování Account Microsoft</span><span class="sxs-lookup"><span data-stu-id="84265-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="84265-195">Pokyny k migraci</span><span class="sxs-lookup"><span data-stu-id="84265-195">Migration guidance</span></span>

<span data-ttu-id="84265-196">Informace o tom, jak migrovat aplikace ASP.NET Core 1.x do ASP.NET Core 2.0 naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="84265-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="84265-197">Migrace z technologie ASP.NET Core 1.x do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="84265-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="84265-198">Ověřování a identitu migrovat do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="84265-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="84265-199">Další informace</span><span class="sxs-lookup"><span data-stu-id="84265-199">Additional Information</span></span>

<span data-ttu-id="84265-200">Úplný seznam změn, najdete v článku [poznámky k verzi pro ASP.NET Core 2.0](https://github.com/aspnet/Home/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="84265-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/aspnet/Home/releases/tag/2.0.0).</span></span>

<span data-ttu-id="84265-201">Spojte se s vývojovým týmem ASP.NET Core průběh a plány, Nalaďte si [rychlá schůzka komunitě ASP.NET](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="84265-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
