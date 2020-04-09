---
title: Migrace z ASP.NET do ASP.NET jádra
author: isaac2004
description: Získejte pokyny pro migraci existujících aplikací ASP.NET MVC nebo Web API do ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
uid: migration/proper-to-2x/index
ms.openlocfilehash: 68a45dc50e00bead564500a12509b62a4a193ec4
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511083"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="68524-103">Migrace z ASP.NET do ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="68524-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="68524-104">Podle [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="68524-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="68524-105">Tento článek slouží jako referenční příručka pro migraci ASP.NET aplikací, které mají ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68524-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68524-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="68524-106">Prerequisites</span></span>

[<span data-ttu-id="68524-107">Sada .NET Core SDK 2.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="68524-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="68524-108">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="68524-108">Target frameworks</span></span>

<span data-ttu-id="68524-109">ASP.NET Základní projekty nabízejí vývojářům flexibilitu cílení .NET Core, .NET Framework nebo obojí.</span><span class="sxs-lookup"><span data-stu-id="68524-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="68524-110">Viz [Výběr mezi rozhraním .NET Core a rozhraní MN .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) a zjistěte, který cílový rámec je nejvhodnější.</span><span class="sxs-lookup"><span data-stu-id="68524-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="68524-111">Při cílení na rozhraní .NET Framework musí projekty odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="68524-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="68524-112">Cílení .NET Core umožňuje eliminovat četné explicitní odkazy na balíčky díky [metabalíčku](xref:fundamentals/metapackage-app)ASP.NET Core .</span><span class="sxs-lookup"><span data-stu-id="68524-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="68524-113">Nainstalujte `Microsoft.AspNetCore.App` metabalíček do projektu:</span><span class="sxs-lookup"><span data-stu-id="68524-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="68524-114">Při použití metabalíčku nejsou s aplikací nasazeny žádné balíčky odkazované v metabalíčku.</span><span class="sxs-lookup"><span data-stu-id="68524-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="68524-115">Úložiště .NET Core Runtime Store obsahuje tyto prostředky a jsou předkompilovány ke zlepšení výkonu.</span><span class="sxs-lookup"><span data-stu-id="68524-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="68524-116">Další podrobnosti najdete [v metabalíčku Microsoft.AspNetCore.App pro ASP.NET Core.](xref:fundamentals/metapackage-app)</span><span class="sxs-lookup"><span data-stu-id="68524-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="68524-117">Rozdíly ve struktuře projektu</span><span class="sxs-lookup"><span data-stu-id="68524-117">Project structure differences</span></span>

<span data-ttu-id="68524-118">Formát souboru *.csproj* byl zjednodušen v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68524-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="68524-119">Některé významné změny zahrnují:</span><span class="sxs-lookup"><span data-stu-id="68524-119">Some notable changes include:</span></span>

- <span data-ttu-id="68524-120">Explicitní zahrnutí souborů není nutné, aby byly považovány za součást projektu.</span><span class="sxs-lookup"><span data-stu-id="68524-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="68524-121">To snižuje riziko konfliktů sloučení XML při práci s velkými týmy.</span><span class="sxs-lookup"><span data-stu-id="68524-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="68524-122">Neexistují žádné odkazy založené na identifikátoru GUID na jiné projekty, což zlepšuje čitelnost souborů.</span><span class="sxs-lookup"><span data-stu-id="68524-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="68524-123">Soubor lze upravit bez jeho uvolnění v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="68524-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Upravit možnost kontextové nabídky CSPROJ v Sadě Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="68524-125">Nahrazení souboru Global.asax</span><span class="sxs-lookup"><span data-stu-id="68524-125">Global.asax file replacement</span></span>

<span data-ttu-id="68524-126">ASP.NET Core představil nový mechanismus pro zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="68524-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="68524-127">Vstupním bodem pro ASP.NET aplikace je soubor *Global.asax.*</span><span class="sxs-lookup"><span data-stu-id="68524-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="68524-128">Úkoly, jako je konfigurace postupu a registrace filtrů a oblastí, jsou zpracovány v souboru *Global.asax.*</span><span class="sxs-lookup"><span data-stu-id="68524-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="68524-129">Tento přístup spáruje aplikaci a server, na který je nasazena způsobem, který narušuje implementaci.</span><span class="sxs-lookup"><span data-stu-id="68524-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="68524-130">Ve snaze oddělit, [OWIN](https://owin.org/) byl zaveden poskytnout čistší způsob, jak používat více rámců dohromady.</span><span class="sxs-lookup"><span data-stu-id="68524-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="68524-131">OWIN poskytuje kanál pro přidání pouze potřebných modulů.</span><span class="sxs-lookup"><span data-stu-id="68524-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="68524-132">Hostitelské prostředí používá funkci [po spuštění](xref:fundamentals/startup) ke konfiguraci služeb a kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="68524-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="68524-133">`Startup`zaregistruje sadu middleware s aplikací.</span><span class="sxs-lookup"><span data-stu-id="68524-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="68524-134">Pro každý požadavek aplikace volá každou součást middleware s ukazatelem hlavy propojeného seznamu na existující sadu obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="68524-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="68524-135">Každá součást middlewaru může do kanálu zpracování požadavků přidat jednu nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="68524-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="68524-136">Toho lze dosáhnout vrácením odkazu na obslužnou rutinu, která je novou hlavou seznamu.</span><span class="sxs-lookup"><span data-stu-id="68524-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="68524-137">Každá obslužná rutina je zodpovědná za zapamatování a vyvolání další obslužné rutiny v seznamu.</span><span class="sxs-lookup"><span data-stu-id="68524-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="68524-138">S ASP.NET jádra, vstupní bod `Startup`do aplikace je a již nemáte závislost na *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="68524-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="68524-139">Při použití OWIN s rozhraním .NET Framework použijte jako kanál něco jako následující:</span><span class="sxs-lookup"><span data-stu-id="68524-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="68524-140">Tím nakonfigurujete výchozí trasy a výchozí xmlserializace přes Json.</span><span class="sxs-lookup"><span data-stu-id="68524-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="68524-141">Podle potřeby přidejte do tohoto kanálu další middleware (načtení služeb, nastavení konfigurace, statické soubory atd.).</span><span class="sxs-lookup"><span data-stu-id="68524-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="68524-142">ASP.NET Core používá podobný přístup, ale nespoléhá se na OWIN pro zpracování položky.</span><span class="sxs-lookup"><span data-stu-id="68524-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="68524-143">Místo toho, to se provádí prostřednictvím *metody* `Main` Program.cs `Startup` (podobně jako konzolové aplikace) a je načten přes tam.</span><span class="sxs-lookup"><span data-stu-id="68524-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="68524-144">`Startup`musí obsahovat metodu. `Configure`</span><span class="sxs-lookup"><span data-stu-id="68524-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="68524-145">V `Configure`, přidejte potřebné middleware do potrubí.</span><span class="sxs-lookup"><span data-stu-id="68524-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="68524-146">V následujícím příkladu (z výchozí šablony webu) metody rozšíření konfigurují kanál s podporou:</span><span class="sxs-lookup"><span data-stu-id="68524-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="68524-147">Chybové stránky</span><span class="sxs-lookup"><span data-stu-id="68524-147">Error pages</span></span>
- <span data-ttu-id="68524-148">HTTP Přísné zabezpečení přenosu</span><span class="sxs-lookup"><span data-stu-id="68524-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="68524-149">Přesměrování HTTP na protokol HTTPS</span><span class="sxs-lookup"><span data-stu-id="68524-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="68524-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="68524-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="68524-151">Hostitel a aplikace byly odděleny, což poskytuje flexibilitu přechodu na jinou platformu v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="68524-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="68524-152">Podrobnější odkaz na ASP.NET základního startupu a middlewaru najdete [v tématu Startup in ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="68524-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="68524-153">Konfigurace úložiště</span><span class="sxs-lookup"><span data-stu-id="68524-153">Store configurations</span></span>

<span data-ttu-id="68524-154">ASP.NET podporuje nastavení ukládání.</span><span class="sxs-lookup"><span data-stu-id="68524-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="68524-155">Tato nastavení se používají například pro podporu prostředí, do kterého byly aplikace nasazeny.</span><span class="sxs-lookup"><span data-stu-id="68524-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="68524-156">Běžnou praxí bylo ukládat všechny vlastní dvojice `<appSettings>` klíč-hodnota v části souboru *Web.config:*</span><span class="sxs-lookup"><span data-stu-id="68524-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="68524-157">Aplikace číst tato `ConfigurationManager.AppSettings` nastavení pomocí `System.Configuration` kolekce v oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="68524-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="68524-158">ASP.NET Core může ukládat konfigurační data pro aplikaci do libovolného souboru a načíst je jako součást middleware bootstrapping.</span><span class="sxs-lookup"><span data-stu-id="68524-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="68524-159">Výchozí soubor použitý v šablonách projektu je *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="68524-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="68524-160">Načítání tohoto souboru `IConfiguration` do instance uvnitř aplikace se provádí v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="68524-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="68524-161">Aplikace čte `Configuration` z získat nastavení:</span><span class="sxs-lookup"><span data-stu-id="68524-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="68524-162">Existují rozšíření tohoto přístupu, aby byl proces robustnější, například pomocí [vkládání závislostí](xref:fundamentals/dependency-injection) (DI) k načtení služby s těmito hodnotami.</span><span class="sxs-lookup"><span data-stu-id="68524-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="68524-163">Přístup DI poskytuje sadu objektů konfigurace silného typu.</span><span class="sxs-lookup"><span data-stu-id="68524-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="68524-164">Podrobnější odkaz na konfiguraci ASP.NET jádra naleznete [v tématu Konfigurace v ASP.NET jádra](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="68524-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="68524-165">Nativní vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="68524-165">Native dependency injection</span></span>

<span data-ttu-id="68524-166">Důležitým cílem při vytváření velkých, škálovatelných aplikací je volné párování součástí a služeb.</span><span class="sxs-lookup"><span data-stu-id="68524-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="68524-167">[Vkládání závislostí](xref:fundamentals/dependency-injection) je oblíbenou technikou pro dosažení tohoto cíle a je nativní součástí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68524-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="68524-168">V ASP.NET aplikacích se vývojáři spoléhají na knihovnu třetích stran při implementaci vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="68524-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="68524-169">Jednou z takových knihoven je [Unity](https://github.com/unitycontainer/unity), kterou poskytuje Microsoft Patterns & Practices.</span><span class="sxs-lookup"><span data-stu-id="68524-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="68524-170">Příkladem nastavení vkládání závislostí s Unity `IDependencyResolver` je implementace, `UnityContainer`která zabalí :</span><span class="sxs-lookup"><span data-stu-id="68524-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="68524-171">Vytvořte instanci služby `UnityContainer`, zaregistrujte službu `HttpConfiguration` a nastavte překládání závislostí na novou instanci kontejneru: `UnityResolver`</span><span class="sxs-lookup"><span data-stu-id="68524-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="68524-172">V `IProductRepository` případě potřeby vstříkněte:</span><span class="sxs-lookup"><span data-stu-id="68524-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="68524-173">Vzhledem k tomu, že vkládání závislostí je `ConfigureServices` součástí ASP.NET core, můžete přidat službu v metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="68524-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="68524-174">Úložiště může být injektováno kdekoli, jak tomu bylo u Unity.</span><span class="sxs-lookup"><span data-stu-id="68524-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="68524-175">Další informace o vkládání závislostí naleznete v [tématu vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="68524-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="68524-176">Obsluha statických souborů</span><span class="sxs-lookup"><span data-stu-id="68524-176">Serve static files</span></span>

<span data-ttu-id="68524-177">Důležitou součástí vývoje webových aplikací je schopnost obsluhovat statické prostředky na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="68524-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="68524-178">Nejběžnějšípříklady statických souborů jsou HTML, CSS, Javascript a obrázky.</span><span class="sxs-lookup"><span data-stu-id="68524-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="68524-179">Tyto soubory je třeba uložit do publikovaného umístění aplikace (nebo CDN) a odkazovat, aby mohly být načteny požadavkem.</span><span class="sxs-lookup"><span data-stu-id="68524-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="68524-180">Tento proces se změnil v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68524-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="68524-181">V ASP.NET jsou statické soubory uloženy v různých adresářích a odkazovány v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="68524-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="68524-182">V ASP.NET Core jsou statické soubory uloženy v "kořenovém adresáři*&lt;webu" (kořenový obsah&gt;/wwwroot),* pokud není nakonfigurováno jinak.</span><span class="sxs-lookup"><span data-stu-id="68524-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="68524-183">Soubory jsou načteny do kanálu požadavku vyvoláním `UseStaticFiles` metody `Startup.Configure`rozšíření z :</span><span class="sxs-lookup"><span data-stu-id="68524-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="68524-184">Pokud cílení .NET Framework, nainstalujte balíček `Microsoft.AspNetCore.StaticFiles`NuGet .</span><span class="sxs-lookup"><span data-stu-id="68524-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="68524-185">Například datový zdroj obrázku ve složce *wwwroot/images* je přístupný `http://<app>/images/<imageFileName>`prohlížeči v umístění, například .</span><span class="sxs-lookup"><span data-stu-id="68524-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="68524-186">Podrobnější odkaz na zobrazování statických souborů v ASP.NET jádru naleznete v [tématu Statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="68524-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-cookies"></a><span data-ttu-id="68524-187">Soubory cookie s více hodnotami</span><span class="sxs-lookup"><span data-stu-id="68524-187">Multi-value cookies</span></span>

<span data-ttu-id="68524-188">[Soubory cookie s více hodnotami](xref:System.Web.HttpCookie.Values) nejsou v ASP.NET Core podporovány.</span><span class="sxs-lookup"><span data-stu-id="68524-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="68524-189">Vytvořte jeden soubor cookie na hodnotu.</span><span class="sxs-lookup"><span data-stu-id="68524-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="68524-190">Částečná migrace aplikací</span><span class="sxs-lookup"><span data-stu-id="68524-190">Partial app migration</span></span>

<span data-ttu-id="68524-191">Jedním z přístupů k částečné migraci aplikací je vytvoření podaplikace služby IIS a přesunutí pouze určitých tras z ASP.NET 4.x do ASP.NET jádra při zachování struktury adresy URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="68524-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="68524-192">Zvažte například strukturu adresy URL aplikace ze souboru *applicationHost.config:*</span><span class="sxs-lookup"><span data-stu-id="68524-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="68524-193">Adresářová struktura:</span><span class="sxs-lookup"><span data-stu-id="68524-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a><span data-ttu-id="68524-194">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="68524-194">Additional resources</span></span>

- [<span data-ttu-id="68524-195">Přenesení knihoven do jádra rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="68524-195">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
