---
title: Co je nového v ASP.NET Core 2.0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667256"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="a7b47-103">Co je nového v ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="a7b47-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="a7b47-104">Tento článek upozorňuje na nejvýznamnější změny v ASP.NET Core 2.0 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="a7b47-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="a7b47-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a7b47-105">Razor Pages</span></span>

<span data-ttu-id="a7b47-106">Razor Pages je nová funkce ASP.NET Core MVC, která usnadňuje a zkušeněji kódování scénářů zaměřených na stránky.</span><span class="sxs-lookup"><span data-stu-id="a7b47-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="a7b47-107">Další informace naleznete v úvodu a kurzu:</span><span class="sxs-lookup"><span data-stu-id="a7b47-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="a7b47-108">Úvod do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a7b47-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="a7b47-109">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="a7b47-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="a7b47-110">metabalíček ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7b47-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="a7b47-111">Nový metabalíček ASP.NET Core zahrnuje všechny balíčky vyrobené a podporované týmy ASP.NET core a entity framework core spolu s jejich vnitřními závislostmi a závislostmi třetích stran.</span><span class="sxs-lookup"><span data-stu-id="a7b47-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="a7b47-112">Již nemusíte vybírat jednotlivé funkce ASP.NET Core podle balíčku.</span><span class="sxs-lookup"><span data-stu-id="a7b47-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="a7b47-113">Všechny funkce jsou součástí balíčku [Microsoft.AspNetCore.All.](https://www.nuget.org/packages/Microsoft.AspNetCore.All)</span><span class="sxs-lookup"><span data-stu-id="a7b47-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="a7b47-114">Výchozí šablony používají tento balíček.</span><span class="sxs-lookup"><span data-stu-id="a7b47-114">The default templates use this package.</span></span>

<span data-ttu-id="a7b47-115">Další informace naleznete v [metabalíčku Microsoft.AspNetCore.All pro ASP.NET Core 2.0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="a7b47-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="a7b47-116">Runtime Obchod</span><span class="sxs-lookup"><span data-stu-id="a7b47-116">Runtime Store</span></span>

<span data-ttu-id="a7b47-117">Aplikace, které `Microsoft.AspNetCore.All` používají metabalíček, automaticky využívají výhod nového úložiště .NET Core Runtime Store.</span><span class="sxs-lookup"><span data-stu-id="a7b47-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="a7b47-118">Úložiště obsahuje všechny datové zdroje za běhu potřebné ke spuštění ASP.NET aplikací Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="a7b47-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="a7b47-119">Při použití `Microsoft.AspNetCore.All` metabalíčku žádné prostředky z odkazované ASP.NET balíčky Core NuGet jsou nasazeny s aplikací, protože již jsou umístěny v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="a7b47-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="a7b47-120">Prostředky v runtime store jsou také předkompilovány ke zlepšení doby spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7b47-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="a7b47-121">Další informace naleznete [v tématu Runtime store](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="a7b47-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="a7b47-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="a7b47-122">.NET Standard 2.0</span></span>

<span data-ttu-id="a7b47-123">Balíčky ASP.NET Core 2.0 cílí na standard .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="a7b47-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="a7b47-124">Na balíčky lze odkazovat jinými knihovnami .NET Standard 2.0 a lze je spouštět v implementacích rozhraní .NET Standard 2.0 rozhraní .NET, včetně rozhraní .NET Core 2.0 a rozhraní .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="a7b47-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="a7b47-125">Metapackage `Microsoft.AspNetCore.All` cílí pouze na rozhraní .NET Core 2.0, protože je určen pro použití s úložištěm runtime .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="a7b47-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="a7b47-126">Aktualizace konfigurace</span><span class="sxs-lookup"><span data-stu-id="a7b47-126">Configuration update</span></span>

<span data-ttu-id="a7b47-127">Instance `IConfiguration` je přidána do kontejneru služeb ve výchozím nastavení v ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="a7b47-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="a7b47-128">`IConfiguration`v kontejneru služeb usnadňuje aplikacím načíst hodnoty konfigurace z kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a7b47-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="a7b47-129">Informace o stavu plánované dokumentace naleznete v [tématu problém GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="a7b47-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="a7b47-130">Aktualizace protokolování</span><span class="sxs-lookup"><span data-stu-id="a7b47-130">Logging update</span></span>

<span data-ttu-id="a7b47-131">V ASP.NET Core 2.0 protokolování je začleněna do systému vkládání závislostí (DI) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="a7b47-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="a7b47-132">Můžete přidat zprostředkovatele a nakonfigurovat filtrování v *souboru Program.cs* namísto v *souboru Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="a7b47-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="a7b47-133">A výchozí `ILoggerFactory` podporuje filtrování způsobem, který umožňuje použít jeden flexibilní přístup pro filtrování mezi zprostředkovateli a filtrování konkrétního zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="a7b47-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="a7b47-134">Další informace naleznete [v tématu Úvod do protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a7b47-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="a7b47-135">Aktualizace ověřování</span><span class="sxs-lookup"><span data-stu-id="a7b47-135">Authentication update</span></span>

<span data-ttu-id="a7b47-136">Nový model ověřování usnadňuje konfiguraci ověřování pro aplikaci pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="a7b47-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="a7b47-137">Nové šablony jsou k dispozici pro konfiguraci ověřování pro webové aplikace a webová rozhraní API pomocí [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="a7b47-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="a7b47-138">Informace o stavu plánované dokumentace naleznete v [tématu problém GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="a7b47-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="a7b47-139">Aktualizace identity</span><span class="sxs-lookup"><span data-stu-id="a7b47-139">Identity update</span></span>

<span data-ttu-id="a7b47-140">Usnadnili jsme vytváření zabezpečených webových api pomocí identity v ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="a7b47-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="a7b47-141">Přístupové tokeny pro přístup k webovým rozhraním API můžete získat pomocí [knihovny Microsoft Authentication Library (MSAL).](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="a7b47-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="a7b47-142">Další informace o změnách ověřování v protokolu 2.0 naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="a7b47-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="a7b47-143">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7b47-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a7b47-144">Povolit generování QR kódu pro ověřovací aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7b47-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="a7b47-145">Migrace ověřování a identity do ASP.NET jádra 2.0</span><span class="sxs-lookup"><span data-stu-id="a7b47-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="a7b47-146">Šablony SPA</span><span class="sxs-lookup"><span data-stu-id="a7b47-146">SPA templates</span></span>

<span data-ttu-id="a7b47-147">K dispozici jsou šablony projektu Jednostránkové aplikace (SPA) pro Angular, Aurelia, Knockout.js, React.js a React.js s Redux.</span><span class="sxs-lookup"><span data-stu-id="a7b47-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="a7b47-148">Úhlová šablona byla aktualizována na Úhlové 4.</span><span class="sxs-lookup"><span data-stu-id="a7b47-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="a7b47-149">Šablony Angular a React jsou ve výchozím nastavení k dispozici. Informace o tom, jak získat další šablony, naleznete v [tématu Vytvoření nového projektu SPA](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="a7b47-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="a7b47-150">Informace o tom, jak vytvořit spa <xref:client-side/spa-services>v ASP.NET Core, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="a7b47-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="a7b47-151">Zlepšení kestrelu</span><span class="sxs-lookup"><span data-stu-id="a7b47-151">Kestrel improvements</span></span>

<span data-ttu-id="a7b47-152">Webový server Kestrel má nové funkce, díky kterým je vhodnější jako internetový server.</span><span class="sxs-lookup"><span data-stu-id="a7b47-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="a7b47-153">Do nové `KestrelServerOptions` `Limits` vlastnosti třídy je přidáno několik možností konfigurace omezení serveru.</span><span class="sxs-lookup"><span data-stu-id="a7b47-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="a7b47-154">Přidejte omezení pro následující:</span><span class="sxs-lookup"><span data-stu-id="a7b47-154">Add limits for the following:</span></span>

* <span data-ttu-id="a7b47-155">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="a7b47-155">Maximum client connections</span></span>
* <span data-ttu-id="a7b47-156">Maximální velikost těla požadavku</span><span class="sxs-lookup"><span data-stu-id="a7b47-156">Maximum request body size</span></span>
* <span data-ttu-id="a7b47-157">Minimální přenosová rychlost tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="a7b47-157">Minimum request body data rate</span></span>

<span data-ttu-id="a7b47-158">Další informace naleznete [v tématu Kestrel implementace webového serveru v ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="a7b47-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="a7b47-159">WebListener přejmenován na HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a7b47-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="a7b47-160">Balíčky `Microsoft.AspNetCore.Server.WebListener` `Microsoft.Net.Http.Server` a byly sloučeny `Microsoft.AspNetCore.Server.HttpSys`do nového balíčku .</span><span class="sxs-lookup"><span data-stu-id="a7b47-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="a7b47-161">Obory názvů byly aktualizovány tak, aby odpovídaly.</span><span class="sxs-lookup"><span data-stu-id="a7b47-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="a7b47-162">Další informace naleznete [v tématu implementace webového serveru HTTP.sys v ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="a7b47-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="a7b47-163">Rozšířená podpora záhlaví protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="a7b47-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="a7b47-164">Při použití MVC `FileStreamResult` k `FileContentResult`přenosu nebo , nyní máte `ETag` možnost `LastModified` nastavit nebo datum na obsah, který přenášíte.</span><span class="sxs-lookup"><span data-stu-id="a7b47-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="a7b47-165">Tyto hodnoty můžete nastavit na vrácený obsah s kódem podobným následující:</span><span class="sxs-lookup"><span data-stu-id="a7b47-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="a7b47-166">Soubor vrácený návštěvníkům má příslušná http `ETag` `LastModified` záhlaví pro hodnoty a.</span><span class="sxs-lookup"><span data-stu-id="a7b47-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="a7b47-167">Pokud návštěvník aplikace požaduje obsah s hlavičkou požadavku rozsahu, ASP.NET Core požadavek rozpozná a zzpracovává záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a7b47-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="a7b47-168">Pokud požadovaný obsah lze částečně doručit, ASP.NET Core odpovídajícím způsobem přeskočí a vrátí pouze požadovanou sadu bajtů.</span><span class="sxs-lookup"><span data-stu-id="a7b47-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="a7b47-169">Není nutné zapisovat žádné speciální obslužné rutiny do metod přizpůsobit nebo zpracovat tuto funkci; je automaticky zpracována za vás.</span><span class="sxs-lookup"><span data-stu-id="a7b47-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="a7b47-170">Hostování spouštění a přehledů aplikací</span><span class="sxs-lookup"><span data-stu-id="a7b47-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="a7b47-171">Hostitelská prostředí nyní můžete vložit další balíček závislostí a spustit kód při spuštění aplikace, aniž by aplikace nutnost explicitně trvat závislost nebo volání jakékoli metody.</span><span class="sxs-lookup"><span data-stu-id="a7b47-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="a7b47-172">Tuto funkci lze použít k povolení některých prostředí "light-up" funkce jedinečné pro toto prostředí bez aplikace potřebují vědět dopředu.</span><span class="sxs-lookup"><span data-stu-id="a7b47-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="a7b47-173">V ASP.NET Core 2.0 se tato funkce používá k automatickému povolení diagnostiky Application Insights při ladění v sadě Visual Studio a (po přihlášení) při spuštění ve službě Azure App Services.</span><span class="sxs-lookup"><span data-stu-id="a7b47-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="a7b47-174">V důsledku toho šablony projektu již nepřidávají balíčky Application Insights a kód ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="a7b47-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="a7b47-175">Informace o stavu plánované dokumentace naleznete v [tématu problém GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="a7b47-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="a7b47-176">Automatické používání antipadělkových žetonů</span><span class="sxs-lookup"><span data-stu-id="a7b47-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="a7b47-177">ASP.NET Core vždy pomáhal html-kódovat obsah ve výchozím nastavení, ale s novou verzí je přijatdalší krok, aby se zabránilo útokům padělání žádostí mezi weby (XSRF).</span><span class="sxs-lookup"><span data-stu-id="a7b47-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="a7b47-178">ASP.NET Core nyní ve výchozím nastavení vyzařují tokeny anti-padělanosti a ověřují je na akcích a stránkách formuláře POST bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a7b47-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="a7b47-179">Další informace naleznete [v tématu Zabránění útokům na vyžádání mezi servery (XSRF/CSRF).](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="a7b47-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="a7b47-180">Automatická předkompilace</span><span class="sxs-lookup"><span data-stu-id="a7b47-180">Automatic precompilation</span></span>

<span data-ttu-id="a7b47-181">Razor view pre-kompilace je povolena během publikování ve výchozím nastavení, což snižuje velikost výstupu publikování a čas spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7b47-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="a7b47-182">Další informace naleznete [v tématu Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="a7b47-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="a7b47-183">Podpora holicího strojku pro C# 7.1</span><span class="sxs-lookup"><span data-stu-id="a7b47-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="a7b47-184">Stroj zobrazení Razor byl aktualizován pro práci s novým kompilátorem Roslyn.</span><span class="sxs-lookup"><span data-stu-id="a7b47-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="a7b47-185">To zahrnuje podporu pro c# 7.1 funkce, jako jsou výchozí výrazy, odvozené názvy tuple a porovnávání vzorů s obecnými typy.</span><span class="sxs-lookup"><span data-stu-id="a7b47-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="a7b47-186">Chcete-li v projektu použít c# 7.1, přidejte do souboru projektu následující vlastnost a znovu načtěte řešení:</span><span class="sxs-lookup"><span data-stu-id="a7b47-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="a7b47-187">Informace o stavu funkcí jazyka C# 7.1 naleznete [v úložišti Roslyn GitHub](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="a7b47-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="a7b47-188">Další aktualizace dokumentace pro 2.0</span><span class="sxs-lookup"><span data-stu-id="a7b47-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="a7b47-189">Visual Studio publikuje profily pro nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7b47-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="a7b47-190">Správa klíčů</span><span class="sxs-lookup"><span data-stu-id="a7b47-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="a7b47-191">Konfigurace ověřování na Facebooku</span><span class="sxs-lookup"><span data-stu-id="a7b47-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a7b47-192">Konfigurace ověřování na Twitteru</span><span class="sxs-lookup"><span data-stu-id="a7b47-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a7b47-193">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="a7b47-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="a7b47-194">Konfigurace ověřování účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="a7b47-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="a7b47-195">Pokyny k migraci</span><span class="sxs-lookup"><span data-stu-id="a7b47-195">Migration guidance</span></span>

<span data-ttu-id="a7b47-196">Pokyny k migraci ASP.NET aplikací core 1.x do ASP.NET jádra 2.0 naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="a7b47-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="a7b47-197">Migrace z ASP.NET jádra 1.x do ASP.NET Jádra 2.0</span><span class="sxs-lookup"><span data-stu-id="a7b47-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="a7b47-198">Migrace ověřování a identity do ASP.NET jádra 2.0</span><span class="sxs-lookup"><span data-stu-id="a7b47-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="a7b47-199">Další informace</span><span class="sxs-lookup"><span data-stu-id="a7b47-199">Additional Information</span></span>

<span data-ttu-id="a7b47-200">Úplný seznam změn naleznete v [ASP.NET poznámky k verzi Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="a7b47-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="a7b47-201">Chcete-li se spojit s pokrokem a plány vývojového týmu ASP.NET Core, nalaďte se na [ASP.NET komunitního standupu](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="a7b47-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
