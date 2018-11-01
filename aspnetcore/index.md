---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Nechte si představit ASP.NET Core, což je platformově univerzální, vysoce výkonná architektura typu open-source, která slouží k vytváření moderních cloudových aplikací připojených k internetu.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: index
ms.openlocfilehash: 37448b1b3d0da4e3cb34b1cd51f663b7e53ddced
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50207391"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="21885-103">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21885-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="21885-104">Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="21885-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="21885-105">ASP.NET Core je platformově univerzální, vysoce výkonná architektura typu [open-source](https://github.com/aspnet/home), která slouží k vytváření moderních cloudových aplikací připojených k internetu.</span><span class="sxs-lookup"><span data-stu-id="21885-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="21885-106">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="21885-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="21885-107">Vytvářet webové aplikace a služby, aplikace [IoT](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.</span><span class="sxs-lookup"><span data-stu-id="21885-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="21885-108">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="21885-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="21885-109">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="21885-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="21885-110">Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="21885-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-use-aspnet-core"></a><span data-ttu-id="21885-111">Proč používat ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="21885-111">Why use ASP.NET Core?</span></span>

<span data-ttu-id="21885-112">Miliony vývojářů už dříve k vytváření webových aplikací používali [ASP.NET 4.x](/aspnet/overview) (což bude platit i nadále).</span><span class="sxs-lookup"><span data-stu-id="21885-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="21885-113">ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.</span><span class="sxs-lookup"><span data-stu-id="21885-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="21885-114">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="21885-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="21885-115">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/index#build-web-apis) a [webové aplikace](xref:tutorials/index#build-web-apps):</span><span class="sxs-lookup"><span data-stu-id="21885-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/index#build-web-apis) and [web apps](xref:tutorials/index#build-web-apps):</span></span>

* <span data-ttu-id="21885-116">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly [testovatelné](xref:test/index).</span><span class="sxs-lookup"><span data-stu-id="21885-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps [testable](xref:test/index).</span></span>
* <span data-ttu-id="21885-117">[Stránky Razor](xref:razor-pages/index) (nově ve verzi ASP.NET Core 2.0) představují stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="21885-117">[Razor Pages](xref:razor-pages/index) (new in ASP.NET Core 2.0) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="21885-118">[Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="21885-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="21885-119">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="21885-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="21885-120">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="21885-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="21885-121">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="21885-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="21885-122">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="21885-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="21885-123">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="21885-123">Client-side development</span></span>

<span data-ttu-id="21885-124">Architektura ASP.NET Core se hladce integruje do oblíbených klientských rozhraní a knihoven, včetně [Angular](xref:spa/angular), [React](xref:spa/react) a [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="21885-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="21885-125">Další informace najdete v článku [Vývoj na straně klienta](xref:client-side/index).</span><span class="sxs-lookup"><span data-stu-id="21885-125">For more information, see [Client-side development](xref:client-side/index).</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="21885-126">Cílení ASP.NET Core na .NET Framework</span><span class="sxs-lookup"><span data-stu-id="21885-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="21885-127">Cílem ASP.NET Core může být .NET Core nebo .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="21885-127">ASP.NET Core can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="21885-128">Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows.</span><span class="sxs-lookup"><span data-stu-id="21885-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="21885-129">V ASP.NET Core neplánujeme odebrat podporu pro cílení na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="21885-129">There are no plans to remove support for targeting .NET Framework in ASP.NET Core.</span></span> <span data-ttu-id="21885-130">ASP.NET Core se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="21885-130">Generally, ASP.NET Core is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="21885-131">Aplikace vytvořené pomocí .NET Standard 2.0 běží všude, kde se podporuje .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="21885-131">Apps written with .NET Standard 2.0 run anywhere that .NET Standard 2.0 is supported.</span></span>

<span data-ttu-id="21885-132">ASP.NET Core 2.x se podporuje ve verzích .NET Framework kompatibilních s .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="21885-132">ASP.NET Core 2.x is supported on .NET Framework versions compatible with .NET Standard 2.0:</span></span>

* <span data-ttu-id="21885-133">Důrazně se doporučuje .NET Framework 4.7.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="21885-133">.NET Framework 4.7.1 and later is strongly recommended.</span></span>
* <span data-ttu-id="21885-134">.NET Framework 4.6.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="21885-134">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="21885-135">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="21885-135">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="21885-136">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="21885-136">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="21885-137">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="21885-137">Cross-platform.</span></span> <span data-ttu-id="21885-138">Běží v systémech macOS, Linux a Windows.</span><span class="sxs-lookup"><span data-stu-id="21885-138">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="21885-139">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="21885-139">Improved performance</span></span>
* <span data-ttu-id="21885-140">Souběžná správa verzí</span><span class="sxs-lookup"><span data-stu-id="21885-140">Side-by-side versioning</span></span>
* <span data-ttu-id="21885-141">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="21885-141">New APIs</span></span>
* <span data-ttu-id="21885-142">Open source</span><span class="sxs-lookup"><span data-stu-id="21885-142">Open source</span></span>

<span data-ttu-id="21885-143">Ze všech sil se snažíme doplnit rozhraní API z .NET Framework do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="21885-143">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="21885-144">Sada [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) zpřístupnila v .NET Core tisíce rozhraní API, která byla určena jen pro Windows.</span><span class="sxs-lookup"><span data-stu-id="21885-144">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="21885-145">Tato rozhraní API nebyla v .NET Core 1.x dostupná.</span><span class="sxs-lookup"><span data-stu-id="21885-145">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="21885-146">Jak si stáhnout ukázku</span><span class="sxs-lookup"><span data-stu-id="21885-146">How to download a sample</span></span>

<span data-ttu-id="21885-147">Řada článků a kurzů obsahuje odkazy na vzorový kód.</span><span class="sxs-lookup"><span data-stu-id="21885-147">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="21885-148">[Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/aspnet/Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="21885-148">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/Docs/zip/master).</span></span>
1. <span data-ttu-id="21885-149">Rozbalte soubor *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="21885-149">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="21885-150">Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.</span><span class="sxs-lookup"><span data-stu-id="21885-150">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

## <a name="next-steps"></a><span data-ttu-id="21885-151">Další kroky</span><span class="sxs-lookup"><span data-stu-id="21885-151">Next steps</span></span>

<span data-ttu-id="21885-152">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="21885-152">For more information, see the following resources:</span></span>

* [<span data-ttu-id="21885-153">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="21885-153">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="21885-154">Kurzy k ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21885-154">ASP.NET Core tutorials</span></span>](xref:tutorials/index)
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="21885-155">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21885-155">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="21885-156">[Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány.</span><span class="sxs-lookup"><span data-stu-id="21885-156">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="21885-157">Nabízí nové blogové příspěvky a software třetích stran.</span><span class="sxs-lookup"><span data-stu-id="21885-157">It features new blogs and third-party software.</span></span>
