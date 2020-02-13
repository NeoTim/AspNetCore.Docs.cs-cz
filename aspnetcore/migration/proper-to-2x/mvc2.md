---
title: Migrace z ASP.NET na ASP.NET Core 2,0
author: isaac2004
description: Dostanou pokyny k migraci stávajících ASP.NET MVC nebo webových aplikací API na ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/mvc2
ms.openlocfilehash: 11bd3b948afaedc675ac4249099969382683f653
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172443"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="d5c67-103">Migrace z ASP.NET na ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="d5c67-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="d5c67-104">Od [Petr Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="d5c67-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="d5c67-105">Tento článek slouží jako referenční příručka pro migraci ASP.NETch aplikací na ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="d5c67-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d5c67-106">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="d5c67-106">Prerequisites</span></span>

<span data-ttu-id="d5c67-107">Nainstalujte **jednu** z následujících možností ze [souborů ke stažení pro rozhraní .NET: Windows](https://www.microsoft.com/net/download/windows):</span><span class="sxs-lookup"><span data-stu-id="d5c67-107">Install **one** of the following from [.NET Downloads: Windows](https://www.microsoft.com/net/download/windows):</span></span>

* <span data-ttu-id="d5c67-108">Sada .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="d5c67-108">.NET Core SDK</span></span>
* <span data-ttu-id="d5c67-109">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="d5c67-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="d5c67-110">**ASP.NET a webové vývojové** úlohy</span><span class="sxs-lookup"><span data-stu-id="d5c67-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="d5c67-111">**Vývojová úloha .NET Core pro různé platformy**</span><span class="sxs-lookup"><span data-stu-id="d5c67-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="d5c67-112">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="d5c67-112">Target frameworks</span></span>

<span data-ttu-id="d5c67-113">Projekty ASP.NET Core 2,0 nabízejí vývojářům flexibilitu při cílení na rozhraní .NET Core, .NET Framework nebo obojí.</span><span class="sxs-lookup"><span data-stu-id="d5c67-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="d5c67-114">V tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) určíte, které cílové rozhraní je nejvhodnější.</span><span class="sxs-lookup"><span data-stu-id="d5c67-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="d5c67-115">Při cílení na .NET Framework musí projekty odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5c67-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="d5c67-116">Cílení na .NET Core vám umožní eliminovat mnoho explicitních odkazů na balíčky díky ASP.NET Core 2,0 [Metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="d5c67-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="d5c67-117">Do projektu nainstalujte `Microsoft.AspNetCore.All` Metapackage:</span><span class="sxs-lookup"><span data-stu-id="d5c67-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="d5c67-118">Při použití Metapackage se s aplikací nasadí žádné balíčky, na které se neodkazuje v Metapackage.</span><span class="sxs-lookup"><span data-stu-id="d5c67-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="d5c67-119">Běhové úložiště .NET Core zahrnuje tyto prostředky a jsou předkompilovány pro zlepšení výkonu.</span><span class="sxs-lookup"><span data-stu-id="d5c67-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="d5c67-120">Další podrobnosti najdete v tématu <xref:fundamentals/metapackage>.</span><span class="sxs-lookup"><span data-stu-id="d5c67-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="d5c67-121">Rozdíly struktury projektu</span><span class="sxs-lookup"><span data-stu-id="d5c67-121">Project structure differences</span></span>

<span data-ttu-id="d5c67-122">Formát souboru *. csproj* byl v ASP.NET Core zjednodušený.</span><span class="sxs-lookup"><span data-stu-id="d5c67-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="d5c67-123">Mezi významné změny patří:</span><span class="sxs-lookup"><span data-stu-id="d5c67-123">Some notable changes include:</span></span>

* <span data-ttu-id="d5c67-124">Explicitní zahrnutí souborů není nutné, aby byly považovány za součást projektu.</span><span class="sxs-lookup"><span data-stu-id="d5c67-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="d5c67-125">Tím se snižuje riziko konfliktů při sloučení XML při práci na velkých týmech.</span><span class="sxs-lookup"><span data-stu-id="d5c67-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="d5c67-126">Neexistují žádné odkazy založené na identifikátorech GUID na jiné projekty, což vylepšuje čitelnost souboru.</span><span class="sxs-lookup"><span data-stu-id="d5c67-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="d5c67-127">Soubor lze upravovat bez jeho uvolnění v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d5c67-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Možnost úpravy kontextové nabídky příkazu CSPROJ v aplikaci Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="d5c67-129">Nahrazení souboru Global. asax</span><span class="sxs-lookup"><span data-stu-id="d5c67-129">Global.asax file replacement</span></span>

<span data-ttu-id="d5c67-130">ASP.NET Core zavádí nový mechanismus pro zavedení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5c67-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="d5c67-131">Vstupním bodem pro aplikace ASP.NET je soubor *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="d5c67-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="d5c67-132">Úlohy, jako je konfigurace směrování a registrace do oblasti, jsou zpracovávány v souboru *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="d5c67-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="d5c67-133">Tento přístup Couples aplikaci a server, na který je nasazený, způsobem, který je v konfliktu s implementací.</span><span class="sxs-lookup"><span data-stu-id="d5c67-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="d5c67-134">Ve snaze oddělit se [Owin](https://owin.org/) zavedla k tomu, aby poskytovala čisticí způsob, jak používat víc platforem dohromady.</span><span class="sxs-lookup"><span data-stu-id="d5c67-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="d5c67-135">OWIN poskytuje kanál pro přidání pouze těch potřebných modulů.</span><span class="sxs-lookup"><span data-stu-id="d5c67-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="d5c67-136">Hostující prostředí přijímá [spouštěcí](xref:fundamentals/startup) funkci ke konfiguraci služeb a kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5c67-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="d5c67-137">`Startup` registruje sadu middlewaru s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d5c67-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="d5c67-138">Pro každý požadavek aplikace volá každou součást middlewaru s ukazatelem na hlavní seznam s ukazatelem na existující sadu obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="d5c67-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="d5c67-139">Každá součást middleware může do kanálu zpracování požadavků přidat jednu nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="d5c67-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="d5c67-140">To je dosaženo vrácením odkazu na obslužnou rutinu, která je nového záhlaví seznamu.</span><span class="sxs-lookup"><span data-stu-id="d5c67-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="d5c67-141">Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužné rutiny v seznamu.</span><span class="sxs-lookup"><span data-stu-id="d5c67-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="d5c67-142">Při ASP.NET Core je vstupním bodem aplikace `Startup`a již nemusíte mít závislost na *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="d5c67-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="d5c67-143">Při použití OWIN s .NET Framework použijte jako kanál něco podobného jako v následujícím tvaru:</span><span class="sxs-lookup"><span data-stu-id="d5c67-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="d5c67-144">Tím se nakonfigurují výchozí trasy a ve výchozím nastavení se XmlSerialization přes JSON.</span><span class="sxs-lookup"><span data-stu-id="d5c67-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="d5c67-145">Podle potřeby přidejte do tohoto kanálu další middleware (načítají se služby, nastavení konfigurace, statické soubory atd.).</span><span class="sxs-lookup"><span data-stu-id="d5c67-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="d5c67-146">ASP.NET Core používá podobný přístup, ale nespoléhá na OWIN k tomu, aby záznam zpracoval.</span><span class="sxs-lookup"><span data-stu-id="d5c67-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="d5c67-147">Místo toho se provádí pomocí metody *Program.cs* `Main` (podobně jako u konzolových aplikací) a `Startup` se načítají přes něj.</span><span class="sxs-lookup"><span data-stu-id="d5c67-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="d5c67-148">`Startup` musí zahrnovat metodu `Configure`.</span><span class="sxs-lookup"><span data-stu-id="d5c67-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="d5c67-149">V `Configure`do kanálu přidejte potřebný middleware.</span><span class="sxs-lookup"><span data-stu-id="d5c67-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="d5c67-150">V následujícím příkladu (z výchozí šablony webu) se pro konfiguraci kanálu s podporou pro použít několik metod rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d5c67-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="d5c67-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="d5c67-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="d5c67-152">Chybové stránky</span><span class="sxs-lookup"><span data-stu-id="d5c67-152">Error pages</span></span>
* <span data-ttu-id="d5c67-153">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="d5c67-153">Static files</span></span>
* <span data-ttu-id="d5c67-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d5c67-154">ASP.NET Core MVC</span></span>
* <span data-ttu-id="d5c67-155">Identita</span><span class="sxs-lookup"><span data-stu-id="d5c67-155">Identity</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="d5c67-156">Hostitel a aplikace se odlišily, což poskytuje flexibilitu přesunu na jinou platformu v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="d5c67-156">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="d5c67-157">Podrobnější informace o ASP.NET Core spouštění a middlewaru najdete v článku <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d5c67-157">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="d5c67-158">Ukládání konfigurací</span><span class="sxs-lookup"><span data-stu-id="d5c67-158">Storing configurations</span></span>

<span data-ttu-id="d5c67-159">ASP.NET podporuje ukládání nastavení.</span><span class="sxs-lookup"><span data-stu-id="d5c67-159">ASP.NET supports storing settings.</span></span> <span data-ttu-id="d5c67-160">Toto nastavení se používá například pro podporu prostředí, do kterého byly aplikace nasazeny.</span><span class="sxs-lookup"><span data-stu-id="d5c67-160">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="d5c67-161">Běžný postup je uložit všechny vlastní páry klíč-hodnota do `<appSettings>` části souboru *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="d5c67-161">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="d5c67-162">Aplikace čtou tato nastavení pomocí kolekce `ConfigurationManager.AppSettings` v oboru názvů `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="d5c67-162">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="d5c67-163">ASP.NET Core může ukládat konfigurační data pro aplikaci do libovolného souboru a načíst je jako součást zavádění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d5c67-163">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="d5c67-164">Výchozím souborem použitým v šablonách projektu je *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d5c67-164">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="d5c67-165">Načtení tohoto souboru do instance `IConfiguration` v rámci aplikace se provádí v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d5c67-165">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="d5c67-166">Aplikace načte z `Configuration`, aby získala nastavení:</span><span class="sxs-lookup"><span data-stu-id="d5c67-166">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="d5c67-167">Existují rozšíření tohoto přístupu, aby byl proces robustnější, jako je například použití [vkládání závislostí](xref:fundamentals/dependency-injection) (di) k načtení služby s těmito hodnotami.</span><span class="sxs-lookup"><span data-stu-id="d5c67-167">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="d5c67-168">Metoda DI má k dispozici sadu konfiguračních objektů se silným typem.</span><span class="sxs-lookup"><span data-stu-id="d5c67-168">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="d5c67-169">**Poznámka:** Podrobnější informace o konfiguraci ASP.NET Core najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d5c67-169">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="d5c67-170">Vkládání nativních závislostí</span><span class="sxs-lookup"><span data-stu-id="d5c67-170">Native dependency injection</span></span>

<span data-ttu-id="d5c67-171">Důležitým cílem při sestavování rozsáhlých škálovatelných aplikací je volně přizpůsobitelné spojování komponent a služeb.</span><span class="sxs-lookup"><span data-stu-id="d5c67-171">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="d5c67-172">[Vkládání závislostí](xref:fundamentals/dependency-injection) je oblíbená technika pro dosažení tohoto a je to nativní součást ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5c67-172">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="d5c67-173">V aplikacích ASP.NET se vývojáři spoléhají na knihovnu třetí strany, která implementuje vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="d5c67-173">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="d5c67-174">Jedna taková knihovna je [Unity](https://github.com/unitycontainer/unity), kterou poskytují vzory Microsoftu & postupy.</span><span class="sxs-lookup"><span data-stu-id="d5c67-174">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="d5c67-175">Příkladem nastavení vstřiku závislosti s Unity je implementace `IDependencyResolver`, která zabalí `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="d5c67-175">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="d5c67-176">Vytvořte instanci `UnityContainer`, zaregistrujte službu a nastavte překladač závislostí `HttpConfiguration` na novou instanci `UnityResolver` pro váš kontejner:</span><span class="sxs-lookup"><span data-stu-id="d5c67-176">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="d5c67-177">Vložení `IProductRepository` tam, kde je potřeba:</span><span class="sxs-lookup"><span data-stu-id="d5c67-177">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="d5c67-178">Protože je vkládání závislostí součástí ASP.NET Core, můžete do `Startup.ConfigureServices`přidat svou službu:</span><span class="sxs-lookup"><span data-stu-id="d5c67-178">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="d5c67-179">Úložiště může být vloženo kdekoli, stejně jako u Unity.</span><span class="sxs-lookup"><span data-stu-id="d5c67-179">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="d5c67-180">Další informace o vkládání závislostí v ASP.NET Core naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d5c67-180">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="d5c67-181">Obsluha statických souborů</span><span class="sxs-lookup"><span data-stu-id="d5c67-181">Serving static files</span></span>

<span data-ttu-id="d5c67-182">Důležitou součástí vývoje webu je schopnost poskytovat statické prostředky na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d5c67-182">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="d5c67-183">Nejběžnějšími příklady statických souborů jsou HTML, CSS, JavaScript a image.</span><span class="sxs-lookup"><span data-stu-id="d5c67-183">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="d5c67-184">Tyto soubory musí být uloženy v publikovaném umístění aplikace (nebo CDN) a odkazovány, aby je bylo možné načíst pomocí žádosti.</span><span class="sxs-lookup"><span data-stu-id="d5c67-184">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="d5c67-185">Tento proces se v ASP.NET Core změnil.</span><span class="sxs-lookup"><span data-stu-id="d5c67-185">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="d5c67-186">V ASP.NET jsou statické soubory uloženy v různých adresářích a odkazovány v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="d5c67-186">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="d5c67-187">V ASP.NET Core jsou statické soubory uloženy v kořenu "Web root" ( *&lt;kořenovém obsahu&gt;/wwwroot*), pokud nejsou nakonfigurovány jinak.</span><span class="sxs-lookup"><span data-stu-id="d5c67-187">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="d5c67-188">Soubory jsou načteny do kanálu požadavků vyvoláním metody rozšíření `UseStaticFiles` z `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d5c67-188">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="d5c67-189">**Poznámka:** Pokud cílíte .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="d5c67-189">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="d5c67-190">Například prostředek obrázku ve složce *wwwroot/images* je přístupný prohlížeči v umístění, jako je například `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="d5c67-190">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="d5c67-191">**Poznámka:** Podrobný odkaz na obsluhu statických souborů v ASP.NET Core najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d5c67-191">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5c67-192">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5c67-192">Additional resources</span></span>

* [<span data-ttu-id="d5c67-193">Portování knihoven do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d5c67-193">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
