---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Nechte si představit ASP.NET Core, což je platformově univerzální, vysoce výkonná architektura typu open-source, která slouží k vytváření moderních cloudových aplikací připojených k internetu.
ms.author: riande
ms.custom: mvc
ms.date: 11/10/2018
uid: index
ms.openlocfilehash: 1699acc0086dfd50c573afc239bc8f37eb9e7af9
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569985"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="4a07c-103">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a07c-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="4a07c-104">Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="4a07c-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="4a07c-105">ASP.NET Core je platformově univerzální, vysoce výkonná architektura typu [open-source](https://github.com/aspnet/home), která slouží k vytváření moderních cloudových aplikací připojených k internetu.</span><span class="sxs-lookup"><span data-stu-id="4a07c-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="4a07c-106">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="4a07c-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="4a07c-107">Vytvářet webové aplikace a služby, aplikace [IoT](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.</span><span class="sxs-lookup"><span data-stu-id="4a07c-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="4a07c-108">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="4a07c-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="4a07c-109">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="4a07c-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="4a07c-110">Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="4a07c-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-use-aspnet-core"></a><span data-ttu-id="4a07c-111">Proč používat ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="4a07c-111">Why use ASP.NET Core?</span></span>

<span data-ttu-id="4a07c-112">Miliony vývojářů už dříve k vytváření webových aplikací používali [ASP.NET 4.x](/aspnet/overview) (což bude platit i nadále).</span><span class="sxs-lookup"><span data-stu-id="4a07c-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="4a07c-113">ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.</span><span class="sxs-lookup"><span data-stu-id="4a07c-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="4a07c-114">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4a07c-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="4a07c-115">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="4a07c-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="4a07c-116">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.</span><span class="sxs-lookup"><span data-stu-id="4a07c-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="4a07c-117">[Stránky Razor](xref:razor-pages/index) (nově ve verzi ASP.NET Core 2.0) představují stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="4a07c-117">[Razor Pages](xref:razor-pages/index) (new in ASP.NET Core 2.0) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="4a07c-118">[Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="4a07c-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="4a07c-119">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="4a07c-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="4a07c-120">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="4a07c-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="4a07c-121">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="4a07c-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="4a07c-122">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="4a07c-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="4a07c-123">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="4a07c-123">Client-side development</span></span>

<span data-ttu-id="4a07c-124">Architektura ASP.NET Core se hladce integruje do oblíbených klientských rozhraní a knihoven, včetně [Angular](xref:spa/angular), [React](xref:spa/react) a [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="4a07c-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="4a07c-125">Další informace najdete v článku [Vývoj na straně klienta](xref:client-side/index).</span><span class="sxs-lookup"><span data-stu-id="4a07c-125">For more information, see [Client-side development](xref:client-side/index).</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="4a07c-126">Cílení ASP.NET Core na .NET Framework</span><span class="sxs-lookup"><span data-stu-id="4a07c-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="4a07c-127">Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="4a07c-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="4a07c-128">Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows.</span><span class="sxs-lookup"><span data-stu-id="4a07c-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="4a07c-129">ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="4a07c-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="4a07c-130">Aplikace vytvořené pomocí .NET Standard 2.0 běží všude, kde se podporuje .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="4a07c-130">Apps written with .NET Standard 2.0 run anywhere that .NET Standard 2.0 is supported.</span></span>

<span data-ttu-id="4a07c-131">ASP.NET Core 2.x se podporuje ve verzích .NET Framework kompatibilních s .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="4a07c-131">ASP.NET Core 2.x is supported on .NET Framework versions compatible with .NET Standard 2.0:</span></span>

* <span data-ttu-id="4a07c-132">Důrazně se doporučuje .NET Framework 4.7.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="4a07c-132">.NET Framework 4.7.1 and later is strongly recommended.</span></span>
* <span data-ttu-id="4a07c-133">.NET Framework 4.6.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="4a07c-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="4a07c-134">ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4a07c-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="4a07c-135">Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="4a07c-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="4a07c-136">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="4a07c-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="4a07c-137">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="4a07c-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="4a07c-138">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="4a07c-138">Cross-platform.</span></span> <span data-ttu-id="4a07c-139">Běží v systémech macOS, Linux a Windows.</span><span class="sxs-lookup"><span data-stu-id="4a07c-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="4a07c-140">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="4a07c-140">Improved performance</span></span>
* <span data-ttu-id="4a07c-141">Souběžná správa verzí</span><span class="sxs-lookup"><span data-stu-id="4a07c-141">Side-by-side versioning</span></span>
* <span data-ttu-id="4a07c-142">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4a07c-142">New APIs</span></span>
* <span data-ttu-id="4a07c-143">Open source</span><span class="sxs-lookup"><span data-stu-id="4a07c-143">Open source</span></span>

<span data-ttu-id="4a07c-144">Ze všech sil se snažíme doplnit rozhraní API z .NET Framework do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4a07c-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="4a07c-145">Sada [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) zpřístupnila v .NET Core tisíce rozhraní API, která byla určena jen pro Windows.</span><span class="sxs-lookup"><span data-stu-id="4a07c-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="4a07c-146">Tato rozhraní API nebyla v .NET Core 1.x dostupná.</span><span class="sxs-lookup"><span data-stu-id="4a07c-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="4a07c-147">Jak si stáhnout ukázku</span><span class="sxs-lookup"><span data-stu-id="4a07c-147">How to download a sample</span></span>

<span data-ttu-id="4a07c-148">Řada článků a kurzů obsahuje odkazy na vzorový kód.</span><span class="sxs-lookup"><span data-stu-id="4a07c-148">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="4a07c-149">[Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/aspnet/Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="4a07c-149">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/Docs/zip/master).</span></span>
1. <span data-ttu-id="4a07c-150">Rozbalte soubor *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="4a07c-150">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="4a07c-151">Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.</span><span class="sxs-lookup"><span data-stu-id="4a07c-151">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

<span data-ttu-id="4a07c-152">Pro účely demonstrace více scénářů využívají ukázkové aplikace příkazy `#define` a `#if-#else/#elif-#endif` jazyka C# k selektivní kompilaci a spouštění různých částí vzorového kódu.</span><span class="sxs-lookup"><span data-stu-id="4a07c-152">To demonstrate multiple scenarios, sample apps make use of the `#define` and `#if-#else/#elif-#endif` C# statements to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="4a07c-153">U ukázek, které používají tento přístup, nastavte příkaz `#define` na začátku souborů C# na symbol přidružený ke scénáři, který chcete spustit.</span><span class="sxs-lookup"><span data-stu-id="4a07c-153">For those samples that make use of this approach, set the `#define` statement at the top of the C# files to the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="4a07c-154">Ukázka může vyžadovat nastavení symbolu na začátku několika souborů kvůli spuštění určitého scénáře.</span><span class="sxs-lookup"><span data-stu-id="4a07c-154">A sample may require you to set the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="4a07c-155">Například následující seznam symbolů `#define` udává, že jsou dostupné čtyři scénáře (jeden scénář na symbol).</span><span class="sxs-lookup"><span data-stu-id="4a07c-155">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="4a07c-156">Při aktuální konfiguraci ukázky se spustí scénář `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="4a07c-156">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="4a07c-157">Pokud chcete ukázku změnit tak, aby se spustil scénář `ExpandDefault`, definujte symbol `ExpandDefault` a zbývající symboly nechejte zakomentované:</span><span class="sxs-lookup"><span data-stu-id="4a07c-157">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="4a07c-158">Další informace o používání [direktiv preprocesoru C#](/dotnet/csharp/language-reference/preprocessor-directives/) k selektivní kompilaci částí kódu najdete v článku [#define (referenční dokumentace jazyka C# )](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) a [#if (referenční dokumentace jazyka C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="4a07c-158">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

## <a name="next-steps"></a><span data-ttu-id="4a07c-159">Další kroky</span><span class="sxs-lookup"><span data-stu-id="4a07c-159">Next steps</span></span>

<span data-ttu-id="4a07c-160">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="4a07c-160">For more information, see the following resources:</span></span>

* [<span data-ttu-id="4a07c-161">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="4a07c-161">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="4a07c-162">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a07c-162">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="4a07c-163">[Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány.</span><span class="sxs-lookup"><span data-stu-id="4a07c-163">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="4a07c-164">Nabízí nové blogové příspěvky a software třetích stran.</span><span class="sxs-lookup"><span data-stu-id="4a07c-164">It features new blogs and third-party software.</span></span>
