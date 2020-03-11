---
title: Co je nového v ASP.NET Core 2,0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 2,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667256"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="c0bd6-103">Co je nového v ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="c0bd6-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="c0bd6-104">Tento článek zvýrazňuje nejvýznamnější změny v ASP.NET Core 2,0 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="c0bd6-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c0bd6-105">Razor Pages</span></span>

<span data-ttu-id="c0bd6-106">Razor Pages je nová funkce ASP.NET Core MVC, která usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="c0bd6-107">Další informace najdete v úvodu a v kurzu:</span><span class="sxs-lookup"><span data-stu-id="c0bd6-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="c0bd6-108">Úvod do Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c0bd6-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="c0bd6-109">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="c0bd6-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="c0bd6-110">ASP.NET Core Metapackage</span><span class="sxs-lookup"><span data-stu-id="c0bd6-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="c0bd6-111">Nový ASP.NET Core Metapackage zahrnuje všechny balíčky vytvořené a podporované ASP.NET Core a Entity Framework Core týmy a jejich interní závislosti a závislosti třetích stran.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="c0bd6-112">Už nemusíte volit jednotlivé funkce ASP.NET Core podle balíčku.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="c0bd6-113">Všechny funkce jsou součástí balíčku [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) .</span><span class="sxs-lookup"><span data-stu-id="c0bd6-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="c0bd6-114">Výchozí šablony používají tento balíček.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-114">The default templates use this package.</span></span>

<span data-ttu-id="c0bd6-115">Další informace najdete v tématu [Microsoft. AspNetCore. All Metapackage for ASP.NET Core 2,0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="c0bd6-116">Běhové úložiště</span><span class="sxs-lookup"><span data-stu-id="c0bd6-116">Runtime Store</span></span>

<span data-ttu-id="c0bd6-117">Aplikace, které používají `Microsoft.AspNetCore.All` Metapackage, automaticky využívají nové úložiště runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="c0bd6-118">Úložiště obsahuje všechny prostředky modulu runtime potřebné ke spouštění aplikací ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="c0bd6-119">Při použití `Microsoft.AspNetCore.All` Metapackage se s aplikací nasazují žádné prostředky z odkazovaných balíčků NuGet ASP.NET Core, protože už jsou umístěné v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="c0bd6-120">Prostředky v úložišti za běhu jsou také předem kompilovány pro zlepšení času spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="c0bd6-121">Další informace najdete v tématu [běhové úložiště](/dotnet/core/deploying/runtime-store) .</span><span class="sxs-lookup"><span data-stu-id="c0bd6-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="c0bd6-122">.NET Standard 2,0</span><span class="sxs-lookup"><span data-stu-id="c0bd6-122">.NET Standard 2.0</span></span>

<span data-ttu-id="c0bd6-123">Cílová ASP.NET Core 2,0 balíčků .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="c0bd6-124">Na balíčky můžou odkazovat jiné knihovny .NET Standard 2,0 a můžou běžet na .NET Standard 2,0 implementace platformy .NET, včetně .NET Core 2,0 a .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="c0bd6-125">`Microsoft.AspNetCore.All` Metapackage cílí pouze na rozhraní .NET Core 2,0, protože je určeno pro použití s úložištěm runtime .NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="c0bd6-126">Aktualizace konfigurace</span><span class="sxs-lookup"><span data-stu-id="c0bd6-126">Configuration update</span></span>

<span data-ttu-id="c0bd6-127">Do kontejneru Services je ve výchozím nastavení přidána instance `IConfiguration` v ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="c0bd6-128">`IConfiguration` v kontejneru Services usnadňuje aplikacím načítání konfiguračních hodnot z kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="c0bd6-129">Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="c0bd6-130">Aktualizace protokolování</span><span class="sxs-lookup"><span data-stu-id="c0bd6-130">Logging update</span></span>

<span data-ttu-id="c0bd6-131">V ASP.NET Core 2,0 je ve výchozím nastavení do systému vkládání závislostí začleněno protokolování (DI).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="c0bd6-132">Přidáte poskytovatele a nakonfigurujete filtrování v souboru *program.cs* místo v souboru *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="c0bd6-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="c0bd6-133">Výchozí `ILoggerFactory` podporuje filtrování způsobem, který umožňuje použít jeden flexibilní přístup pro filtrování křížového zprostředkovatele i pro filtrování specifického zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="c0bd6-134">Další informace najdete v tématu [Úvod do protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="c0bd6-135">Aktualizace ověřování</span><span class="sxs-lookup"><span data-stu-id="c0bd6-135">Authentication update</span></span>

<span data-ttu-id="c0bd6-136">Nový model ověřování usnadňuje konfiguraci ověřování pro aplikaci pomocí typu DI.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="c0bd6-137">Nové šablony jsou k dispozici pro konfiguraci ověřování pro webové aplikace a webová rozhraní API pomocí [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="c0bd6-138">Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="c0bd6-139">Aktualizace identity</span><span class="sxs-lookup"><span data-stu-id="c0bd6-139">Identity update</span></span>

<span data-ttu-id="c0bd6-140">Usnadnili jsme vytváření zabezpečených webových rozhraní API pomocí identity v ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="c0bd6-141">Přístupové tokeny pro přístup k webovým rozhraním API můžete získat pomocí [knihovny Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="c0bd6-142">Další informace o změnách ověřování v 2,0 najdete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="c0bd6-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="c0bd6-143">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0bd6-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="c0bd6-144">Povolit generování kódu QR pro ověřovací aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0bd6-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="c0bd6-145">Migrace ověřování a identity na ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="c0bd6-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="c0bd6-146">Šablony SPA</span><span class="sxs-lookup"><span data-stu-id="c0bd6-146">SPA templates</span></span>

<span data-ttu-id="c0bd6-147">K dispozici jsou šablony projektů s jednou stránkou (SPA) pro úhlové, Aurelia, vyseknutí. js, reagují. js a reagují. js s Redux.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="c0bd6-148">Úhlová šablona byla aktualizována na úhlové 4.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="c0bd6-149">Šablony úhlů a reakce jsou k dispozici ve výchozím nastavení. informace o tom, jak získat další šablony, najdete v tématu [Vytvoření nového projektu Spa](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="c0bd6-150">Informace o tom, jak vytvořit SPA v ASP.NET Core, najdete v článku <xref:client-side/spa-services>.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="c0bd6-151">Vylepšení Kestrel</span><span class="sxs-lookup"><span data-stu-id="c0bd6-151">Kestrel improvements</span></span>

<span data-ttu-id="c0bd6-152">Webový server Kestrel má nové funkce, díky kterým je vhodnější jako internetový server.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="c0bd6-153">Do vlastnosti New `Limits` třídy `KestrelServerOptions` se přidalo několik možností konfigurace omezení serveru.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="c0bd6-154">Přidejte omezení pro následující:</span><span class="sxs-lookup"><span data-stu-id="c0bd6-154">Add limits for the following:</span></span>

* <span data-ttu-id="c0bd6-155">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="c0bd6-155">Maximum client connections</span></span>
* <span data-ttu-id="c0bd6-156">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="c0bd6-156">Maximum request body size</span></span>
* <span data-ttu-id="c0bd6-157">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="c0bd6-157">Minimum request body data rate</span></span>

<span data-ttu-id="c0bd6-158">Další informace najdete v tématu [implementace webového serveru Kestrel v ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="c0bd6-159">Weblisten byl přejmenován na HTTP. sys</span><span class="sxs-lookup"><span data-stu-id="c0bd6-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="c0bd6-160">Balíčky `Microsoft.AspNetCore.Server.WebListener` a `Microsoft.Net.Http.Server` byly sloučeny do nového balíčku `Microsoft.AspNetCore.Server.HttpSys`.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="c0bd6-161">Obory názvů byly aktualizovány tak, aby odpovídaly.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="c0bd6-162">Další informace najdete v tématu [implementace webového serveru http. sys v ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="c0bd6-163">Rozšířená podpora hlaviček protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="c0bd6-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="c0bd6-164">Při použití MVC k přenosu `FileStreamResult` nebo `FileContentResult`teď máte možnost nastavit `ETag` nebo `LastModified` datum na přenášeném obsahu.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="c0bd6-165">Můžete nastavit tyto hodnoty vráceného obsahu s podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c0bd6-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="c0bd6-166">Soubor vrácený návštěvníkům má vhodné hlavičky protokolu HTTP pro `ETag` a `LastModified` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="c0bd6-167">Pokud návštěvník aplikace požaduje obsah s hlavičkou požadavku na rozsah, ASP.NET Core rozpoznává požadavek a zpracuje hlavičku.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="c0bd6-168">Pokud lze požadovaný obsah částečně doručovat, ASP.NET Core odpovídajícím způsobem přeskočí a vrátí pouze požadovanou sadu bajtů.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="c0bd6-169">Nemusíte psát žádné speciální obslužné rutiny do metod pro přizpůsobení nebo zpracování této funkce; automaticky se za vás zpracuje.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="c0bd6-170">Hostování spuštění a Application Insights</span><span class="sxs-lookup"><span data-stu-id="c0bd6-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="c0bd6-171">Hostující prostředí teď můžou vkládat dodatečné závislosti balíčků a spouštět kód při spuštění aplikace, aniž by aplikace musela explicitně převzít závislost nebo volat jakékoli metody.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="c0bd6-172">Tato funkce se dá použít k tomu, aby určitá prostředí mohla být pro toto prostředí jedinečná, aniž by aplikace musela znát čas dopředu.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="c0bd6-173">V ASP.NET Core 2,0 se tato funkce používá k automatickému povolení Application Insights diagnostiky při ladění v aplikaci Visual Studio a při spuštění v Azure App Services.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="c0bd6-174">V důsledku toho šablony projektu již ve výchozím nastavení nepřidává Application Insights balíčky a kód.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="c0bd6-175">Informace o stavu plánované dokumentace najdete v tématu [problém GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="c0bd6-176">Automatické použití odolných tokenů proti padělání</span><span class="sxs-lookup"><span data-stu-id="c0bd6-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="c0bd6-177">ASP.NET Core ve výchozím nastavení vždycky pomohl obsah s kódováním HTML, ale s novou verzí je potřeba další krok, který pomáhá zabránit útokům na více lokalitám proti falšování.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="c0bd6-178">Služba ASP.NET Core nyní ve výchozím nastavení generuje tokeny proti padělání a ověří je v akcích a stránkách vydaných formulářem bez dodatečné konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="c0bd6-179">Další informace najdete v tématu [prevence útoků proti falšování (XSRF/CSRF) žádostí mezi weby](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="c0bd6-180">Automatická předkompilace</span><span class="sxs-lookup"><span data-stu-id="c0bd6-180">Automatic precompilation</span></span>

<span data-ttu-id="c0bd6-181">Předkompilace zobrazení Razor je ve výchozím nastavení povolená, takže se zmenší velikost výstupu publikování a čas spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="c0bd6-182">Další informace najdete v tématu [kompilace zobrazení Razor a předkompilace v ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="c0bd6-183">Podpora Razor pro C# 7,1</span><span class="sxs-lookup"><span data-stu-id="c0bd6-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="c0bd6-184">Modul zobrazení Razor byl aktualizován tak, aby fungoval s novým kompilátorem Roslyn.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="c0bd6-185">Který zahrnuje podporu pro C# funkce 7,1, jako jsou výchozí výrazy, odvoditelné názvy řazené kolekce členů a porovnávání vzorů s obecnými typy.</span><span class="sxs-lookup"><span data-stu-id="c0bd6-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="c0bd6-186">Pokud chcete C# v projektu použít 7,1, přidejte do souboru projektu následující vlastnost a pak znovu načtěte řešení:</span><span class="sxs-lookup"><span data-stu-id="c0bd6-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="c0bd6-187">Informace o stavu funkcí C# 7,1 najdete [v úložišti GitHub Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="c0bd6-188">Další aktualizace dokumentace pro 2,0</span><span class="sxs-lookup"><span data-stu-id="c0bd6-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="c0bd6-189">Publikační profily sady Visual Studio pro nasazení aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0bd6-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="c0bd6-190">Správa klíčů</span><span class="sxs-lookup"><span data-stu-id="c0bd6-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="c0bd6-191">Konfigurace ověřování na Facebooku</span><span class="sxs-lookup"><span data-stu-id="c0bd6-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c0bd6-192">Konfigurace ověřování na Twitteru</span><span class="sxs-lookup"><span data-stu-id="c0bd6-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c0bd6-193">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="c0bd6-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c0bd6-194">Konfigurace ověřování účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="c0bd6-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="c0bd6-195">Pokyny k migraci</span><span class="sxs-lookup"><span data-stu-id="c0bd6-195">Migration guidance</span></span>

<span data-ttu-id="c0bd6-196">Pokyny k migraci ASP.NET Corech aplikací 1. x na ASP.NET Core 2,0 naleznete v následujících zdrojích informací:</span><span class="sxs-lookup"><span data-stu-id="c0bd6-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="c0bd6-197">Migrace z ASP.NET Core 1. x na ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="c0bd6-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="c0bd6-198">Migrace ověřování a identity na ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="c0bd6-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="c0bd6-199">Další informace</span><span class="sxs-lookup"><span data-stu-id="c0bd6-199">Additional Information</span></span>

<span data-ttu-id="c0bd6-200">Úplný seznam změn najdete v [poznámkách k verzi pro ASP.NET Core 2,0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="c0bd6-201">Pokud se chcete připojit k vývoji a plánům vývojového týmu ASP.NET Core, prolaďte si [rychlou schůzku komunitu ASP.NET](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="c0bd6-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
