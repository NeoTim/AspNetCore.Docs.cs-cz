---
title: Správa paměti a vzory v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak je paměť spravována v ASP.NET Core a jak funguje systém uvolňování paměti (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440945"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="84933-103">Správa paměti a uvolňování paměti (GC) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84933-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="84933-104">[Sébastien Ros](https://github.com/sebastienros) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84933-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="84933-105">Správa paměti je složitá, a to i ve spravovaném rámci, jako je rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="84933-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="84933-106">Analýza a pochopení problémů s pamětí může být náročné.</span><span class="sxs-lookup"><span data-stu-id="84933-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="84933-107">Tento článek:</span><span class="sxs-lookup"><span data-stu-id="84933-107">This article:</span></span>

* <span data-ttu-id="84933-108">Byl motivován mnoha *nevracení paměti* a *GC nefunguje* problémy.</span><span class="sxs-lookup"><span data-stu-id="84933-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="84933-109">Většina těchto problémů byla způsobena tím, že nechápu, jak funguje spotřeba paměti v .NET Core, nebo nepochopení, jak se měří.</span><span class="sxs-lookup"><span data-stu-id="84933-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="84933-110">Ukazuje problematické využití paměti a navrhuje alternativní přístupy.</span><span class="sxs-lookup"><span data-stu-id="84933-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="84933-111">Jak funguje uvolňování paměti (GC) v rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="84933-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="84933-112">Gc přiděluje segmenty haldy, kde každý segment je souvislý rozsah paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="84933-113">Objekty umístěné v haldě jsou rozděleny do jedné ze 3 generací: 0, 1 nebo 2.</span><span class="sxs-lookup"><span data-stu-id="84933-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="84933-114">Generování určuje frekvenci GC pokusí uvolnit paměť na spravované objekty, které jsou již odkazuje aplikace.</span><span class="sxs-lookup"><span data-stu-id="84933-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="84933-115">Nižší číslované generace jsou GC'd častěji.</span><span class="sxs-lookup"><span data-stu-id="84933-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="84933-116">Objekty jsou přesunuty z jedné generace do druhé na základě jejich životnosti.</span><span class="sxs-lookup"><span data-stu-id="84933-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="84933-117">Jak objekty žijí déle, jsou přesunuty do vyšší generace.</span><span class="sxs-lookup"><span data-stu-id="84933-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="84933-118">Jak již bylo zmíněno dříve, vyšší generace jsou GC'd méně často.</span><span class="sxs-lookup"><span data-stu-id="84933-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="84933-119">Krátkodobé žil objekty vždy zůstávají v generaci 0.</span><span class="sxs-lookup"><span data-stu-id="84933-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="84933-120">Například objekty, které jsou odkazovány během životnosti webové žádosti jsou krátkodobé.</span><span class="sxs-lookup"><span data-stu-id="84933-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="84933-121">[Singletons](xref:fundamentals/dependency-injection#service-lifetimes) na úrovni aplikace obvykle migrují do generace 2.</span><span class="sxs-lookup"><span data-stu-id="84933-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="84933-122">Když se spustí aplikace ASP.NET Core, gc:</span><span class="sxs-lookup"><span data-stu-id="84933-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="84933-123">Rezervuje některé paměti pro počáteční segmenty haldy.</span><span class="sxs-lookup"><span data-stu-id="84933-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="84933-124">Potvrdí malou část paměti při načtení běhu.</span><span class="sxs-lookup"><span data-stu-id="84933-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="84933-125">Předchozí přidělení paměti se provádí z důvodů výkonu.</span><span class="sxs-lookup"><span data-stu-id="84933-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="84933-126">Výhoda výkonu pochází z haldy segmenty v souvislé paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="84933-127">Volejte GC. Shromažďovat</span><span class="sxs-lookup"><span data-stu-id="84933-127">Call GC.Collect</span></span>

<span data-ttu-id="84933-128">Volám [GC. Shromážděte](xref:System.GC.Collect*) explicitně:</span><span class="sxs-lookup"><span data-stu-id="84933-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="84933-129">**Nemělo** by se to provádět produkčními aplikacemi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="84933-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="84933-130">Je užitečné při zkoumání nevracení paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="84933-131">Při zkoumání ověří GC odstranil všechny visící objekty z paměti, takže paměť může být měřena.</span><span class="sxs-lookup"><span data-stu-id="84933-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="84933-132">Analýza využití paměti aplikace</span><span class="sxs-lookup"><span data-stu-id="84933-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="84933-133">Vyhrazené nástroje mohou pomoci analyzovat využití paměti:</span><span class="sxs-lookup"><span data-stu-id="84933-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="84933-134">Odkazy na objekty inventury</span><span class="sxs-lookup"><span data-stu-id="84933-134">Counting object references</span></span>
- <span data-ttu-id="84933-135">Měření, jaký vliv má gc na využití procesoru</span><span class="sxs-lookup"><span data-stu-id="84933-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="84933-136">Měření paměťového prostoru používaného pro každou generaci</span><span class="sxs-lookup"><span data-stu-id="84933-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="84933-137">K analýze využití paměti použijte následující nástroje:</span><span class="sxs-lookup"><span data-stu-id="84933-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="84933-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Lze použít na výrobních strojích.</span><span class="sxs-lookup"><span data-stu-id="84933-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="84933-139">Analýza využití paměti bez ladicího programu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84933-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="84933-140">Využití paměti profilu v sadě Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84933-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="84933-141">Zjišťování problémů s pamětí</span><span class="sxs-lookup"><span data-stu-id="84933-141">Detecting memory issues</span></span>

<span data-ttu-id="84933-142">Správce úloh lze získat představu o tom, kolik paměti ASP.NET aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="84933-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="84933-143">Hodnota paměti Správce úloh:</span><span class="sxs-lookup"><span data-stu-id="84933-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="84933-144">Představuje množství paměti, která se používá procesem ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="84933-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="84933-145">Zahrnuje živé objekty aplikace a další spotřebitele paměti, jako je například využití nativní paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="84933-146">Pokud hodnota paměti Správce úloh se zvyšuje neomezeně dlouho a nikdy se nevylučuje, aplikace má nevracení paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="84933-147">V následujících částech demonstrovat a vysvětlit několik vzorů využití paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="84933-148">Ukázková aplikace pro využití paměti zobrazení</span><span class="sxs-lookup"><span data-stu-id="84933-148">Sample display memory usage app</span></span>

<span data-ttu-id="84933-149">[Ukázková aplikace MemoryLeak](https://github.com/sebastienros/memoryleak) je k dispozici na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="84933-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="84933-150">Aplikace MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="84933-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="84933-151">Obsahuje diagnostický řadič, který shromažďuje paměť v reálném čase a gc data pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84933-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="84933-152">Má index stránku, která zobrazuje paměť a GC data.</span><span class="sxs-lookup"><span data-stu-id="84933-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="84933-153">Stránka Rejstřík je aktualizována každou sekundu.</span><span class="sxs-lookup"><span data-stu-id="84933-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="84933-154">Obsahuje řadič rozhraní API, který poskytuje různé vzory načítání paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="84933-155">Není však podporovanýnástroj, lze jej však použít k zobrazení vzorců využití paměti aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="84933-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="84933-156">Spusťte únik paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-156">Run MemoryLeak.</span></span> <span data-ttu-id="84933-157">Přidělená paměť se pomalu zvyšuje, dokud nedojde k gc.</span><span class="sxs-lookup"><span data-stu-id="84933-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="84933-158">Paměť se zvyšuje, protože nástroj přiděluje vlastní objekt pro sběr dat.</span><span class="sxs-lookup"><span data-stu-id="84933-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="84933-159">Následující obrázek znázorňuje stránku MemoryLeak Index, když dojde k Gen 0 GC.</span><span class="sxs-lookup"><span data-stu-id="84933-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="84933-160">Graf zobrazuje 0 RPS (Požadavky za sekundu), protože nebyly volány žádné koncové body rozhraní API z řadiče rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="84933-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![předchozí graf](memory/_static/0RPS.png)

<span data-ttu-id="84933-162">Graf zobrazuje dvě hodnoty využití paměti:</span><span class="sxs-lookup"><span data-stu-id="84933-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="84933-163">Přiděleno: velikost paměti obsazené spravovanými objekty</span><span class="sxs-lookup"><span data-stu-id="84933-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="84933-164">[Pracovní sada](/windows/win32/memory/working-set): Sada stránek ve virtuálním adresovém prostoru procesu, které jsou aktuálně rezidentní ve fyzické paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="84933-165">Zobrazená pracovní sada má stejnou hodnotu Správce úloh.</span><span class="sxs-lookup"><span data-stu-id="84933-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="84933-166">Přechodné objekty</span><span class="sxs-lookup"><span data-stu-id="84933-166">Transient objects</span></span>

<span data-ttu-id="84933-167">Následující rozhraní API vytvoří instanci string 10 kB a vrátí ji klientovi.</span><span class="sxs-lookup"><span data-stu-id="84933-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="84933-168">Při každém požadavku je nový objekt přidělen v paměti a zapsán do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="84933-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="84933-169">Řetězce jsou uloženy jako znaky UTF-16 v rozhraní .NET, takže každý znak trvá 2 bajty v paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="84933-170">Následující graf je generován s relativně malé zatížení v ukázat, jak přidělení paměti jsou ovlivněny GC.</span><span class="sxs-lookup"><span data-stu-id="84933-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![předchozí graf](memory/_static/bigstring.png)

<span data-ttu-id="84933-172">Předchozí graf ukazuje:</span><span class="sxs-lookup"><span data-stu-id="84933-172">The preceding chart shows:</span></span>

* <span data-ttu-id="84933-173">4K RPS (Požadavky za sekundu).</span><span class="sxs-lookup"><span data-stu-id="84933-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="84933-174">Generace 0 GC kolekce dojít přibližně každé dvě sekundy.</span><span class="sxs-lookup"><span data-stu-id="84933-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="84933-175">Pracovní sada je konstantní na přibližně 500 MB.</span><span class="sxs-lookup"><span data-stu-id="84933-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="84933-176">CPU je 12%.</span><span class="sxs-lookup"><span data-stu-id="84933-176">CPU is 12%.</span></span>
* <span data-ttu-id="84933-177">Spotřeba paměti a uvolnění (prostřednictvím GC) je stabilní.</span><span class="sxs-lookup"><span data-stu-id="84933-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="84933-178">Následující graf je proveden na maximální propustnost, která může být zpracována strojem.</span><span class="sxs-lookup"><span data-stu-id="84933-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![předchozí graf](memory/_static/bigstring2.png)

<span data-ttu-id="84933-180">Předchozí graf ukazuje:</span><span class="sxs-lookup"><span data-stu-id="84933-180">The preceding chart shows:</span></span>

* <span data-ttu-id="84933-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="84933-181">22K RPS</span></span>
* <span data-ttu-id="84933-182">Generace 0 GC kolekce dojít několikrát za sekundu.</span><span class="sxs-lookup"><span data-stu-id="84933-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="84933-183">Generace 1 kolekce jsou spuštěny, protože aplikace přiděleno výrazně více paměti za sekundu.</span><span class="sxs-lookup"><span data-stu-id="84933-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="84933-184">Pracovní sada je konstantní na přibližně 500 MB.</span><span class="sxs-lookup"><span data-stu-id="84933-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="84933-185">CPU je 33%.</span><span class="sxs-lookup"><span data-stu-id="84933-185">CPU is 33%.</span></span>
* <span data-ttu-id="84933-186">Spotřeba paměti a uvolnění (prostřednictvím GC) je stabilní.</span><span class="sxs-lookup"><span data-stu-id="84933-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="84933-187">Procesor (33%) není nadměrně využívána, proto může uvolňování paměti držet krok s vysokým počtem přidělení.</span><span class="sxs-lookup"><span data-stu-id="84933-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="84933-188">Pracovní stanice GC vs. Server GC</span><span class="sxs-lookup"><span data-stu-id="84933-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="84933-189">Uvolňování paměti .NET má dva různé režimy:</span><span class="sxs-lookup"><span data-stu-id="84933-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="84933-190">**Pracovní stanice GC**: Optimalizováno pro stolní počítače.</span><span class="sxs-lookup"><span data-stu-id="84933-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="84933-191">**Server GC**.</span><span class="sxs-lookup"><span data-stu-id="84933-191">**Server GC**.</span></span> <span data-ttu-id="84933-192">Výchozí gc pro ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="84933-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="84933-193">Optimalizováno pro server.</span><span class="sxs-lookup"><span data-stu-id="84933-193">Optimized for the server.</span></span>

<span data-ttu-id="84933-194">Režim GC lze explicitně nastavit v souboru projektu nebo v souboru *runtimeconfig.json* publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="84933-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="84933-195">V souboru projektu `ServerGarbageCollection` jsou zobrazeny následující značky:</span><span class="sxs-lookup"><span data-stu-id="84933-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="84933-196">Změna `ServerGarbageCollection` v souboru projektu vyžaduje, aby byla aplikace znovu sestavena.</span><span class="sxs-lookup"><span data-stu-id="84933-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="84933-197">**Poznámka:** Uvolňování paměti serveru **není** k dispozici na počítačích s jedním jádrem.</span><span class="sxs-lookup"><span data-stu-id="84933-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="84933-198">Další informace naleznete v tématu <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="84933-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="84933-199">Následující obrázek znázorňuje profil paměti v rámci 5K RPS pomocí pracovní stanice GC.</span><span class="sxs-lookup"><span data-stu-id="84933-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![předchozí graf](memory/_static/workstation.png)

<span data-ttu-id="84933-201">Rozdíly mezi tímto grafem a verzí serveru jsou významné:</span><span class="sxs-lookup"><span data-stu-id="84933-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="84933-202">Pracovní sada klesne z 500 MB na 70 MB.</span><span class="sxs-lookup"><span data-stu-id="84933-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="84933-203">Gc dělá generace 0 kolekce vícekrát za sekundu namísto každé dvě sekundy.</span><span class="sxs-lookup"><span data-stu-id="84933-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="84933-204">GC klesne z 300 MB na 10 MB.</span><span class="sxs-lookup"><span data-stu-id="84933-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="84933-205">V typickém prostředí webového serveru je využití procesoru důležitější než paměť, proto je lepší server GC.</span><span class="sxs-lookup"><span data-stu-id="84933-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="84933-206">Pokud je využití paměti vysoké a využití procesoru je relativně nízká, pracovní stanice GC může být výkonnější.</span><span class="sxs-lookup"><span data-stu-id="84933-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="84933-207">Například vysoká hustota hostování několika webových aplikací, kde je nedostatek paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="84933-208">GC pomocí Dockeru a malých kontejnerů</span><span class="sxs-lookup"><span data-stu-id="84933-208">GC using Docker and small containers</span></span>

<span data-ttu-id="84933-209">Pokud je v jednom počítači spuštěno více kontejnerizovaných aplikací, může být pracovní stanice GC více než server GC.</span><span class="sxs-lookup"><span data-stu-id="84933-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="84933-210">Další informace naleznete [v tématu Spuštění s serverem GC v malém kontejneru](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) a [spuštěno se serverem GC v části 1 scénáře malého kontejneru – pevný limit pro haldu GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="84933-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="84933-211">Trvalé odkazy na objekty</span><span class="sxs-lookup"><span data-stu-id="84933-211">Persistent object references</span></span>

<span data-ttu-id="84933-212">Gc nemůže uvolnit objekty, které jsou odkazovány.</span><span class="sxs-lookup"><span data-stu-id="84933-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="84933-213">Objekty, které jsou odkazovány, ale již nejsou potřeba, mají za následek nevracení paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="84933-214">Pokud aplikace často přiděluje objekty a nepodaří je uvolnit poté, co již nejsou potřeba, využití paměti se časem zvýší.</span><span class="sxs-lookup"><span data-stu-id="84933-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="84933-215">Následující rozhraní API vytvoří instanci string 10 kB a vrátí ji klientovi.</span><span class="sxs-lookup"><span data-stu-id="84933-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="84933-216">Rozdíl oproti předchozímu příkladu je, že tato instance je odkazována statickým členem, což znamená, že není nikdy k dispozici pro sběr.</span><span class="sxs-lookup"><span data-stu-id="84933-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="84933-217">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="84933-217">The preceding code:</span></span>

* <span data-ttu-id="84933-218">Je příkladem typické nevracení paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="84933-219">Při častých voláních způsobí, že se `OutOfMemory` paměť aplikace zvětší, dokud se proces nezhroutí s výjimkou.</span><span class="sxs-lookup"><span data-stu-id="84933-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![předchozí graf](memory/_static/eternal.png)

<span data-ttu-id="84933-221">Na předchozím obrázku:</span><span class="sxs-lookup"><span data-stu-id="84933-221">In the preceding image:</span></span>

* <span data-ttu-id="84933-222">Zátěžové `/api/staticstring` testování koncového bodu způsobí lineární zvýšení paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="84933-223">Gc se pokusí uvolnit paměť jako tlak paměti roste voláním generace 2 kolekce.</span><span class="sxs-lookup"><span data-stu-id="84933-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="84933-224">Gc nelze uvolnit nevrácené paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="84933-225">Přidělené a pracovní sady se časem zvyšují.</span><span class="sxs-lookup"><span data-stu-id="84933-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="84933-226">Některé scénáře, jako je například ukládání do mezipaměti, vyžadují, aby byly odkazy na objekt y drženy, dokud je tlak paměti nevynutí uvolnění.</span><span class="sxs-lookup"><span data-stu-id="84933-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="84933-227">Třídu <xref:System.WeakReference> lze použít pro tento typ kódu ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="84933-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="84933-228">Objekt `WeakReference` je shromažďována pod tlaky paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="84933-229">Výchozí implementace <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> použití `WeakReference`.</span><span class="sxs-lookup"><span data-stu-id="84933-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="84933-230">Nativní paměť</span><span class="sxs-lookup"><span data-stu-id="84933-230">Native memory</span></span>

<span data-ttu-id="84933-231">Některé objekty .NET Core spoléhají na nativní paměť.</span><span class="sxs-lookup"><span data-stu-id="84933-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="84933-232">Nativní paměť **nemůže** být shromažďována GC.</span><span class="sxs-lookup"><span data-stu-id="84933-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="84933-233">Objekt .NET používající nativní paměť jej musí uvolnit pomocí nativního kódu.</span><span class="sxs-lookup"><span data-stu-id="84933-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="84933-234">Rozhraní .NET <xref:System.IDisposable> poskytuje rozhraní, které umožňuje vývojářům uvolnit nativní paměť.</span><span class="sxs-lookup"><span data-stu-id="84933-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="84933-235">I <xref:System.IDisposable.Dispose*> když není volána, `Dispose` správně implementované třídy volání při spuštění [finalizační metody.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)</span><span class="sxs-lookup"><span data-stu-id="84933-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="84933-236">Uvažujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="84933-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="84933-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) je spravovaná třída, takže všechny instance budou shromažďovány na konci požadavku.</span><span class="sxs-lookup"><span data-stu-id="84933-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="84933-238">Následující obrázek znázorňuje profil paměti `fileprovider` při vyvolání rozhraní API nepřetržitě.</span><span class="sxs-lookup"><span data-stu-id="84933-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![předchozí graf](memory/_static/fileprovider.png)

<span data-ttu-id="84933-240">Předchozí graf ukazuje zřejmý problém s implementací této třídy, protože stále zvyšuje využití paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="84933-241">Jedná se o známý problém, který je sledován v [tomto vydání](https://github.com/dotnet/aspnetcore/issues/3110).</span><span class="sxs-lookup"><span data-stu-id="84933-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="84933-242">Stejný únik může dojít v uživatelském kódu, jedním z následujících:</span><span class="sxs-lookup"><span data-stu-id="84933-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="84933-243">Není uvolnění třídy správně.</span><span class="sxs-lookup"><span data-stu-id="84933-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="84933-244">Zapomínání vyvolat `Dispose`metodu závislé objekty, které by měly být uvolněny.</span><span class="sxs-lookup"><span data-stu-id="84933-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="84933-245">Hromada velkých objektů</span><span class="sxs-lookup"><span data-stu-id="84933-245">Large objects heap</span></span>

<span data-ttu-id="84933-246">Časté přidělení paměti/volné cykly může fragmentovat paměť, zejména při přidělování velké bloky paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="84933-247">Objekty jsou přiděleny v souvislých blocích paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="84933-248">Chcete-li zmírnit fragmentaci, když GC uvolní paměť, pokusí se ji defragmentovat.</span><span class="sxs-lookup"><span data-stu-id="84933-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="84933-249">Tento proces se nazývá **zhutnění**.</span><span class="sxs-lookup"><span data-stu-id="84933-249">This process is called **compaction**.</span></span> <span data-ttu-id="84933-250">Zhutnění zahrnuje přesouvání objektů.</span><span class="sxs-lookup"><span data-stu-id="84933-250">Compaction involves moving objects.</span></span> <span data-ttu-id="84933-251">Přesunutí velkých objektů ukládá snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="84933-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="84933-252">Z tohoto důvodu GC vytvoří speciální paměťovou zónu pro _velké_ objekty, nazývané [haldy velkých objektů](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="84933-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="84933-253">Objekty, které jsou větší než 85 000 bajtů (přibližně 83 kB), jsou:</span><span class="sxs-lookup"><span data-stu-id="84933-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="84933-254">Umístěnna na LOH.</span><span class="sxs-lookup"><span data-stu-id="84933-254">Placed on the LOH.</span></span>
* <span data-ttu-id="84933-255">Není zhutněný.</span><span class="sxs-lookup"><span data-stu-id="84933-255">Not compacted.</span></span>
* <span data-ttu-id="84933-256">Shromažďovány během generace 2 GCs.</span><span class="sxs-lookup"><span data-stu-id="84933-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="84933-257">Když LOH je plná, GC spustí generace 2 kolekce.</span><span class="sxs-lookup"><span data-stu-id="84933-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="84933-258">Kolekce Generace 2:</span><span class="sxs-lookup"><span data-stu-id="84933-258">Generation 2 collections:</span></span>

* <span data-ttu-id="84933-259">Jsou ze své podstaty pomalé.</span><span class="sxs-lookup"><span data-stu-id="84933-259">Are inherently slow.</span></span>
* <span data-ttu-id="84933-260">Navíc vznikají náklady na aktivaci kolekce na všech ostatních generacích.</span><span class="sxs-lookup"><span data-stu-id="84933-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="84933-261">Následující kód okamžitě zkomprimuje LOH:</span><span class="sxs-lookup"><span data-stu-id="84933-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="84933-262">Viz <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> informace o zhutnění LOH.</span><span class="sxs-lookup"><span data-stu-id="84933-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="84933-263">V kontejnerech pomocí .NET Core 3.0 a novější loh je automaticky komprimován.</span><span class="sxs-lookup"><span data-stu-id="84933-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="84933-264">Následující rozhraní API, které ilustruje toto chování:</span><span class="sxs-lookup"><span data-stu-id="84933-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="84933-265">Následující graf zobrazuje profil paměti `/api/loh/84975` volání koncového bodu při maximálním zatížení:</span><span class="sxs-lookup"><span data-stu-id="84933-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![předchozí graf](memory/_static/loh1.png)

<span data-ttu-id="84933-267">Následující graf znázorňuje profil `/api/loh/84976` paměti volání koncového bodu a přiděluje *ještě jeden bajt*:</span><span class="sxs-lookup"><span data-stu-id="84933-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![předchozí graf](memory/_static/loh2.png)

<span data-ttu-id="84933-269">Poznámka: `byte[]` Struktura má režijní bajty.</span><span class="sxs-lookup"><span data-stu-id="84933-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="84933-270">To je důvod, proč 84,976 bajtů aktivuje limit 85,000.</span><span class="sxs-lookup"><span data-stu-id="84933-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="84933-271">Porovnání dvou předchozích grafů:</span><span class="sxs-lookup"><span data-stu-id="84933-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="84933-272">Pracovní sada je podobná pro oba scénáře, asi 450 MB.</span><span class="sxs-lookup"><span data-stu-id="84933-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="84933-273">Pod Požadavky LOH (84,975 bajtů) zobrazuje většinou generace 0 kolekce.</span><span class="sxs-lookup"><span data-stu-id="84933-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="84933-274">Přes Požadavky LOH generovat konstantní generace 2 kolekce.</span><span class="sxs-lookup"><span data-stu-id="84933-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="84933-275">Generace 2 kolekce jsou drahé.</span><span class="sxs-lookup"><span data-stu-id="84933-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="84933-276">Je zapotřebí více procesoru a propustnost klesá téměř o 50 %.</span><span class="sxs-lookup"><span data-stu-id="84933-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="84933-277">Dočasné velké objekty jsou obzvláště problematické, protože způsobují gen2 GC.</span><span class="sxs-lookup"><span data-stu-id="84933-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="84933-278">Pro maximální výkon by mělo být minimalizováno použití velkých objektů.</span><span class="sxs-lookup"><span data-stu-id="84933-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="84933-279">Pokud je to možné, rozdělte velké objekty.</span><span class="sxs-lookup"><span data-stu-id="84933-279">If possible, split up large objects.</span></span> <span data-ttu-id="84933-280">Například [middleware pro ukládání do mezipaměti odpovědi](xref:performance/caching/response) v ASP.NET Core rozdělilo položky mezipaměti na bloky menší než 85 000 bajtů.</span><span class="sxs-lookup"><span data-stu-id="84933-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="84933-281">Následující odkazy ukazují ASP.NET základní přístup k udržování objektů pod limitem LOH:</span><span class="sxs-lookup"><span data-stu-id="84933-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="84933-282">ResponseCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="84933-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="84933-283">ResponseCaching/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="84933-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="84933-284">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="84933-284">For more information, see:</span></span>

* [<span data-ttu-id="84933-285">Velká objektová halda odkrytá</span><span class="sxs-lookup"><span data-stu-id="84933-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="84933-286">Hromada velkých objektů</span><span class="sxs-lookup"><span data-stu-id="84933-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="84933-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="84933-287">HttpClient</span></span>

<span data-ttu-id="84933-288">Nesprávné použití <xref:System.Net.Http.HttpClient> může mít za následek nevracení prostředků.</span><span class="sxs-lookup"><span data-stu-id="84933-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="84933-289">Systémové prostředky, jako jsou připojení k databázi, sokety, popisovače souborů atd.:</span><span class="sxs-lookup"><span data-stu-id="84933-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="84933-290">Jsou vzácnější než paměť.</span><span class="sxs-lookup"><span data-stu-id="84933-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="84933-291">Jsou problematičtější při úniku než paměti.</span><span class="sxs-lookup"><span data-stu-id="84933-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="84933-292">Zkušení vývojáři rozhraní <xref:System.IDisposable.Dispose*> .NET vědí, že mají volat objekty, které implementují <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="84933-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="84933-293">Není likvidace objektů, `IDisposable` které implementují obvykle vede k nevracení paměti nebo nevracení systémových prostředků.</span><span class="sxs-lookup"><span data-stu-id="84933-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="84933-294">`HttpClient`vynucuje `IDisposable`, ale **neměl** by být zlikvidován při každém vyvolání.</span><span class="sxs-lookup"><span data-stu-id="84933-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="84933-295">Spíše `HttpClient` by měly být znovu použity.</span><span class="sxs-lookup"><span data-stu-id="84933-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="84933-296">Následující koncový bod vytvoří a `HttpClient` zmaže novou instanci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="84933-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="84933-297">Při načtení jsou zaznamenány následující chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="84933-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="84933-298">Přestože `HttpClient` jsou instance uvolněny, skutečné síťové připojení trvá nějakou dobu, než bude uvolněno operačním systémem.</span><span class="sxs-lookup"><span data-stu-id="84933-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="84933-299">Neustálým vytvářením nových připojení dochází k _vyčerpání portů._</span><span class="sxs-lookup"><span data-stu-id="84933-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="84933-300">Každé připojení klienta vyžaduje vlastní port klienta.</span><span class="sxs-lookup"><span data-stu-id="84933-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="84933-301">Jedním ze způsobů, jak zabránit vyčerpání portu, je opětovné použití stejné `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="84933-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="84933-302">Instance `HttpClient` se uvolní, když se aplikace zastaví.</span><span class="sxs-lookup"><span data-stu-id="84933-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="84933-303">Tento příklad ukazuje, že ne každý jednorázový prostředek by měl být uvolněn po každém použití.</span><span class="sxs-lookup"><span data-stu-id="84933-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="84933-304">Lepší způsob zpracování životnosti `HttpClient` instance najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="84933-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="84933-305">HttpClient a správa životnosti</span><span class="sxs-lookup"><span data-stu-id="84933-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="84933-306">Blog o výrobě klienta HTTPClient</span><span class="sxs-lookup"><span data-stu-id="84933-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="84933-307">Sdružování objektů</span><span class="sxs-lookup"><span data-stu-id="84933-307">Object pooling</span></span>

<span data-ttu-id="84933-308">Předchozí příklad ukázal, `HttpClient` jak může být instance statická a znovu použita všemi požadavky.</span><span class="sxs-lookup"><span data-stu-id="84933-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="84933-309">Opakované použití zabraňuje vyčerpání prostředků.</span><span class="sxs-lookup"><span data-stu-id="84933-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="84933-310">Sdružování objektů:</span><span class="sxs-lookup"><span data-stu-id="84933-310">Object pooling:</span></span>

* <span data-ttu-id="84933-311">Použije vzorek opakovaného použití.</span><span class="sxs-lookup"><span data-stu-id="84933-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="84933-312">Je určen pro objekty, které jsou nákladné vytvořit.</span><span class="sxs-lookup"><span data-stu-id="84933-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="84933-313">Fond je kolekce předem inicializovaných objektů, které mohou být vyhrazeny a uvolněny v podprocesech.</span><span class="sxs-lookup"><span data-stu-id="84933-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="84933-314">Fondy mohou definovat pravidla přidělení, jako jsou omezení, předdefinované velikosti nebo rychlost růstu.</span><span class="sxs-lookup"><span data-stu-id="84933-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="84933-315">Balíček NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) obsahuje třídy, které pomáhají spravovat tyto fondy.</span><span class="sxs-lookup"><span data-stu-id="84933-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="84933-316">Následující koncový bod rozhraní API `byte` inkonzisuje vyrovnávací paměť, která je vyplněna náhodnými čísly na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="84933-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="84933-317">Následující graf zobrazuje volání předchozího rozhraní API s mírným zatížením:</span><span class="sxs-lookup"><span data-stu-id="84933-317">The following chart display calling the preceding API with moderate load:</span></span>

![předchozí graf](memory/_static/array.png)

<span data-ttu-id="84933-319">V předchozím grafu generace 0 kolekce dojít přibližně jednou za sekundu.</span><span class="sxs-lookup"><span data-stu-id="84933-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="84933-320">Předchozí kód lze optimalizovat sdružováním `byte` vyrovnávací paměti pomocí [>ArrayPool\<T ](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="84933-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="84933-321">Statická instance je znovu použita napříč požadavky.</span><span class="sxs-lookup"><span data-stu-id="84933-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="84933-322">Co se liší s tímto přístupem je, že sdružený objekt je vrácena z rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="84933-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="84933-323">To znamená:</span><span class="sxs-lookup"><span data-stu-id="84933-323">That means:</span></span>

* <span data-ttu-id="84933-324">Objekt je mimo vaši kontrolu, jakmile se vrátíte z metody.</span><span class="sxs-lookup"><span data-stu-id="84933-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="84933-325">Objekt nelze uvolnit.</span><span class="sxs-lookup"><span data-stu-id="84933-325">You can't release the object.</span></span>

<span data-ttu-id="84933-326">Chcete-li nastavit likvidaci objektu:</span><span class="sxs-lookup"><span data-stu-id="84933-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="84933-327">Zapouzdřit sdružené pole v objektu na jedno použití.</span><span class="sxs-lookup"><span data-stu-id="84933-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="84933-328">Zaregistrujte sdružený objekt pomocí [httpcontext.response.registerFordispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="84933-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="84933-329">`RegisterForDispose`postará o volání `Dispose`cílového objektu tak, aby byl vydán pouze po dokončení požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="84933-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="84933-330">Použití stejnézatížení jako nesdružená verze má za následek následující graf:</span><span class="sxs-lookup"><span data-stu-id="84933-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![předchozí graf](memory/_static/pooledarray.png)

<span data-ttu-id="84933-332">Hlavní rozdíl je přidělena bajtů a v důsledku toho mnohem méně generace 0 kolekce.</span><span class="sxs-lookup"><span data-stu-id="84933-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84933-333">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="84933-333">Additional resources</span></span>

* [<span data-ttu-id="84933-334">Uvolněné</span><span class="sxs-lookup"><span data-stu-id="84933-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="84933-335">Principy různých režimů GC pomocí vizualizéru souběžnosti</span><span class="sxs-lookup"><span data-stu-id="84933-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="84933-336">Velká objektová halda odkrytá</span><span class="sxs-lookup"><span data-stu-id="84933-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="84933-337">Hromada velkých objektů</span><span class="sxs-lookup"><span data-stu-id="84933-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
