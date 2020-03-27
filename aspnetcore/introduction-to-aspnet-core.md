---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Nechte si představit ASP.NET Core, což je platformově univerzální, vysoce výkonná architektura typu open-source, která slouží k vytváření moderních cloudových aplikací připojených k internetu.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: fd7fa9dd70502f51222e457dd887ef668d377278
ms.sourcegitcommit: 4b166b49ec557a03f99f872dd069ca5e56faa524
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366657"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="d2a6c-103">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2a6c-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="d2a6c-104">Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="d2a6c-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="d2a6c-105">ASP.NET Core je platformově univerzální, vysoce výkonná architektura typu [open-source](https://github.com/aspnet/home), která slouží k vytváření moderních cloudových aplikací připojených k internetu.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="d2a6c-106">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="d2a6c-107">Vytvářet webové aplikace a služby, aplikace [IoT](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="d2a6c-108">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="d2a6c-109">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="d2a6c-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="d2a6c-110">Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="d2a6c-111">Proč zvolit ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="d2a6c-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="d2a6c-112">Miliony vývojářů používají nebo používali [ASP.NET 4. x](/aspnet/overview) k vytváření webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="d2a6c-113">ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="d2a6c-114">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d2a6c-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="d2a6c-115">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="d2a6c-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="d2a6c-116">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="d2a6c-117">[Razor Pages](xref:razor-pages/index) představuje stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="d2a6c-118">[Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="d2a6c-119">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="d2a6c-120">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="d2a6c-121">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="d2a6c-122">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="d2a6c-123">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="d2a6c-123">Client-side development</span></span>

<span data-ttu-id="d2a6c-124">ASP.NET Core se hladce integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index), [úhlů](xref:spa/angular), [reakce](xref:spa/react)a [zavedení](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="d2a6c-125">Další informace najdete v tématu <xref:blazor/index> a související témata týkající se *vývoje na straně klienta*.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="d2a6c-126">Cílení ASP.NET Core na .NET Framework</span><span class="sxs-lookup"><span data-stu-id="d2a6c-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="d2a6c-127">Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="d2a6c-128">Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="d2a6c-129">ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="d2a6c-130">Knihovny napsané pomocí .NET Standard 2,0 běží na libovolné [platformě .NET, která implementuje .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-130">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="d2a6c-131">ASP.NET Core 2. x se podporuje v .NET Framework verzích, které implementují .NET Standard 2,0:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-131">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="d2a6c-132">Důrazně doporučujeme .NET Framework nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-132">.NET Framework latest version is strongly recommended.</span></span>
* <span data-ttu-id="d2a6c-133">.NET Framework 4.6.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="d2a6c-134">ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="d2a6c-135">Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="d2a6c-136">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="d2a6c-137">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="d2a6c-138">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-138">Cross-platform.</span></span> <span data-ttu-id="d2a6c-139">Běží v systémech macOS, Linux a Windows.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="d2a6c-140">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="d2a6c-140">Improved performance</span></span>
* [<span data-ttu-id="d2a6c-141">Souběžná Správa verzí</span><span class="sxs-lookup"><span data-stu-id="d2a6c-141">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="d2a6c-142">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d2a6c-142">New APIs</span></span>
* <span data-ttu-id="d2a6c-143">Open source</span><span class="sxs-lookup"><span data-stu-id="d2a6c-143">Open source</span></span>

<span data-ttu-id="d2a6c-144">Ze všech sil se snažíme doplnit rozhraní API z .NET Framework do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="d2a6c-145">Sada [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) zpřístupnila v .NET Core tisíce rozhraní API, která byla určena jen pro Windows.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="d2a6c-146">Tato rozhraní API nebyla v .NET Core 1.x dostupná.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="d2a6c-147">Doporučený studijní program</span><span class="sxs-lookup"><span data-stu-id="d2a6c-147">Recommended learning path</span></span>

<span data-ttu-id="d2a6c-148">Jako úvod do vývoje aplikací ASP.NET Core doporučujeme následující posloupnost kurzů a článků:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-148">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="d2a6c-149">Postupujte podle kurzu pro typ aplikace, kterou chcete vyvíjet nebo udržovat:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-149">Follow a tutorial for the type of app you want to develop or maintain:</span></span>

   |<span data-ttu-id="d2a6c-150">Typ aplikace</span><span class="sxs-lookup"><span data-stu-id="d2a6c-150">App type</span></span>  |<span data-ttu-id="d2a6c-151">Scénář</span><span class="sxs-lookup"><span data-stu-id="d2a6c-151">Scenario</span></span>  |<span data-ttu-id="d2a6c-152">Kurz</span><span class="sxs-lookup"><span data-stu-id="d2a6c-152">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="d2a6c-153">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="d2a6c-153">Web app</span></span>                   | <span data-ttu-id="d2a6c-154">Vývoj nové aplikace</span><span class="sxs-lookup"><span data-stu-id="d2a6c-154">For new development</span></span>        |[<span data-ttu-id="d2a6c-155">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="d2a6c-155">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="d2a6c-156">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="d2a6c-156">Web app</span></span>                   | <span data-ttu-id="d2a6c-157">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="d2a6c-157">For maintaining an MVC app</span></span> |[<span data-ttu-id="d2a6c-158">Začínáme s MVC</span><span class="sxs-lookup"><span data-stu-id="d2a6c-158">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="d2a6c-159">Webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d2a6c-159">Web API</span></span>                   |                            |<span data-ttu-id="d2a6c-160">[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)\*</span><span class="sxs-lookup"><span data-stu-id="d2a6c-160">[Create a web API](xref:tutorials/first-web-api)\*</span></span>  |
   |<span data-ttu-id="d2a6c-161">Aplikace v reálném čase</span><span class="sxs-lookup"><span data-stu-id="d2a6c-161">Real-time app</span></span>             |                            |[<span data-ttu-id="d2a6c-162">Začínáme s funkcí SignalR</span><span class="sxs-lookup"><span data-stu-id="d2a6c-162">Get started with SignalR</span></span>](xref:tutorials/signalr) |
   |<span data-ttu-id="d2a6c-163">Aplikace Blazor</span><span class="sxs-lookup"><span data-stu-id="d2a6c-163">Blazor app</span></span>                |                            |[<span data-ttu-id="d2a6c-164">Začínáme s Blazor</span><span class="sxs-lookup"><span data-stu-id="d2a6c-164">Get started with Blazor</span></span>](xref:blazor/get-started) |
   |<span data-ttu-id="d2a6c-165">Aplikace vzdáleného volání procedur</span><span class="sxs-lookup"><span data-stu-id="d2a6c-165">Remote Procedure Call app</span></span> |                            |[<span data-ttu-id="d2a6c-166">Začínáme se službou gRPC</span><span class="sxs-lookup"><span data-stu-id="d2a6c-166">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |

1. <span data-ttu-id="d2a6c-167">Postupujte podle kurzu, který popisuje základní přístup k datům:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-167">Follow a tutorial that shows how to do basic data access:</span></span>

   |<span data-ttu-id="d2a6c-168">Scénář</span><span class="sxs-lookup"><span data-stu-id="d2a6c-168">Scenario</span></span>  |<span data-ttu-id="d2a6c-169">Kurz</span><span class="sxs-lookup"><span data-stu-id="d2a6c-169">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="d2a6c-170">Vývoj nové aplikace</span><span class="sxs-lookup"><span data-stu-id="d2a6c-170">For new development</span></span>        |[<span data-ttu-id="d2a6c-171">Razor Pages pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d2a6c-171">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="d2a6c-172">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="d2a6c-172">For maintaining an MVC app</span></span> |[<span data-ttu-id="d2a6c-173">MVC pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d2a6c-173">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

1. <span data-ttu-id="d2a6c-174">Podívejte se na přehled funkcí ASP.NET Core, které se vztahují na všechny typy aplikací:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-174">Read an overview of ASP.NET Core features that apply to all app types:</span></span>

   * [<span data-ttu-id="d2a6c-175">Základy</span><span class="sxs-lookup"><span data-stu-id="d2a6c-175">Fundamentals</span></span>](xref:fundamentals/index)

1. <span data-ttu-id="d2a6c-176">Projděte obsah a najděte další témata, která vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-176">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="d2a6c-177">\* K dispozici je nový [kurz webového rozhraní API, který můžete kompletně absolvovat v prohlížeči](https://docs.microsoft.com/learn/modules/build-web-api-net-core) bez nutnosti instalace integrovaného vývojového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-177">\* There is a new [web API tutorial that you follow entirely in the browser](https://docs.microsoft.com/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span>  <span data-ttu-id="d2a6c-178">Kód běží v prostředí [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) a k testování se používá [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-178">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migration-from-the-net-framework"></a><span data-ttu-id="d2a6c-179">Migrace z .NET Framework</span><span class="sxs-lookup"><span data-stu-id="d2a6c-179">Migration from the .NET Framework</span></span>

<span data-ttu-id="d2a6c-180">Referenční příručka pro migraci aplikací ASP.NET na ASP.NET Core najdete v tématu <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-180">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="d2a6c-181">Jak si stáhnout ukázku</span><span class="sxs-lookup"><span data-stu-id="d2a6c-181">How to download a sample</span></span>

<span data-ttu-id="d2a6c-182">Řada článků a kurzů obsahuje odkazy na vzorový kód.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-182">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="d2a6c-183">[Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-183">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="d2a6c-184">Rozbalte soubor *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-184">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="d2a6c-185">Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-185">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="d2a6c-186">Direktivy preprocesoru ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="d2a6c-186">Preprocessor directives in sample code</span></span>

<span data-ttu-id="d2a6c-187">Aby bylo možné předvést více scénářů, ukázkové aplikace používají direktivy preprocesoru `#define` a `#if-#else/#elif-#endif` k selektivní kompilaci a spouštění různých sekcí vzorového kódu.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-187">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="d2a6c-188">U vzorků, které využívají tento přístup, nastavte direktivu `#define` v horní části C# souborů k definování symbolu přidruženého ke scénáři, který chcete spustit.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-188">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="d2a6c-189">Některé ukázky vyžadují definování symbolu v horní části více souborů, aby bylo možné spustit scénář.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-189">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="d2a6c-190">Například následující seznam symbolů `#define` udává, že jsou dostupné čtyři scénáře (jeden scénář na symbol).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-190">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="d2a6c-191">Při aktuální konfiguraci ukázky se spustí scénář `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-191">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="d2a6c-192">Pokud chcete ukázku změnit tak, aby se spustil scénář `ExpandDefault`, definujte symbol `ExpandDefault` a zbývající symboly nechejte zakomentované:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-192">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="d2a6c-193">Další informace o používání [direktiv preprocesoru C#](/dotnet/csharp/language-reference/preprocessor-directives/) k selektivní kompilaci částí kódu najdete v článku [#define (referenční dokumentace jazyka C# )](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) a [#if (referenční dokumentace jazyka C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-193">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="d2a6c-194">Oblasti ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="d2a6c-194">Regions in sample code</span></span>

<span data-ttu-id="d2a6c-195">Některé ukázkové aplikace obsahují oddíly kódu, které jsou obklopené direktivami [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) a [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# .</span><span class="sxs-lookup"><span data-stu-id="d2a6c-195">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="d2a6c-196">Systém pro sestavování dokumentace vkládá tyto oblasti do zobrazených témat dokumentace.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-196">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="d2a6c-197">Názvy oblastí obvykle obsahují slovo „snippet“.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-197">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="d2a6c-198">Následující příklad ukazuje oblast s názvem `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-198">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="d2a6c-199">Na předchozí fragment kódu C# odkazuje soubor Markdown tohoto tématu pomocí následujícího řádku:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-199">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="d2a6c-200">Můžete bezpečně ignorovat (nebo odebrat) direktivy `#region` a `#endregion`, které obklopují kód.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-200">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="d2a6c-201">Pokud plánujete spustit ukázkové scénáře popsané v tématu, neměňte kód v těchto direktivách.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-201">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="d2a6c-202">Při experimentování s jinými scénáři můžete tento kód klidně pozměnit.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-202">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="d2a6c-203">Další informace najdete v článku [Příspěvky k dokumentaci rozhraní ASP.NET: fragmenty kódu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="d2a6c-203">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d2a6c-204">Další kroky</span><span class="sxs-lookup"><span data-stu-id="d2a6c-204">Next steps</span></span>

<span data-ttu-id="d2a6c-205">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="d2a6c-205">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="d2a6c-206">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2a6c-206">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="d2a6c-207">[Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-207">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="d2a6c-208">Nabízí nové blogové příspěvky a software třetích stran.</span><span class="sxs-lookup"><span data-stu-id="d2a6c-208">It features new blogs and third-party software.</span></span>