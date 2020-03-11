---
title: Správa paměti a vzory v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak je paměť spravovaná v ASP.NET Core a jak funguje uvolňování paměti (GC).
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: performance/memory
ms.openlocfilehash: 0ae367e954e21e2f696a3b292fa64f1d2dba98ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667025"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="0f8cb-103">Správa paměti a uvolňování paměti (GC) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f8cb-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="0f8cb-104">Od [Sébastien ROS](https://github.com/sebastienros) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0f8cb-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0f8cb-105">Správa paměti je složitá, i ve spravovaném rozhraní, jako je .NET.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="0f8cb-106">Analýza a porozumění problémům s pamětí může být náročné.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="0f8cb-107">Tento článek:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-107">This article:</span></span>

* <span data-ttu-id="0f8cb-108">Bylo motivované mnoha *nevrácenými* chybami a uvolňováním paměti, které *nefunguje* .</span><span class="sxs-lookup"><span data-stu-id="0f8cb-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="0f8cb-109">Většina těchto problémů byla způsobena tím, že nerozumíte tomu, jak spotřeba paměti funguje v rozhraní .NET Core, nebo pokud nerozumíte, jak se měří.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="0f8cb-110">Ukazuje problematické využití paměti a navrhuje alternativní přístupy.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="0f8cb-111">Jak funguje uvolňování paměti (GC) v .NET Core</span><span class="sxs-lookup"><span data-stu-id="0f8cb-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="0f8cb-112">GC přiděluje segmenty haldy, kde jsou jednotlivé segmenty souvislým rozsahem paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="0f8cb-113">Objekty umístěné v haldě jsou rozdělené do jedné ze 3 generací: 0, 1 nebo 2.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="0f8cb-114">Generace Určuje četnost, po kterou se GC pokusí uvolnit paměť na spravovaných objektech, na které už neodkazuje aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="0f8cb-115">Nižší očíslované generace jsou GC častěji.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="0f8cb-116">Objekty jsou přesunuty z jedné generace do jiné na základě jejich životnosti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="0f8cb-117">Jelikož objekty jsou po dobu provozu, jsou přesunuty do vyšší generace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="0f8cb-118">Jak už bylo uvedeno výše, vyšší generace paměti GC jsou méně často.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="0f8cb-119">Krátkodobé dlouhodobé objekty vždy zůstávají v generaci 0.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="0f8cb-120">Například objekty, které jsou odkazovány během životnosti webové žádosti, jsou krátkodobé.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="0f8cb-121">[Jednoznačné](xref:fundamentals/dependency-injection#service-lifetimes) úrovně aplikace se obvykle migrují do generace 2.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="0f8cb-122">Při spuštění aplikace ASP.NET Core se v GC:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="0f8cb-123">Rezervuje určitou paměť pro počáteční segmenty haldy.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="0f8cb-124">Po načtení modulu runtime potvrdí malou část paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="0f8cb-125">Předchozí přidělení paměti se provádí z důvodů výkonu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="0f8cb-126">Výhoda výkonu přichází z segmentů haldy v souvislé paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="0f8cb-127">Vyvolejte GC. Shromáždění</span><span class="sxs-lookup"><span data-stu-id="0f8cb-127">Call GC.Collect</span></span>

<span data-ttu-id="0f8cb-128">Volání [GC. Shromažďovat](xref:System.GC.Collect*) explicitně:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="0f8cb-129">By **se nemělo provádět v** produkčních ASP.NET Corech aplikacích.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="0f8cb-130">Je užitečné při zkoumání nevracení paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="0f8cb-131">Při šetření ověří GC, že se z paměti odebraly všechny objekty dangling, takže je možné změřit paměť.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="0f8cb-132">Analýza využití paměti aplikací</span><span class="sxs-lookup"><span data-stu-id="0f8cb-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="0f8cb-133">Vyhrazené nástroje mohou pomoci analyzovat využití paměti:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="0f8cb-134">Počítání odkazů na objekty</span><span class="sxs-lookup"><span data-stu-id="0f8cb-134">Counting object references</span></span>
- <span data-ttu-id="0f8cb-135">Měření vlivu GC na využití procesoru</span><span class="sxs-lookup"><span data-stu-id="0f8cb-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="0f8cb-136">Měření paměťového prostoru používaného pro každou generaci</span><span class="sxs-lookup"><span data-stu-id="0f8cb-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="0f8cb-137">K analýze využití paměti použijte následující nástroje:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="0f8cb-138">[dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): lze použít na produkčních počítačích.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="0f8cb-139">Analýza využití paměti bez ladicího programu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f8cb-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="0f8cb-140">Využití paměti profilu v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f8cb-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="0f8cb-141">Zjišťování problémů s pamětí</span><span class="sxs-lookup"><span data-stu-id="0f8cb-141">Detecting memory issues</span></span>

<span data-ttu-id="0f8cb-142">Správce úloh se dá použít k získání nápadu o tom, kolik paměti aplikace ASP.NET používá.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="0f8cb-143">Hodnota paměti Správce úloh:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="0f8cb-144">Představuje velikost paměti, která je používána procesem ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="0f8cb-145">Zahrnuje živé objekty aplikace a další uživatele paměti, jako je využití nativní paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="0f8cb-146">Pokud se hodnota paměti Správce úloh zvětšuje na neomezenou dobu a nikdy nesloučí, aplikace bude mít nevrácenou paměť.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="0f8cb-147">Následující části ukazují a vysvětlují několik vzorců využití paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="0f8cb-148">Ukázková aplikace pro zobrazení využití paměti</span><span class="sxs-lookup"><span data-stu-id="0f8cb-148">Sample display memory usage app</span></span>

<span data-ttu-id="0f8cb-149">[Ukázková aplikace MemoryLeak](https://github.com/sebastienros/memoryleak) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="0f8cb-150">Aplikace MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="0f8cb-151">Obsahuje diagnostický kontroler, který shromažďuje data paměti a GC v reálném čase pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="0f8cb-152">Má stránku index, která zobrazuje data paměti a GC.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="0f8cb-153">Stránka index se aktualizuje každou sekundu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="0f8cb-154">Obsahuje kontroler rozhraní API, který poskytuje různé vzory zatížení paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="0f8cb-155">Nástroj není podporovaným nástrojem, ale dá se použít k zobrazení vzorců využití paměti aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="0f8cb-156">Spusťte MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-156">Run MemoryLeak.</span></span> <span data-ttu-id="0f8cb-157">Přidělená paměť se pomalu zvyšuje, dokud nedojde k GC.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="0f8cb-158">Paměť roste, protože nástroj přiděluje vlastní objekt pro zachycení dat.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="0f8cb-159">Následující obrázek ukazuje stránku indexu MemoryLeak, když dojde k GC 0. generace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="0f8cb-160">Graf zobrazuje 0 RPS (počet požadavků za sekundu), protože nebyly volány koncové body rozhraní API z řadiče rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![předchozí graf](memory/_static/0RPS.png)

<span data-ttu-id="0f8cb-162">Graf zobrazuje dvě hodnoty využití paměti:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="0f8cb-163">Přiděleno: množství paměti obsazené spravovanými objekty</span><span class="sxs-lookup"><span data-stu-id="0f8cb-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="0f8cb-164">[Pracovní sada](/windows/win32/memory/working-set): sada stránek ve virtuálním adresním prostoru procesu, které jsou aktuálně rezidenty ve fyzické paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="0f8cb-165">Zobrazená pracovní sada má stejnou hodnotu jako správce úloh.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="0f8cb-166">Přechodné objekty</span><span class="sxs-lookup"><span data-stu-id="0f8cb-166">Transient objects</span></span>

<span data-ttu-id="0f8cb-167">Následující rozhraní API vytvoří instanci řetězce 10 KB a vrátí ji klientovi.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="0f8cb-168">V každé žádosti je nový objekt přidělen v paměti a zapsán do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="0f8cb-169">Řetězce jsou uloženy v rozhraní .NET jako UTF-16 znaků, takže každý znak trvá 2 bajty paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="0f8cb-170">Následující graf se generuje s poměrně malým zatížením, aby se zobrazilo, jaký vliv má GC na přidělení paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![předchozí graf](memory/_static/bigstring.png)

<span data-ttu-id="0f8cb-172">Předchozí graf znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-172">The preceding chart shows:</span></span>

* <span data-ttu-id="0f8cb-173">4K RPS (počet požadavků za sekundu).</span><span class="sxs-lookup"><span data-stu-id="0f8cb-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="0f8cb-174">K kolekcím GC generace 0 dochází každé dvě sekundy.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="0f8cb-175">Pracovní sada je konstanta o velikosti přibližně 500 MB.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="0f8cb-176">PROCESOR je 12%.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-176">CPU is 12%.</span></span>
* <span data-ttu-id="0f8cb-177">Spotřeba paměti a verze (prostřednictvím GC) je stabilní.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="0f8cb-178">Následující graf se seřizuje s maximální propustností, kterou může počítač zpracovat.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![předchozí graf](memory/_static/bigstring2.png)

<span data-ttu-id="0f8cb-180">Předchozí graf znázorňuje:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-180">The preceding chart shows:</span></span>

* <span data-ttu-id="0f8cb-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="0f8cb-181">22K RPS</span></span>
* <span data-ttu-id="0f8cb-182">Kolekce GC pro generaci 0 se probíhají několikrát za sekundu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="0f8cb-183">Kolekce 1. generace se spouštějí, protože aplikace přidělila mnohem více paměti za sekundu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="0f8cb-184">Pracovní sada je konstanta o velikosti přibližně 500 MB.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="0f8cb-185">PROCESOR je 33%.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-185">CPU is 33%.</span></span>
* <span data-ttu-id="0f8cb-186">Spotřeba paměti a verze (prostřednictvím GC) je stabilní.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="0f8cb-187">PROCESOR (33%) není přetížený, takže uvolňování paměti může udržovat vysoký počet přidělení.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="0f8cb-188">GC pro pracovní stanice vs. GC serveru</span><span class="sxs-lookup"><span data-stu-id="0f8cb-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="0f8cb-189">Systém uvolňování paměti .NET má dva různé režimy:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="0f8cb-190">**GC pracovní stanice**: optimalizováno pro plochu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="0f8cb-191">**GC serveru**.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-191">**Server GC**.</span></span> <span data-ttu-id="0f8cb-192">Výchozí GC pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="0f8cb-193">Optimalizováno pro server.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-193">Optimized for the server.</span></span>

<span data-ttu-id="0f8cb-194">Režim GC se dá explicitně nastavit v souboru projektu nebo v souboru *runtimeconfig. JSON* publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="0f8cb-195">Následující kód ukazuje nastavení `ServerGarbageCollection` v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="0f8cb-196">Změna `ServerGarbageCollection` v souboru projektu vyžaduje opětovné vytvoření aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="0f8cb-197">**Poznámka:** Uvolňování paměti serveru **není k dispozici na** počítačích s jedním jádrem.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="0f8cb-198">Další informace naleznete v tématu <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="0f8cb-199">Následující obrázek ukazuje profil paměti v rámci 5K RPS pomocí GC pracovní stanice.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![předchozí graf](memory/_static/workstation.png)

<span data-ttu-id="0f8cb-201">Rozdíly mezi tímto grafem a verzí serveru jsou významné:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="0f8cb-202">Pracovní sada klesne z 500 MB na 70 MB.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="0f8cb-203">Uvolňování paměti v GC vyprší víckrát za sekundu místo každé dvě sekundy.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="0f8cb-204">Uvolňování paměti se zamítá z 300 MB na 10 MB.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="0f8cb-205">V typických prostředích webového serveru je využití procesoru důležitější než paměť, proto je server GC lepší.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="0f8cb-206">Pokud je využití paměti vysoké a využití procesoru je poměrně nízké, může být UVOLŇOVÁNí paměti pracovní stanice více výkonné.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="0f8cb-207">Například vysoká hustota hostující několik webových aplikací, kde je paměť omezených.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-207">For example, high density hosting several web apps where memory is scarce.</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="0f8cb-208">Trvalé odkazy na objekty</span><span class="sxs-lookup"><span data-stu-id="0f8cb-208">Persistent object references</span></span>

<span data-ttu-id="0f8cb-209">GC nemůže uvolnit objekty, na které se odkazuje.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-209">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="0f8cb-210">Objekty, na které se odkazuje, ale které už nepotřebují, mají za následek nevracení paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-210">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="0f8cb-211">Pokud aplikace často přiděluje objekty a neuvolní je až po jejich nepotřebení, využití paměti se v průběhu času zvýší.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-211">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="0f8cb-212">Následující rozhraní API vytvoří instanci řetězce 10 KB a vrátí ji klientovi.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-212">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="0f8cb-213">Rozdíl s předchozím příkladem je, že tato instance je odkazována statickým členem, což znamená, že není nikdy k dispozici pro shromažďování.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-213">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="0f8cb-214">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-214">The preceding code:</span></span>

* <span data-ttu-id="0f8cb-215">Je příkladem typické nevrácení paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-215">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="0f8cb-216">S častými voláními způsobí, že se paměť aplikace zvětšuje, dokud proces neselže s výjimkou `OutOfMemory`.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-216">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![předchozí graf](memory/_static/eternal.png)

<span data-ttu-id="0f8cb-218">Na předchozím obrázku:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-218">In the preceding image:</span></span>

* <span data-ttu-id="0f8cb-219">Zátěžové testování `/api/staticstring` koncový bod způsobuje lineární zvětšení paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-219">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="0f8cb-220">GC se pokusí uvolnit paměť, protože tlak paměti roste, voláním kolekce 2. generace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-220">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="0f8cb-221">GC nemůže uvolnit nevrácenou paměť.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-221">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="0f8cb-222">Přidělená a pracovní sada se zvýšila s časem.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-222">Allocated and working set increase with time.</span></span>

<span data-ttu-id="0f8cb-223">Některé scénáře, jako je například ukládání do mezipaměti, vyžadují, aby byly uloženy odkazy na objekty, dokud tlak vynutí uvolnění paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-223">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="0f8cb-224">Třídu <xref:System.WeakReference> lze použít pro tento typ kódu pro ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-224">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="0f8cb-225">Objekt `WeakReference` je shromážděn v části tlaky paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-225">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="0f8cb-226">Výchozí implementace <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> používá `WeakReference`.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-226">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="0f8cb-227">Nativní paměť</span><span class="sxs-lookup"><span data-stu-id="0f8cb-227">Native memory</span></span>

<span data-ttu-id="0f8cb-228">Některé objekty .NET Core spoléhají na nativní paměť.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-228">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="0f8cb-229">UVOLŇOVÁNí paměti **nelze shromáždit** nativní paměť.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-229">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="0f8cb-230">Objekt .NET s použitím nativní paměti musí uvolnit pomocí nativního kódu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-230">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="0f8cb-231">Rozhraní .NET poskytuje rozhraní <xref:System.IDisposable>, které vývojářům umožňuje uvolnit nativní paměť.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-231">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="0f8cb-232">I v případě, že není volána <xref:System.IDisposable.Dispose*>, správně implementované třídy volají `Dispose` při spuštění [finalizační metody](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .</span><span class="sxs-lookup"><span data-stu-id="0f8cb-232">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="0f8cb-233">Vezměte v úvahu následující kód:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-233">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="0f8cb-234">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) je spravovaná třída, takže všechny instance budou shromážděny na konci žádosti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-234">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="0f8cb-235">Následující obrázek ukazuje profil paměti a průběžně vyvolal rozhraní API pro `fileprovider`.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-235">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![předchozí graf](memory/_static/fileprovider.png)

<span data-ttu-id="0f8cb-237">Předchozí graf znázorňuje zjevné problémy s implementací této třídy, protože zajišťuje zvýšení využití paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-237">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="0f8cb-238">Jedná se o známý problém, který se sleduje v [tomto problému](https://github.com/dotnet/aspnetcore/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="0f8cb-238">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="0f8cb-239">Stejná netěsnost by mohla být provedena v uživatelském kódu, a to jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-239">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="0f8cb-240">Neuvolňuje třídu správně.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-240">Not releasing the class correctly.</span></span>
* <span data-ttu-id="0f8cb-241">Forgetting vyvolat metodu `Dispose`závislých objektů, které by měly být uvolněny.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-241">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="0f8cb-242">Halda velkých objektů</span><span class="sxs-lookup"><span data-stu-id="0f8cb-242">Large objects heap</span></span>

<span data-ttu-id="0f8cb-243">Časté přiřazování paměti/bezplatné cykly můžou rozdělit paměť, zejména při přidělování velkých bloků paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-243">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="0f8cb-244">Objekty jsou přidělovány v souvislém bloku paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-244">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="0f8cb-245">Aby se zmírnila fragmentace, když GC uvolní paměť, trys ji defragmentovat.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-245">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="0f8cb-246">Tento proces se nazývá **komprimace**.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-246">This process is called **compaction**.</span></span> <span data-ttu-id="0f8cb-247">Komprimace zahrnuje přesun objektů.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-247">Compaction involves moving objects.</span></span> <span data-ttu-id="0f8cb-248">Přesunutí velkých objektů ukládá snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-248">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="0f8cb-249">Z tohoto důvodu GC vytvoří speciální zónu paměti pro _velké_ objekty označované jako [halda velkých objektů](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="0f8cb-249">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="0f8cb-250">Objekty, které jsou větší než 85 000 bajtů (přibližně 83 KB), jsou:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-250">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="0f8cb-251">Umístit do LOH.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-251">Placed on the LOH.</span></span>
* <span data-ttu-id="0f8cb-252">Není zkomprimováno.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-252">Not compacted.</span></span>
* <span data-ttu-id="0f8cb-253">Shromážděno během generace 2 GC.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-253">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="0f8cb-254">Když je LOH plný, GC spustí kolekci 2. generace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-254">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="0f8cb-255">Kolekce 2. generace:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-255">Generation 2 collections:</span></span>

* <span data-ttu-id="0f8cb-256">Jsou ve své podstatě pomalé.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-256">Are inherently slow.</span></span>
* <span data-ttu-id="0f8cb-257">Navíc se účtují náklady na aktivaci kolekce na všech ostatních generacích.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-257">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="0f8cb-258">Následující kód komprimuje LOH hned:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-258">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="0f8cb-259">Informace o komprimaci LOH najdete v tématu <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-259">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="0f8cb-260">V kontejnerech pomocí .NET Core 3,0 a novějších se LOH automaticky zkomprimuje.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-260">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="0f8cb-261">Toto chování ilustruje následující rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-261">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="0f8cb-262">Následující graf znázorňuje profil paměti volajícího koncového bodu `/api/loh/84975` v rámci maximálního zatížení:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-262">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![předchozí graf](memory/_static/loh1.png)

<span data-ttu-id="0f8cb-264">Následující graf znázorňuje profil paměti pro volání `/api/loh/84976`ho koncového bodu a přidělení *pouze jednoho bajtu*:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-264">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![předchozí graf](memory/_static/loh2.png)

<span data-ttu-id="0f8cb-266">Poznámka: struktura `byte[]` má režijní bajty.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-266">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="0f8cb-267">To je důvod, proč 84 976 bajtů spouští limit 85 000.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-267">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="0f8cb-268">Porovnání dvou předchozích grafů:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-268">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="0f8cb-269">Pracovní sada je pro oba scénáře podobná, přibližně 450 MB.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-269">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="0f8cb-270">V části požadavky LOH (84 975 bajtů) se zobrazí hlavně kolekce 0. generace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-270">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="0f8cb-271">Požadavky over LOH generují kolekce konstant generace 2.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-271">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="0f8cb-272">Kolekce 2. generace jsou nákladné.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-272">Generation 2 collections are expensive.</span></span> <span data-ttu-id="0f8cb-273">Je potřeba více PROCESORů a propustnost se sníží téměř 50%.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-273">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="0f8cb-274">Dočasné velké objekty jsou obzvláště problematické, protože způsobují Gen2 GC.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-274">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="0f8cb-275">Pro maximální výkon by se mělo minimalizovat použití velkých objektů.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-275">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="0f8cb-276">Pokud je to možné, rozdělte velké objekty.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-276">If possible, split up large objects.</span></span> <span data-ttu-id="0f8cb-277">Například middleware pro [ukládání odpovědí do mezipaměti](xref:performance/caching/response) v ASP.NET Core rozdělí položky mezipaměti do bloků menších než 85 000 bajtů.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-277">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="0f8cb-278">Následující odkazy znázorňují ASP.NET Core přístup k udržení objektů pod limitem LOH:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-278">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="0f8cb-279">ResponseCaching/Streams/StreamUtilities. cs</span><span class="sxs-lookup"><span data-stu-id="0f8cb-279">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="0f8cb-280">ResponseCaching/MemoryResponseCache. cs</span><span class="sxs-lookup"><span data-stu-id="0f8cb-280">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="0f8cb-281">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-281">For more information, see:</span></span>

* [<span data-ttu-id="0f8cb-282">Nekrytá halda Large Object</span><span class="sxs-lookup"><span data-stu-id="0f8cb-282">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="0f8cb-283">Halda velkých objektů</span><span class="sxs-lookup"><span data-stu-id="0f8cb-283">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="0f8cb-284">HttpClient</span><span class="sxs-lookup"><span data-stu-id="0f8cb-284">HttpClient</span></span>

<span data-ttu-id="0f8cb-285">Nesprávné použití <xref:System.Net.Http.HttpClient> může způsobit nevracení prostředků.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-285">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="0f8cb-286">Systémové prostředky, jako jsou databázová připojení, sokety, popisovače souborů atd.:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-286">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="0f8cb-287">Je více omezených než paměť.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-287">Are more scarce than memory.</span></span>
* <span data-ttu-id="0f8cb-288">Jsou více problematické při nevracení paměti.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-288">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="0f8cb-289">Zkušení vývojáři rozhraní .NET vědí, že volají <xref:System.IDisposable.Dispose*> objektů, které implementují <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-289">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="0f8cb-290">Nevyřazuje objekty, které implementují `IDisposable` obvykle mají za následek nevrácenou paměť nebo nevrácené systémové prostředky.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-290">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="0f8cb-291">`HttpClient` implementuje `IDisposable`, **ale neměla by být** uvolněna při každém vyvolání.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-291">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="0f8cb-292">Místo toho je třeba znovu použít `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-292">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="0f8cb-293">Následující koncový bod vytvoří novou instanci `HttpClient` a odstraní ji na každý požadavek:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-293">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="0f8cb-294">Při načtení se zaprotokolují následující chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-294">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="0f8cb-295">I když jsou instance `HttpClient` vyřazeny, vlastní síťové připojení bude nějakou dobu trvat, než operační systém uvolní.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-295">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="0f8cb-296">Průběžným vytvářením nových připojení dojde k _vyčerpání portů_ .</span><span class="sxs-lookup"><span data-stu-id="0f8cb-296">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="0f8cb-297">Každé připojení klienta vyžaduje svůj vlastní port klienta.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-297">Each client connection requires its own client port.</span></span>

<span data-ttu-id="0f8cb-298">Jedním ze způsobů, jak zabránit vyčerpání portů, je použít stejnou instanci `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-298">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="0f8cb-299">Instance `HttpClient` se uvolní při zastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-299">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="0f8cb-300">Tento příklad ukazuje, že po každém použití by mělo být uvolněno každý prostředek na jedno použití.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-300">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="0f8cb-301">Pro lepší zpracování životnosti `HttpClient` instance se podívejte na následující:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-301">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="0f8cb-302">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="0f8cb-302">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="0f8cb-303">Blog o továrně HTTPClient</span><span class="sxs-lookup"><span data-stu-id="0f8cb-303">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="0f8cb-304">Sdružování objektů</span><span class="sxs-lookup"><span data-stu-id="0f8cb-304">Object pooling</span></span>

<span data-ttu-id="0f8cb-305">Předchozí příklad ukázal, jak může být instance `HttpClient` vytvořená staticky a znovu použita všemi požadavky.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-305">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="0f8cb-306">Při opakovaném použití se nebudete moci dostat z prostředků.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-306">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="0f8cb-307">Sdružování objektů:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-307">Object pooling:</span></span>

* <span data-ttu-id="0f8cb-308">Používá vzor opakovaného použití.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-308">Uses the reuse pattern.</span></span>
* <span data-ttu-id="0f8cb-309">Je určený pro objekty, které je náročné vytvořit.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-309">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="0f8cb-310">Fond je kolekce předem inicializovaných objektů, které mohou být rezervovány a vydány v rámci vláken.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-310">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="0f8cb-311">Fondy můžou definovat pravidla přidělování, jako jsou limity, předdefinované velikosti nebo míry růstu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-311">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="0f8cb-312">Balíček NuGet [Microsoft. Extensions. fond objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) obsahuje třídy, které vám pomůžou tyto fondy spravovat.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-312">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="0f8cb-313">Následující koncový bod rozhraní API vytvoří instanci `byte` vyrovnávací paměti, která je vyplněna náhodnými čísly na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-313">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="0f8cb-314">Následující zobrazení grafu volá předchozí rozhraní API se středním zatížením:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-314">The following chart display calling the preceding API with moderate load:</span></span>

![předchozí graf](memory/_static/array.png)

<span data-ttu-id="0f8cb-316">V předchozím grafu se kolekce generace 0 nevyskytují přibližně jednou za sekundu.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-316">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="0f8cb-317">Předchozí kód může být optimalizován přidáním vyrovnávací paměti `byte` pomocí [ArrayPool\<t >](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="0f8cb-317">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="0f8cb-318">Statická instance se opakovaně používá napříč požadavky.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-318">A static instance is reused across requests.</span></span>

<span data-ttu-id="0f8cb-319">To se liší od tohoto přístupu je to, že objekt ve fondu je vrácen z rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-319">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="0f8cb-320">To znamená:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-320">That means:</span></span>

* <span data-ttu-id="0f8cb-321">Objekt je mimo váš ovládací prvek, jakmile se vrátíte z metody.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-321">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="0f8cb-322">Nemůžete uvolnit objekt.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-322">You can't release the object.</span></span>

<span data-ttu-id="0f8cb-323">Nastavení vyřazení objektu:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-323">To set up disposal of the object:</span></span>

* <span data-ttu-id="0f8cb-324">Zapouzdřuje pole ve fondu na jedno a více objektů.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-324">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="0f8cb-325">Zaregistrujte objekt ve fondu s [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="0f8cb-325">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="0f8cb-326">`RegisterForDispose` se postará o volání `Dispose`na cílovém objektu tak, aby se uvolnilo jenom po dokončení požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-326">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="0f8cb-327">Použití stejného zatížení jako nesdružené verze vede k následujícímu grafu:</span><span class="sxs-lookup"><span data-stu-id="0f8cb-327">Applying the same load as the non-pooled version results in the following chart:</span></span>

![předchozí graf](memory/_static/pooledarray.png)

<span data-ttu-id="0f8cb-329">Hlavní rozdíl je přidělený bajtů a jako důsledek je to mnohem méně kolekcí 0. generace.</span><span class="sxs-lookup"><span data-stu-id="0f8cb-329">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0f8cb-330">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0f8cb-330">Additional resources</span></span>

* [<span data-ttu-id="0f8cb-331">Uvolňování paměti</span><span class="sxs-lookup"><span data-stu-id="0f8cb-331">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="0f8cb-332">Porozumění různým režimům GC s Vizualizérm souběžnosti</span><span class="sxs-lookup"><span data-stu-id="0f8cb-332">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="0f8cb-333">Nekrytá halda Large Object</span><span class="sxs-lookup"><span data-stu-id="0f8cb-333">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="0f8cb-334">Halda velkých objektů</span><span class="sxs-lookup"><span data-stu-id="0f8cb-334">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
