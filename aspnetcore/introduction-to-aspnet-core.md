---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Seznamte se s ASP.NET Core, vysoce výkonnou a open-source architekturou pro vytváření moderních cloudových aplikací připojených k Internetu.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: 9cf721b381d3c4c737c15225edcf7918dd1fcf5b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015345"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="08df8-103">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08df8-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="08df8-104">Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="08df8-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08df8-105">ASP.NET Core je vysoce výkonná [Open-Source](https://github.com/dotnet/aspnetcore) platforma pro různé platformy, která umožňuje vytvářet moderní cloudové aplikace připojené k Internetu.</span><span class="sxs-lookup"><span data-stu-id="08df8-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="08df8-106">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="08df8-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="08df8-107">Sestavujte webové aplikace a služby, aplikace [Internet věcí (IoT)](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.</span><span class="sxs-lookup"><span data-stu-id="08df8-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="08df8-108">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="08df8-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="08df8-109">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="08df8-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="08df8-110">Spusťte v [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="08df8-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="08df8-111">Proč zvolit ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="08df8-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="08df8-112">Miliony vývojářů používají nebo používali [ASP.NET 4. x](/aspnet/overview) k vytváření webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="08df8-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="08df8-113">ASP.NET Core je přepracování ASP.NET 4. x, včetně změn v architektuře, jejichž výsledkem je štíhlá a větší modulární architektura.</span><span class="sxs-lookup"><span data-stu-id="08df8-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="08df8-114">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="08df8-115">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="08df8-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="08df8-116">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.</span><span class="sxs-lookup"><span data-stu-id="08df8-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="08df8-117">[ Razor Stránky](xref:razor-pages/index) jsou programovací model založený na stránkách, který usnadňuje vytváření webového uživatelského rozhraní a lepší produktivitu.</span><span class="sxs-lookup"><span data-stu-id="08df8-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="08df8-118">[ Razor značky](xref:mvc/views/razor) poskytují produktivní syntaxi pro [ Razor stránky](xref:razor-pages/index) a [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="08df8-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="08df8-119">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="08df8-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="08df8-120">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="08df8-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="08df8-121">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="08df8-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="08df8-122">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="08df8-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="08df8-123">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="08df8-123">Client-side development</span></span>

<span data-ttu-id="08df8-124">ASP.NET Core se hladce integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index) , [úhlů](xref:spa/angular), [reakce](xref:spa/react)a [zavedení](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="08df8-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="08df8-125">Další informace najdete v tématech <xref:blazor/index> a souvisejících tématech v části *vývoj na straně klienta*.</span><span class="sxs-lookup"><span data-stu-id="08df8-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="08df8-126">ASP.NET Core cílové architektury</span><span class="sxs-lookup"><span data-stu-id="08df8-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="08df8-127">ASP.NET Core 3. x a novější může cílit jenom na .NET Core.</span><span class="sxs-lookup"><span data-stu-id="08df8-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="08df8-128">Obecně se ASP.NET Core skládá z knihoven [.NET Standard](/dotnet/standard/net-standard) .</span><span class="sxs-lookup"><span data-stu-id="08df8-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="08df8-129">Knihovny napsané pomocí .NET Standard 2,0 běží na libovolné [platformě .NET, která implementuje .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="08df8-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="08df8-130">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="08df8-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="08df8-131">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="08df8-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="08df8-132">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="08df8-132">Cross-platform.</span></span> <span data-ttu-id="08df8-133">Běží na Windows, macOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="08df8-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="08df8-134">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="08df8-134">Improved performance</span></span>
* [<span data-ttu-id="08df8-135">Souběžná správa verzí</span><span class="sxs-lookup"><span data-stu-id="08df8-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="08df8-136">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="08df8-136">New APIs</span></span>
* <span data-ttu-id="08df8-137">Open source</span><span class="sxs-lookup"><span data-stu-id="08df8-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="08df8-138">Doporučený studijní program</span><span class="sxs-lookup"><span data-stu-id="08df8-138">Recommended learning path</span></span>

<span data-ttu-id="08df8-139">Pro Úvod k vývoji ASP.NET Corech aplikací doporučujeme následující posloupnost kurzů:</span><span class="sxs-lookup"><span data-stu-id="08df8-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="08df8-140">Postupujte podle kurzu pro typ aplikace, který chcete vyvíjet nebo udržovat.</span><span class="sxs-lookup"><span data-stu-id="08df8-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="08df8-141">Typ aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-141">App type</span></span>  |<span data-ttu-id="08df8-142">Scénář</span><span class="sxs-lookup"><span data-stu-id="08df8-142">Scenario</span></span>  |<span data-ttu-id="08df8-143">Tutoriál</span><span class="sxs-lookup"><span data-stu-id="08df8-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="08df8-144">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-144">Web app</span></span>                   | <span data-ttu-id="08df8-145">Nový vývoj webového uživatelského rozhraní na straně serveru</span><span class="sxs-lookup"><span data-stu-id="08df8-145">New server-side web UI development</span></span> |[<span data-ttu-id="08df8-146">Začínáme se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="08df8-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="08df8-147">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-147">Web app</span></span>                   | <span data-ttu-id="08df8-148">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="08df8-149">Začínáme s MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="08df8-150">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-150">Web app</span></span>                   | <span data-ttu-id="08df8-151">Vývoj webového uživatelského rozhraní na straně klienta</span><span class="sxs-lookup"><span data-stu-id="08df8-151">Client-side web UI development</span></span> |[<span data-ttu-id="08df8-152">Začínáme sBlazor</span><span class="sxs-lookup"><span data-stu-id="08df8-152">Get started with Blazor</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="08df8-153">Webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="08df8-153">Web API</span></span>                   | <span data-ttu-id="08df8-154">Služby HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="08df8-154">RESTful HTTP services</span></span> |<span data-ttu-id="08df8-155">[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="08df8-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="08df8-156">Aplikace vzdáleného volání procedur</span><span class="sxs-lookup"><span data-stu-id="08df8-156">Remote Procedure Call app</span></span> | <span data-ttu-id="08df8-157">Služby pro první kontrakt využívající vyrovnávací paměti protokolu</span><span class="sxs-lookup"><span data-stu-id="08df8-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="08df8-158">Začínáme se službou gRPC</span><span class="sxs-lookup"><span data-stu-id="08df8-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="08df8-159">Aplikace v reálném čase</span><span class="sxs-lookup"><span data-stu-id="08df8-159">Real-time app</span></span>             | <span data-ttu-id="08df8-160">Obousměrná komunikace mezi servery a připojenými klienty</span><span class="sxs-lookup"><span data-stu-id="08df8-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="08df8-161">Začínáme sSignalR</span><span class="sxs-lookup"><span data-stu-id="08df8-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="08df8-162">Postupujte podle kurzu, který ukazuje, jak získat základní přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="08df8-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="08df8-163">Scénář</span><span class="sxs-lookup"><span data-stu-id="08df8-163">Scenario</span></span>  |<span data-ttu-id="08df8-164">Tutoriál</span><span class="sxs-lookup"><span data-stu-id="08df8-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="08df8-165">Nový vývoj</span><span class="sxs-lookup"><span data-stu-id="08df8-165">New development</span></span>        |[<span data-ttu-id="08df8-166">RazorStránky s Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="08df8-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="08df8-167">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="08df8-168">MVC pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="08df8-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="08df8-169">Přečtěte si přehled ASP.NET Core [základních](xref:fundamentals/index) součástí, které se vztahují na všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="08df8-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="08df8-170">Projděte si obsah a vyhledejte další témata, která vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="08df8-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="08df8-171">&dagger;K dispozici je také [Interaktivní kurz webového rozhraní API](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="08df8-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="08df8-172">Není nutná žádná místní instalace vývojářských nástrojů.</span><span class="sxs-lookup"><span data-stu-id="08df8-172">No local installation of development tools is required.</span></span> <span data-ttu-id="08df8-173">Kód se spouští v [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) v prohlížeči a k testování se používá [kudrlinkou](https://curl.haxx.se/) .</span><span class="sxs-lookup"><span data-stu-id="08df8-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="08df8-174">Migrace z .NET Framework</span><span class="sxs-lookup"><span data-stu-id="08df8-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="08df8-175">Referenční příručka pro migraci aplikací ASP.NET 4. x na ASP.NET Core najdete v tématu <xref:migration/proper-to-2x/index> .</span><span class="sxs-lookup"><span data-stu-id="08df8-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08df8-176">ASP.NET Core je vysoce výkonná [Open-Source](https://github.com/dotnet/aspnetcore) platforma pro různé platformy, která umožňuje vytvářet moderní cloudové aplikace připojené k Internetu.</span><span class="sxs-lookup"><span data-stu-id="08df8-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="08df8-177">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="08df8-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="08df8-178">Sestavujte webové aplikace a služby, aplikace [Internet věcí (IoT)](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.</span><span class="sxs-lookup"><span data-stu-id="08df8-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="08df8-179">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="08df8-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="08df8-180">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="08df8-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="08df8-181">Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="08df8-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="08df8-182">Proč zvolit ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="08df8-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="08df8-183">Miliony vývojářů používají nebo používali [ASP.NET 4. x](/aspnet/overview) k vytváření webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="08df8-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="08df8-184">ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.</span><span class="sxs-lookup"><span data-stu-id="08df8-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="08df8-185">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="08df8-186">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="08df8-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="08df8-187">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.</span><span class="sxs-lookup"><span data-stu-id="08df8-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="08df8-188">[ Razor Stránky](xref:razor-pages/index) jsou programovací model založený na stránkách, který usnadňuje vytváření webového uživatelského rozhraní a lepší produktivitu.</span><span class="sxs-lookup"><span data-stu-id="08df8-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="08df8-189">[ Razor značky](xref:mvc/views/razor) poskytují produktivní syntaxi pro [ Razor stránky](xref:razor-pages/index) a [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="08df8-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="08df8-190">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="08df8-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="08df8-191">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="08df8-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="08df8-192">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="08df8-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="08df8-193">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="08df8-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="08df8-194">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="08df8-194">Client-side development</span></span>

<span data-ttu-id="08df8-195">ASP.NET Core se hladce integruje s oblíbenými architekturami a knihovnami na straně klienta, včetně [Blazor](xref:blazor/index) , [úhlů](xref:spa/angular), [reakce](xref:spa/react)a [zavedení](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="08df8-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="08df8-196">Další informace najdete v tématech <xref:blazor/index> a souvisejících tématech v části *vývoj na straně klienta*.</span><span class="sxs-lookup"><span data-stu-id="08df8-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="08df8-197">Cílení ASP.NET Core na .NET Framework</span><span class="sxs-lookup"><span data-stu-id="08df8-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="08df8-198">Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="08df8-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="08df8-199">Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows.</span><span class="sxs-lookup"><span data-stu-id="08df8-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="08df8-200">ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="08df8-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="08df8-201">Knihovny napsané pomocí .NET Standard 2,0 běží na libovolné [platformě .NET, která implementuje .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="08df8-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="08df8-202">ASP.NET Core 2. x se podporuje v .NET Framework verzích, které implementují .NET Standard 2,0:</span><span class="sxs-lookup"><span data-stu-id="08df8-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="08df8-203">Doporučuje se .NET Framework nejnovější verze.</span><span class="sxs-lookup"><span data-stu-id="08df8-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="08df8-204">.NET Framework 4.6.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="08df8-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="08df8-205">ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="08df8-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="08df8-206">Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="08df8-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="08df8-207">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="08df8-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="08df8-208">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="08df8-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="08df8-209">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="08df8-209">Cross-platform.</span></span> <span data-ttu-id="08df8-210">Běží v systémech macOS, Linux a Windows.</span><span class="sxs-lookup"><span data-stu-id="08df8-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="08df8-211">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="08df8-211">Improved performance</span></span>
* [<span data-ttu-id="08df8-212">Souběžná správa verzí</span><span class="sxs-lookup"><span data-stu-id="08df8-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="08df8-213">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="08df8-213">New APIs</span></span>
* <span data-ttu-id="08df8-214">Open source</span><span class="sxs-lookup"><span data-stu-id="08df8-214">Open source</span></span>

<span data-ttu-id="08df8-215">Aby bylo možné zavřít mezeru rozhraní API z .NET Framework do .NET Core, [Sada Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) vystavila tisíce rozhraní API jenom pro Windows, která jsou k dispozici v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="08df8-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="08df8-216">Tato rozhraní API nebyla v .NET Core 1.x dostupná.</span><span class="sxs-lookup"><span data-stu-id="08df8-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="08df8-217">Doporučený studijní program</span><span class="sxs-lookup"><span data-stu-id="08df8-217">Recommended learning path</span></span>

<span data-ttu-id="08df8-218">Jako úvod do vývoje aplikací ASP.NET Core doporučujeme následující posloupnost kurzů a článků:</span><span class="sxs-lookup"><span data-stu-id="08df8-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="08df8-219">Postupujte podle kurzu pro typ aplikace, kterou chcete vyvíjet nebo udržovat.</span><span class="sxs-lookup"><span data-stu-id="08df8-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="08df8-220">Typ aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-220">App type</span></span>  |<span data-ttu-id="08df8-221">Scénář</span><span class="sxs-lookup"><span data-stu-id="08df8-221">Scenario</span></span>  |<span data-ttu-id="08df8-222">Tutoriál</span><span class="sxs-lookup"><span data-stu-id="08df8-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="08df8-223">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-223">Web app</span></span>                   | <span data-ttu-id="08df8-224">Vývoj nové aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-224">For new development</span></span>        |[<span data-ttu-id="08df8-225">Začínáme se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="08df8-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="08df8-226">Webová aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-226">Web app</span></span>                   | <span data-ttu-id="08df8-227">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="08df8-228">Začínáme s MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="08df8-229">Webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="08df8-229">Web API</span></span>                   |                            |<span data-ttu-id="08df8-230">[Vytvoření webového rozhraní API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="08df8-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="08df8-231">Aplikace v reálném čase</span><span class="sxs-lookup"><span data-stu-id="08df8-231">Real-time app</span></span>             |                            |[<span data-ttu-id="08df8-232">Začínáme sSignalR</span><span class="sxs-lookup"><span data-stu-id="08df8-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="08df8-233">Postupujte podle kurzu, který ukazuje, jak získat základní přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="08df8-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="08df8-234">Scénář</span><span class="sxs-lookup"><span data-stu-id="08df8-234">Scenario</span></span>  |<span data-ttu-id="08df8-235">Tutoriál</span><span class="sxs-lookup"><span data-stu-id="08df8-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="08df8-236">Vývoj nové aplikace</span><span class="sxs-lookup"><span data-stu-id="08df8-236">For new development</span></span>        |[<span data-ttu-id="08df8-237">RazorStránky s Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="08df8-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="08df8-238">Údržba aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="08df8-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="08df8-239">MVC pomocí Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="08df8-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="08df8-240">Přečtěte si přehled ASP.NET Core [základních](xref:fundamentals/index) součástí, které se vztahují na všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="08df8-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="08df8-241">Projděte obsah a najděte další témata, která vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="08df8-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="08df8-242">&dagger;K dispozici je také [kurz webového rozhraní API, který se řídí zcela v prohlížeči](/learn/modules/build-web-api-net-core), nevyžaduje se žádná místní instalace IDE.</span><span class="sxs-lookup"><span data-stu-id="08df8-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="08df8-243">Kód běží v prostředí [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) a k testování se používá [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="08df8-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="08df8-244">Migrace z .NET Framework</span><span class="sxs-lookup"><span data-stu-id="08df8-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="08df8-245">Referenční příručka pro migraci aplikací ASP.NET na ASP.NET Core najdete v tématu <xref:migration/proper-to-2x/index> .</span><span class="sxs-lookup"><span data-stu-id="08df8-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="08df8-246">Jak si stáhnout ukázku</span><span class="sxs-lookup"><span data-stu-id="08df8-246">How to download a sample</span></span>

<span data-ttu-id="08df8-247">Řada článků a kurzů obsahuje odkazy na vzorový kód.</span><span class="sxs-lookup"><span data-stu-id="08df8-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="08df8-248">[Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="08df8-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="08df8-249">Rozbalte soubor *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="08df8-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="08df8-250">Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.</span><span class="sxs-lookup"><span data-stu-id="08df8-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="08df8-251">Direktivy preprocesoru ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="08df8-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="08df8-252">Aby bylo možné předvést více scénářů, ukázkové aplikace používají `#define` `#if-#else/#elif-#endif` direktivy preprocesoru a k selektivní kompilaci a spouštění různých sekcí ukázkového kódu.</span><span class="sxs-lookup"><span data-stu-id="08df8-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="08df8-253">Pro tyto ukázky, které využívají tento přístup, nastavte `#define` direktivu v horní části souborů C# k definování symbolu přidruženého ke scénáři, který chcete spustit.</span><span class="sxs-lookup"><span data-stu-id="08df8-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="08df8-254">Některé ukázky vyžadují definování symbolu v horní části více souborů, aby bylo možné spustit scénář.</span><span class="sxs-lookup"><span data-stu-id="08df8-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="08df8-255">Například následující seznam symbolů `#define` udává, že jsou dostupné čtyři scénáře (jeden scénář na symbol).</span><span class="sxs-lookup"><span data-stu-id="08df8-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="08df8-256">Při aktuální konfiguraci ukázky se spustí scénář `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="08df8-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="08df8-257">Pokud chcete ukázku změnit tak, aby se spustil scénář `ExpandDefault`, definujte symbol `ExpandDefault` a zbývající symboly nechejte zakomentované:</span><span class="sxs-lookup"><span data-stu-id="08df8-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="08df8-258">Další informace o používání [direktiv preprocesoru C#](/dotnet/csharp/language-reference/preprocessor-directives/) k selektivní kompilaci částí kódu najdete v článku [#define (referenční dokumentace jazyka C# )](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) a [#if (referenční dokumentace jazyka C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="08df8-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="08df8-259">Oblasti ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="08df8-259">Regions in sample code</span></span>

<span data-ttu-id="08df8-260">Některé ukázkové aplikace obsahují oddíly kódu, které jsou obklopeny [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) a [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) direktivami jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="08df8-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="08df8-261">Systém pro sestavování dokumentace vkládá tyto oblasti do zobrazených témat dokumentace.</span><span class="sxs-lookup"><span data-stu-id="08df8-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="08df8-262">Názvy oblastí obvykle obsahují slovo „snippet“.</span><span class="sxs-lookup"><span data-stu-id="08df8-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="08df8-263">Následující příklad ukazuje oblast s názvem `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="08df8-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="08df8-264">Na předchozí fragment kódu C# odkazuje soubor Markdown tohoto tématu pomocí následujícího řádku:</span><span class="sxs-lookup"><span data-stu-id="08df8-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="08df8-265">Můžete bezpečně ignorovat (nebo odebrat) `#region` `#endregion` direktivy a, které obklopují kód.</span><span class="sxs-lookup"><span data-stu-id="08df8-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="08df8-266">Pokud plánujete spustit ukázkové scénáře popsané v tématu, neměňte kód v těchto direktivách.</span><span class="sxs-lookup"><span data-stu-id="08df8-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="08df8-267">Při experimentování s jinými scénáři můžete tento kód klidně pozměnit.</span><span class="sxs-lookup"><span data-stu-id="08df8-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="08df8-268">Další informace najdete v článku [Příspěvky k dokumentaci rozhraní ASP.NET: fragmenty kódu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="08df8-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="08df8-269">Další kroky</span><span class="sxs-lookup"><span data-stu-id="08df8-269">Next steps</span></span>

<span data-ttu-id="08df8-270">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="08df8-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="08df8-271">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08df8-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="08df8-272">[Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány.</span><span class="sxs-lookup"><span data-stu-id="08df8-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="08df8-273">Nabízí nové blogové příspěvky a software třetích stran.</span><span class="sxs-lookup"><span data-stu-id="08df8-273">It features new blogs and third-party software.</span></span>
