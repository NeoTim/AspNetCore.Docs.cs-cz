---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Získejte úvod do ASP.NET Core, vysoce výkonného open source frameworku s více platformami pro vytváření moderních aplikací připojených k internetu s podporou cloudu.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615940"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="e49aa-103">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e49aa-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="e49aa-104">Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="e49aa-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e49aa-105">ASP.NET Core je multiplatformní, vysoce výkonný open [source](https://github.com/dotnet/aspnetcore) framework pro vytváření moderních aplikací připojených k internetu s podporou cloudu.</span><span class="sxs-lookup"><span data-stu-id="e49aa-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="e49aa-106">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="e49aa-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="e49aa-107">Vytvářejte webové aplikace a služby, aplikace [Internetu věcí (IoT)](https://www.microsoft.com/internet-of-things/) a mobilní backendy.</span><span class="sxs-lookup"><span data-stu-id="e49aa-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="e49aa-108">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="e49aa-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="e49aa-109">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="e49aa-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="e49aa-110">Spustit na [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="e49aa-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="e49aa-111">Proč si vybrat ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="e49aa-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="e49aa-112">Miliony vývojářů používají nebo používají [ASP.NET 4.x](/aspnet/overview) k vytváření webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="e49aa-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="e49aa-113">ASP.NET Core je redesign ASP.NET 4.x, včetně architektonických změn, které vedou k štíhlejšímu a modulatičtějšímu rámci.</span><span class="sxs-lookup"><span data-stu-id="e49aa-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="e49aa-114">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="e49aa-115">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="e49aa-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="e49aa-116">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.</span><span class="sxs-lookup"><span data-stu-id="e49aa-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="e49aa-117">[Razor Pages](xref:razor-pages/index) představuje stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="e49aa-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="e49aa-118">[Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="e49aa-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="e49aa-119">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="e49aa-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="e49aa-120">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="e49aa-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="e49aa-121">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="e49aa-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="e49aa-122">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="e49aa-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="e49aa-123">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="e49aa-123">Client-side development</span></span>

<span data-ttu-id="e49aa-124">ASP.NET Core se bezproblémově integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react)a [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="e49aa-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="e49aa-125">Další informace naleznete <xref:blazor/index> v tématu a souvisejících tématech v části *Vývoj na straně klienta*.</span><span class="sxs-lookup"><span data-stu-id="e49aa-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="e49aa-126">ASP.NET hlavní cílové rámce</span><span class="sxs-lookup"><span data-stu-id="e49aa-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="e49aa-127">ASP.NET Core 3.x a novější může cílit pouze na .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e49aa-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="e49aa-128">Obecně platí, že ASP.NET Core se skládá z knihoven [.NET Standard.](/dotnet/standard/net-standard)</span><span class="sxs-lookup"><span data-stu-id="e49aa-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="e49aa-129">Knihovny napsané pomocí rozhraní .NET Standard 2.0 jsou spuštěny na libovolné [platformě .NET, která implementuje rozhraní .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="e49aa-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="e49aa-130">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="e49aa-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="e49aa-131">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="e49aa-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="e49aa-132">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="e49aa-132">Cross-platform.</span></span> <span data-ttu-id="e49aa-133">Běží na Windows, macOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="e49aa-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="e49aa-134">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="e49aa-134">Improved performance</span></span>
* [<span data-ttu-id="e49aa-135">Souběžná správa verzí</span><span class="sxs-lookup"><span data-stu-id="e49aa-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="e49aa-136">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e49aa-136">New APIs</span></span>
* <span data-ttu-id="e49aa-137">Open source</span><span class="sxs-lookup"><span data-stu-id="e49aa-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="e49aa-138">Doporučený studijní program</span><span class="sxs-lookup"><span data-stu-id="e49aa-138">Recommended learning path</span></span>

<span data-ttu-id="e49aa-139">Doporučujeme následující posloupnost kurzů pro úvod do vývoje aplikací ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e49aa-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="e49aa-140">Postupujte podle kurzu pro typ aplikace, který chcete vyvíjet nebo udržovat.</span><span class="sxs-lookup"><span data-stu-id="e49aa-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="e49aa-141">Typ aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-141">App type</span></span>  |<span data-ttu-id="e49aa-142">Scénář</span><span class="sxs-lookup"><span data-stu-id="e49aa-142">Scenario</span></span>  |<span data-ttu-id="e49aa-143">Kurz</span><span class="sxs-lookup"><span data-stu-id="e49aa-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="e49aa-144">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-144">Web app</span></span>                   | <span data-ttu-id="e49aa-145">Vývoj nového webového uživatelského uživatelského uživatelského nastavení na straně serveru</span><span class="sxs-lookup"><span data-stu-id="e49aa-145">New server-side web UI development</span></span> |[<span data-ttu-id="e49aa-146">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="e49aa-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="e49aa-147">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-147">Web app</span></span>                   | <span data-ttu-id="e49aa-148">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="e49aa-149">Začínáme s MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="e49aa-150">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-150">Web app</span></span>                   | <span data-ttu-id="e49aa-151">Vývoj webového uživatelského uživatelského uživatelského uživatelského nastavení na straně klienta</span><span class="sxs-lookup"><span data-stu-id="e49aa-151">Client-side web UI development</span></span> |[<span data-ttu-id="e49aa-152">Začínáme s Blazorem</span><span class="sxs-lookup"><span data-stu-id="e49aa-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="e49aa-153">Web API</span><span class="sxs-lookup"><span data-stu-id="e49aa-153">Web API</span></span>                   | <span data-ttu-id="e49aa-154">Služby HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="e49aa-154">RESTful HTTP services</span></span> |<span data-ttu-id="e49aa-155">[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="e49aa-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="e49aa-156">Aplikace Vzdálené volání procedur</span><span class="sxs-lookup"><span data-stu-id="e49aa-156">Remote Procedure Call app</span></span> | <span data-ttu-id="e49aa-157">Služby první smlouvy používající vyrovnávací paměti protokolu</span><span class="sxs-lookup"><span data-stu-id="e49aa-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="e49aa-158">Začínáme se službou gRPC</span><span class="sxs-lookup"><span data-stu-id="e49aa-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="e49aa-159">Aplikace v reálném čase</span><span class="sxs-lookup"><span data-stu-id="e49aa-159">Real-time app</span></span>             | <span data-ttu-id="e49aa-160">Obousměrná komunikace mezi servery a připojenými klienty</span><span class="sxs-lookup"><span data-stu-id="e49aa-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="e49aa-161">Začínáme s funkcí SignalR</span><span class="sxs-lookup"><span data-stu-id="e49aa-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="e49aa-162">Postupujte podle kurzu, který ukazuje, jak provést základní přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="e49aa-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="e49aa-163">Scénář</span><span class="sxs-lookup"><span data-stu-id="e49aa-163">Scenario</span></span>  |<span data-ttu-id="e49aa-164">Kurz</span><span class="sxs-lookup"><span data-stu-id="e49aa-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="e49aa-165">Nový vývoj</span><span class="sxs-lookup"><span data-stu-id="e49aa-165">New development</span></span>        |[<span data-ttu-id="e49aa-166">Razor Pages pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e49aa-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="e49aa-167">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="e49aa-168">MVC pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e49aa-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="e49aa-169">Přečtěte si přehled základních ASP.NET základních [principů,](xref:fundamentals/index) které platí pro všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="e49aa-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="e49aa-170">Další zajímavá témata naleznete v obsahu.</span><span class="sxs-lookup"><span data-stu-id="e49aa-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="e49aa-171">&dagger;K dispozici je také [interaktivní web API tutorial](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="e49aa-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="e49aa-172">Není nutná žádná místní instalace vývojových nástrojů.</span><span class="sxs-lookup"><span data-stu-id="e49aa-172">No local installation of development tools is required.</span></span> <span data-ttu-id="e49aa-173">Kód běží v [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) ve vašem prohlížeči a [curl](https://curl.haxx.se/) se používá pro testování.</span><span class="sxs-lookup"><span data-stu-id="e49aa-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="e49aa-174">Migrace z rozhraní .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e49aa-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="e49aa-175">Referenční příručku pro migraci aplikací ASP.NET 4.x <xref:migration/proper-to-2x/index>do ASP.NET Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e49aa-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e49aa-176">ASP.NET Core je multiplatformní, vysoce výkonný open [source](https://github.com/dotnet/aspnetcore) framework pro vytváření moderních aplikací připojených k internetu s podporou cloudu.</span><span class="sxs-lookup"><span data-stu-id="e49aa-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="e49aa-177">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="e49aa-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="e49aa-178">Vytvářejte webové aplikace a služby, aplikace [Internetu věcí (IoT)](https://www.microsoft.com/internet-of-things/) a mobilní backendy.</span><span class="sxs-lookup"><span data-stu-id="e49aa-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="e49aa-179">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="e49aa-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="e49aa-180">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="e49aa-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="e49aa-181">Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="e49aa-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="e49aa-182">Proč si vybrat ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="e49aa-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="e49aa-183">Miliony vývojářů používají nebo používají [ASP.NET 4.x](/aspnet/overview) k vytváření webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="e49aa-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="e49aa-184">ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.</span><span class="sxs-lookup"><span data-stu-id="e49aa-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="e49aa-185">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="e49aa-186">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="e49aa-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="e49aa-187">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.</span><span class="sxs-lookup"><span data-stu-id="e49aa-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="e49aa-188">[Razor Pages](xref:razor-pages/index) představuje stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="e49aa-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="e49aa-189">[Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="e49aa-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="e49aa-190">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="e49aa-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="e49aa-191">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="e49aa-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="e49aa-192">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="e49aa-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="e49aa-193">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="e49aa-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="e49aa-194">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="e49aa-194">Client-side development</span></span>

<span data-ttu-id="e49aa-195">ASP.NET Core se bezproblémově integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react)a [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="e49aa-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="e49aa-196">Další informace naleznete <xref:blazor/index> v tématu a souvisejících tématech v části *Vývoj na straně klienta*.</span><span class="sxs-lookup"><span data-stu-id="e49aa-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="e49aa-197">Cílení ASP.NET Core na .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e49aa-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="e49aa-198">Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="e49aa-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="e49aa-199">Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows.</span><span class="sxs-lookup"><span data-stu-id="e49aa-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="e49aa-200">ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="e49aa-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="e49aa-201">Knihovny napsané pomocí rozhraní .NET Standard 2.0 jsou spuštěny na libovolné [platformě .NET, která implementuje rozhraní .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="e49aa-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="e49aa-202">ASP.NET Core 2.x je podporován ve verzích rozhraní .NET Framework, které implementují rozhraní .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="e49aa-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="e49aa-203">Doporučujeme nejnovější verzi rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="e49aa-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="e49aa-204">.NET Framework 4.6.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="e49aa-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="e49aa-205">ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e49aa-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="e49aa-206">Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="e49aa-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="e49aa-207">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="e49aa-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="e49aa-208">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="e49aa-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="e49aa-209">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="e49aa-209">Cross-platform.</span></span> <span data-ttu-id="e49aa-210">Běží v systémech macOS, Linux a Windows.</span><span class="sxs-lookup"><span data-stu-id="e49aa-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="e49aa-211">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="e49aa-211">Improved performance</span></span>
* [<span data-ttu-id="e49aa-212">Souběžná správa verzí</span><span class="sxs-lookup"><span data-stu-id="e49aa-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="e49aa-213">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e49aa-213">New APIs</span></span>
* <span data-ttu-id="e49aa-214">Open source</span><span class="sxs-lookup"><span data-stu-id="e49aa-214">Open source</span></span>

<span data-ttu-id="e49aa-215">Ze všech sil se snažíme doplnit rozhraní API z .NET Framework do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e49aa-215">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="e49aa-216">Sada [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) zpřístupnila v .NET Core tisíce rozhraní API, která byla určena jen pro Windows.</span><span class="sxs-lookup"><span data-stu-id="e49aa-216">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="e49aa-217">Tato rozhraní API nebyla v .NET Core 1.x dostupná.</span><span class="sxs-lookup"><span data-stu-id="e49aa-217">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="e49aa-218">Doporučený studijní program</span><span class="sxs-lookup"><span data-stu-id="e49aa-218">Recommended learning path</span></span>

<span data-ttu-id="e49aa-219">Jako úvod do vývoje aplikací ASP.NET Core doporučujeme následující posloupnost kurzů a článků:</span><span class="sxs-lookup"><span data-stu-id="e49aa-219">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="e49aa-220">Postupujte podle kurzu pro typ aplikace, kterou chcete vyvíjet nebo udržovat.</span><span class="sxs-lookup"><span data-stu-id="e49aa-220">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="e49aa-221">Typ aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-221">App type</span></span>  |<span data-ttu-id="e49aa-222">Scénář</span><span class="sxs-lookup"><span data-stu-id="e49aa-222">Scenario</span></span>  |<span data-ttu-id="e49aa-223">Kurz</span><span class="sxs-lookup"><span data-stu-id="e49aa-223">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="e49aa-224">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-224">Web app</span></span>                   | <span data-ttu-id="e49aa-225">Vývoj nové aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-225">For new development</span></span>        |[<span data-ttu-id="e49aa-226">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="e49aa-226">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="e49aa-227">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-227">Web app</span></span>                   | <span data-ttu-id="e49aa-228">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-228">For maintaining an MVC app</span></span> |[<span data-ttu-id="e49aa-229">Začínáme s MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-229">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="e49aa-230">Web API</span><span class="sxs-lookup"><span data-stu-id="e49aa-230">Web API</span></span>                   |                            |<span data-ttu-id="e49aa-231">[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="e49aa-231">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="e49aa-232">Aplikace v reálném čase</span><span class="sxs-lookup"><span data-stu-id="e49aa-232">Real-time app</span></span>             |                            |[<span data-ttu-id="e49aa-233">Začínáme s funkcí SignalR</span><span class="sxs-lookup"><span data-stu-id="e49aa-233">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="e49aa-234">Postupujte podle kurzu, který ukazuje, jak provést základní přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="e49aa-234">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="e49aa-235">Scénář</span><span class="sxs-lookup"><span data-stu-id="e49aa-235">Scenario</span></span>  |<span data-ttu-id="e49aa-236">Kurz</span><span class="sxs-lookup"><span data-stu-id="e49aa-236">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="e49aa-237">Vývoj nové aplikace</span><span class="sxs-lookup"><span data-stu-id="e49aa-237">For new development</span></span>        |[<span data-ttu-id="e49aa-238">Razor Pages pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e49aa-238">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="e49aa-239">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="e49aa-239">For maintaining an MVC app</span></span> |[<span data-ttu-id="e49aa-240">MVC pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e49aa-240">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="e49aa-241">Přečtěte si přehled základních ASP.NET základních [principů,](xref:fundamentals/index) které platí pro všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="e49aa-241">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="e49aa-242">Projděte obsah a najděte další témata, která vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="e49aa-242">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="e49aa-243">&dagger;K dispozici je také [web API výukový program, který budete postupovat zcela v prohlížeči](/learn/modules/build-web-api-net-core), není nutná žádná místní instalace IDE.</span><span class="sxs-lookup"><span data-stu-id="e49aa-243">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="e49aa-244">Kód běží v prostředí [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) a k testování se používá [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="e49aa-244">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="e49aa-245">Migrace z rozhraní .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e49aa-245">Migrate from .NET Framework</span></span>

<span data-ttu-id="e49aa-246">Referenční příručku pro migraci ASP.NET aplikací do <xref:migration/proper-to-2x/index>ASP.NET Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e49aa-246">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="e49aa-247">Jak si stáhnout ukázku</span><span class="sxs-lookup"><span data-stu-id="e49aa-247">How to download a sample</span></span>

<span data-ttu-id="e49aa-248">Řada článků a kurzů obsahuje odkazy na vzorový kód.</span><span class="sxs-lookup"><span data-stu-id="e49aa-248">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="e49aa-249">[Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="e49aa-249">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="e49aa-250">Rozbalte soubor *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="e49aa-250">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="e49aa-251">Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.</span><span class="sxs-lookup"><span data-stu-id="e49aa-251">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="e49aa-252">Direktivy preprocesoru ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="e49aa-252">Preprocessor directives in sample code</span></span>

<span data-ttu-id="e49aa-253">Chcete-li demonstrovat více `#define` scénářů, ukázkové aplikace používají direktivy a `#if-#else/#elif-#endif` preprocesoru selektivně kompilovat a spouštět různé části ukázkového kódu.</span><span class="sxs-lookup"><span data-stu-id="e49aa-253">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="e49aa-254">Pro ty ukázky, které využívají `#define` tento přístup, nastavte direktivu v horní části souborů C# definovat symbol přidružený ke scénáři, který chcete spustit.</span><span class="sxs-lookup"><span data-stu-id="e49aa-254">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="e49aa-255">Některé ukázky vyžadují definování symbolu v horní části více souborů ke spuštění scénáře.</span><span class="sxs-lookup"><span data-stu-id="e49aa-255">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="e49aa-256">Například následující seznam symbolů `#define` udává, že jsou dostupné čtyři scénáře (jeden scénář na symbol).</span><span class="sxs-lookup"><span data-stu-id="e49aa-256">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="e49aa-257">Při aktuální konfiguraci ukázky se spustí scénář `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="e49aa-257">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="e49aa-258">Pokud chcete ukázku změnit tak, aby se spustil scénář `ExpandDefault`, definujte symbol `ExpandDefault` a zbývající symboly nechejte zakomentované:</span><span class="sxs-lookup"><span data-stu-id="e49aa-258">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="e49aa-259">Další informace o používání [direktiv preprocesoru C#](/dotnet/csharp/language-reference/preprocessor-directives/) k selektivní kompilaci částí kódu najdete v článku [#define (referenční dokumentace jazyka C# )](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) a [#if (referenční dokumentace jazyka C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="e49aa-259">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="e49aa-260">Oblasti ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="e49aa-260">Regions in sample code</span></span>

<span data-ttu-id="e49aa-261">Některé ukázkové aplikace obsahují části kódu obklopené [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) a [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) direktivy C#.</span><span class="sxs-lookup"><span data-stu-id="e49aa-261">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="e49aa-262">Systém pro sestavování dokumentace vkládá tyto oblasti do zobrazených témat dokumentace.</span><span class="sxs-lookup"><span data-stu-id="e49aa-262">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="e49aa-263">Názvy oblastí obvykle obsahují slovo „snippet“.</span><span class="sxs-lookup"><span data-stu-id="e49aa-263">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="e49aa-264">Následující příklad ukazuje oblast s názvem `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="e49aa-264">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="e49aa-265">Na předchozí fragment kódu C# odkazuje soubor Markdown tohoto tématu pomocí následujícího řádku:</span><span class="sxs-lookup"><span data-stu-id="e49aa-265">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="e49aa-266">Můžete bezpečně ignorovat (nebo `#region` odebrat) a `#endregion` direktivy, které obklopují kód.</span><span class="sxs-lookup"><span data-stu-id="e49aa-266">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="e49aa-267">Neměňte kód v rámci těchto směrnic, pokud plánujete spustit ukázkové scénáře popsané v tématu.</span><span class="sxs-lookup"><span data-stu-id="e49aa-267">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="e49aa-268">Při experimentování s jinými scénáři můžete tento kód klidně pozměnit.</span><span class="sxs-lookup"><span data-stu-id="e49aa-268">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="e49aa-269">Další informace najdete v článku [Příspěvky k dokumentaci rozhraní ASP.NET: fragmenty kódu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="e49aa-269">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e49aa-270">Další kroky</span><span class="sxs-lookup"><span data-stu-id="e49aa-270">Next steps</span></span>

<span data-ttu-id="e49aa-271">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="e49aa-271">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="e49aa-272">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e49aa-272">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="e49aa-273">[Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány.</span><span class="sxs-lookup"><span data-stu-id="e49aa-273">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="e49aa-274">Nabízí nové blogové příspěvky a software třetích stran.</span><span class="sxs-lookup"><span data-stu-id="e49aa-274">It features new blogs and third-party software.</span></span>
