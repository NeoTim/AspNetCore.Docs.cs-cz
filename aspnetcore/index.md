---
title: Úvod do ASP.NET Core
author: rick-anderson
description: Nechte si představit ASP.NET Core, což je platformově univerzální, vysoce výkonná architektura typu open-source, která slouží k vytváření moderních cloudových aplikací připojených k internetu.
ms.author: riande
ms.custom: mvc
ms.date: 11/16/2018
uid: index
ms.openlocfilehash: ccf00316218c0787136193a7acaf55b8687c6ede
ms.sourcegitcommit: 04b55a5ce9d649ff2df926157ec28ae47afe79e2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52156942"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="0f659-103">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f659-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="0f659-104">Autoři: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) a [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="0f659-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="0f659-105">ASP.NET Core je platformově univerzální, vysoce výkonná architektura typu [open-source](https://github.com/aspnet/home), která slouží k vytváření moderních cloudových aplikací připojených k internetu.</span><span class="sxs-lookup"><span data-stu-id="0f659-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="0f659-106">S ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="0f659-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="0f659-107">Vytvářet webové aplikace a služby, aplikace [IoT](https://www.microsoft.com/internet-of-things/) a mobilní back-endy.</span><span class="sxs-lookup"><span data-stu-id="0f659-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="0f659-108">Používat oblíbené vývojářské nástroje v systémech Windows, Mac OS a Linux.</span><span class="sxs-lookup"><span data-stu-id="0f659-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="0f659-109">Nasazovat v cloudu nebo místně</span><span class="sxs-lookup"><span data-stu-id="0f659-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="0f659-110">Používat ke spuštění [.NET Core nebo .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="0f659-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-use-aspnet-core"></a><span data-ttu-id="0f659-111">Proč používat ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="0f659-111">Why use ASP.NET Core?</span></span>

<span data-ttu-id="0f659-112">Miliony vývojářů už dříve k vytváření webových aplikací používali [ASP.NET 4.x](/aspnet/overview) (což bude platit i nadále).</span><span class="sxs-lookup"><span data-stu-id="0f659-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="0f659-113">ASP.NET Core je přepracovanou verzí ASP.NET 4.x se změněnou architekturou. Výsledkem je odlehčené a modulárnější prostředí.</span><span class="sxs-lookup"><span data-stu-id="0f659-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="0f659-114">Vytváření webových rozhraní API a webového uživatelského rozhraní v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0f659-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="0f659-115">Architektura ASP.NET Core MVC nabízí funkce umožňující vytvářet [webová rozhraní API](xref:tutorials/first-web-api) a [webové aplikace](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="0f659-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="0f659-116">[Vzor Model-View-Controller (MVC)](xref:mvc/overview) přispívá k tomu, aby vaše webová rozhraní API a webové aplikace byly testovatelné.</span><span class="sxs-lookup"><span data-stu-id="0f659-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="0f659-117">[Stránky Razor](xref:razor-pages/index) (nově ve verzi ASP.NET Core 2.0) představují stránkovaný programový model, se kterým je vytváření webového uživatelského rozhraní jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="0f659-117">[Razor Pages](xref:razor-pages/index) (new in ASP.NET Core 2.0) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="0f659-118">[Kód Razor](xref:mvc/views/razor) poskytuje produktivní syntaxi jak pro [stránky Razor](xref:razor-pages/index), tak pro [zobrazení MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="0f659-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="0f659-119">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="0f659-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="0f659-120">Díky integrované podpoře [různých datových formátů a vyjednávání obsahu](xref:web-api/advanced/formatting) mohou vaše webová rozhraní API používat různí klienti, včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="0f659-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="0f659-121">[Vazby modelu](xref:mvc/models/model-binding) automaticky mapují data požadavků HTTP na parametry metod akce.</span><span class="sxs-lookup"><span data-stu-id="0f659-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="0f659-122">[Ověření modelu](xref:mvc/models/validation) automaticky provede ověření na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="0f659-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="0f659-123">Vývoj klientské strany</span><span class="sxs-lookup"><span data-stu-id="0f659-123">Client-side development</span></span>

<span data-ttu-id="0f659-124">Architektura ASP.NET Core se hladce integruje do oblíbených klientských rozhraní a knihoven, včetně [Angular](xref:spa/angular), [React](xref:spa/react) a [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="0f659-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="0f659-125">Další informace najdete v článku [Vývoj na straně klienta](xref:client-side/index).</span><span class="sxs-lookup"><span data-stu-id="0f659-125">For more information, see [Client-side development](xref:client-side/index).</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="0f659-126">Cílení ASP.NET Core na .NET Framework</span><span class="sxs-lookup"><span data-stu-id="0f659-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="0f659-127">Cílem ASP.NET Core 2.x může být .NET Core nebo .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0f659-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="0f659-128">Aplikace ASP.NET Core, jejichž cílem je .NET Framework, nejsou multiplatformní, ale běží jen ve Windows.</span><span class="sxs-lookup"><span data-stu-id="0f659-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="0f659-129">ASP.NET Core 2.x se obecně skládá z knihoven [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="0f659-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="0f659-130">Aplikace vytvořené pomocí .NET Standard 2.0 běží všude, kde se podporuje .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="0f659-130">Apps written with .NET Standard 2.0 run anywhere that .NET Standard 2.0 is supported.</span></span>

<span data-ttu-id="0f659-131">ASP.NET Core 2.x se podporuje ve verzích .NET Framework kompatibilních s .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="0f659-131">ASP.NET Core 2.x is supported on .NET Framework versions compatible with .NET Standard 2.0:</span></span>

* <span data-ttu-id="0f659-132">Důrazně se doporučuje .NET Framework 4.7.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="0f659-132">.NET Framework 4.7.1 and later is strongly recommended.</span></span>
* <span data-ttu-id="0f659-133">.NET Framework 4.6.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="0f659-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="0f659-134">ASP.NET Core 3.0 a novější poběží jenom na platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f659-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="0f659-135">Další podrobnosti týkající se této změny najdete v tématu [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (První pohled na změny, které přináší ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="0f659-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="0f659-136">Cílení na .NET Core má několik výhod, které přibývají s každou vydanou verzí.</span><span class="sxs-lookup"><span data-stu-id="0f659-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="0f659-137">Mezi výhody .NET Core oproti .NET Framework patří:</span><span class="sxs-lookup"><span data-stu-id="0f659-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="0f659-138">Mutliplatformní:</span><span class="sxs-lookup"><span data-stu-id="0f659-138">Cross-platform.</span></span> <span data-ttu-id="0f659-139">Běží v systémech macOS, Linux a Windows.</span><span class="sxs-lookup"><span data-stu-id="0f659-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="0f659-140">Vyšší výkon</span><span class="sxs-lookup"><span data-stu-id="0f659-140">Improved performance</span></span>
* <span data-ttu-id="0f659-141">Souběžná správa verzí</span><span class="sxs-lookup"><span data-stu-id="0f659-141">Side-by-side versioning</span></span>
* <span data-ttu-id="0f659-142">Nová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="0f659-142">New APIs</span></span>
* <span data-ttu-id="0f659-143">Open source</span><span class="sxs-lookup"><span data-stu-id="0f659-143">Open source</span></span>

<span data-ttu-id="0f659-144">Ze všech sil se snažíme doplnit rozhraní API z .NET Framework do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f659-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="0f659-145">Sada [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) zpřístupnila v .NET Core tisíce rozhraní API, která byla určena jen pro Windows.</span><span class="sxs-lookup"><span data-stu-id="0f659-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="0f659-146">Tato rozhraní API nebyla v .NET Core 1.x dostupná.</span><span class="sxs-lookup"><span data-stu-id="0f659-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="0f659-147">Jak si stáhnout ukázku</span><span class="sxs-lookup"><span data-stu-id="0f659-147">How to download a sample</span></span>

<span data-ttu-id="0f659-148">Řada článků a kurzů obsahuje odkazy na vzorový kód.</span><span class="sxs-lookup"><span data-stu-id="0f659-148">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="0f659-149">[Stáhněte si soubor zip úložiště ASP.NET](https://codeload.github.com/aspnet/Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="0f659-149">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/Docs/zip/master).</span></span>
1. <span data-ttu-id="0f659-150">Rozbalte soubor *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="0f659-150">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="0f659-151">Adresa URL v ukázkovém odkazu vám pomůže s přechodem do ukázkového adresáře.</span><span class="sxs-lookup"><span data-stu-id="0f659-151">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="0f659-152">Direktivy preprocesoru ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="0f659-152">Preprocessor directives in sample code</span></span>

<span data-ttu-id="0f659-153">Pro účely demonstrace různých scénářů využívají ukázkové aplikace příkazy `#define` a `#if-#else/#elif-#endif` jazyka C# k selektivní kompilaci a spouštění různých částí vzorového kódu.</span><span class="sxs-lookup"><span data-stu-id="0f659-153">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` C# statements to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="0f659-154">U ukázek, které používají tento přístup, nastavte příkaz `#define` na začátku souborů C# na symbol přidružený ke scénáři, který chcete spustit.</span><span class="sxs-lookup"><span data-stu-id="0f659-154">For those samples that make use of this approach, set the `#define` statement at the top of the C# files to the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="0f659-155">U některých ukázek musíte kvůli spuštění scénáře nastavit tento symbol na začátku několika souborů.</span><span class="sxs-lookup"><span data-stu-id="0f659-155">Some samples require setting the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="0f659-156">Například následující seznam symbolů `#define` udává, že jsou dostupné čtyři scénáře (jeden scénář na symbol).</span><span class="sxs-lookup"><span data-stu-id="0f659-156">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="0f659-157">Při aktuální konfiguraci ukázky se spustí scénář `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="0f659-157">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="0f659-158">Pokud chcete ukázku změnit tak, aby se spustil scénář `ExpandDefault`, definujte symbol `ExpandDefault` a zbývající symboly nechejte zakomentované:</span><span class="sxs-lookup"><span data-stu-id="0f659-158">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="0f659-159">Další informace o používání [direktiv preprocesoru C#](/dotnet/csharp/language-reference/preprocessor-directives/) k selektivní kompilaci částí kódu najdete v článku [#define (referenční dokumentace jazyka C# )](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) a [#if (referenční dokumentace jazyka C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="0f659-159">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="0f659-160">Oblasti ve vzorovém kódu</span><span class="sxs-lookup"><span data-stu-id="0f659-160">Regions in sample code</span></span>

<span data-ttu-id="0f659-161">Některé ukázkové aplikace obsahují úseky kódu, které jsou uzavřené mezi příkazy jazyka C# [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) a [#end-region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion).</span><span class="sxs-lookup"><span data-stu-id="0f659-161">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#end-region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# statements.</span></span> <span data-ttu-id="0f659-162">Systém pro sestavování dokumentace vkládá tyto oblasti do zobrazených témat dokumentace.</span><span class="sxs-lookup"><span data-stu-id="0f659-162">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="0f659-163">Názvy oblastí obvykle obsahují slovo „snippet“.</span><span class="sxs-lookup"><span data-stu-id="0f659-163">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="0f659-164">Následující příklad ukazuje oblast s názvem `snippet_FilterInCode`:</span><span class="sxs-lookup"><span data-stu-id="0f659-164">The following example shows a region named `snippet_FilterInCode`:</span></span>

```csharp
#region snippet_FilterInCode
WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter("System", LogLevel.Debug)
            .AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Trace))
            .Build();
#endregion
```

<span data-ttu-id="0f659-165">Na předchozí fragment kódu C# odkazuje soubor Markdown tohoto tématu pomocí následujícího řádku:</span><span class="sxs-lookup"><span data-stu-id="0f659-165">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_FilterInCode)]
```

<span data-ttu-id="0f659-166">Příkazy `#region` a `#end-region`, do kterých je kód uzavřený, můžete bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="0f659-166">You may safely ignore (or remove) the `#region` and `#end-region` statements that surround the code.</span></span> <span data-ttu-id="0f659-167">Pokud budete chtít spustit vzorové scénáře popsané v tomto tématu, neměňte kód uvnitř těchto příkazů.</span><span class="sxs-lookup"><span data-stu-id="0f659-167">Don't alter the code within these statements if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="0f659-168">Při experimentování s jinými scénáři můžete tento kód klidně pozměnit.</span><span class="sxs-lookup"><span data-stu-id="0f659-168">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="0f659-169">Další informace najdete v článku [Příspěvky k dokumentaci rozhraní ASP.NET: fragmenty kódu](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="0f659-169">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0f659-170">Další kroky</span><span class="sxs-lookup"><span data-stu-id="0f659-170">Next steps</span></span>

<span data-ttu-id="0f659-171">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="0f659-171">For more information, see the following resources:</span></span>

* [<span data-ttu-id="0f659-172">Začínáme se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="0f659-172">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="0f659-173">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f659-173">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="0f659-174">[Týdenní přehled novinek v komunitě ASP.NET](https://live.asp.net/) se zabývá pokrokem v týmových projektech a týmovými plány.</span><span class="sxs-lookup"><span data-stu-id="0f659-174">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="0f659-175">Nabízí nové blogové příspěvky a software třetích stran.</span><span class="sxs-lookup"><span data-stu-id="0f659-175">It features new blogs and third-party software.</span></span>
