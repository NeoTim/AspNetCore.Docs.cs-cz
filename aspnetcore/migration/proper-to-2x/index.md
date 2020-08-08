---
title: Migrace z ASP.NET na ASP.NET Core
author: isaac2004
description: Dodržení pokynů k migraci stávajících ASP.NET MVC nebo webových aplikací API na ASP.NET Core. Web
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: defdb190acf915eaa7c4aa8469ee47888f771526
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014916"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="9808c-103">Migrace z ASP.NET na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9808c-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="9808c-104">Od [Petr Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="9808c-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="9808c-105">Tento článek slouží jako referenční příručka pro migraci aplikací ASP.NET na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9808c-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9808c-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9808c-106">Prerequisites</span></span>

[<span data-ttu-id="9808c-107">.NET Core SDK 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="9808c-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="9808c-108">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="9808c-108">Target frameworks</span></span>

<span data-ttu-id="9808c-109">Projekty ASP.NET Core nabízejí vývojářům flexibilitu při cílení na rozhraní .NET Core, .NET Framework nebo obojí.</span><span class="sxs-lookup"><span data-stu-id="9808c-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="9808c-110">V tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) určíte, které cílové rozhraní je nejvhodnější.</span><span class="sxs-lookup"><span data-stu-id="9808c-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="9808c-111">Při cílení na .NET Framework musí projekty odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="9808c-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="9808c-112">Cílení na .NET Core vám umožní eliminovat mnoho explicitních odkazů na balíčky díky ASP.NET Core [Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9808c-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9808c-113">Nainstalujte do `Microsoft.AspNetCore.App` projektu Metapackage:</span><span class="sxs-lookup"><span data-stu-id="9808c-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="9808c-114">Při použití Metapackage se s aplikací nasadí žádné balíčky, na které se neodkazuje v Metapackage.</span><span class="sxs-lookup"><span data-stu-id="9808c-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="9808c-115">Běhové úložiště .NET Core zahrnuje tyto prostředky a jsou předkompilovány pro zlepšení výkonu.</span><span class="sxs-lookup"><span data-stu-id="9808c-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="9808c-116">Další podrobnosti najdete v článku [Microsoft. AspNetCore. app Metapackage pro ASP.NET Core](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="9808c-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="9808c-117">Rozdíly struktury projektu</span><span class="sxs-lookup"><span data-stu-id="9808c-117">Project structure differences</span></span>

<span data-ttu-id="9808c-118">Formát souboru *. csproj* byl v ASP.NET Core zjednodušený.</span><span class="sxs-lookup"><span data-stu-id="9808c-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="9808c-119">Mezi významné změny patří:</span><span class="sxs-lookup"><span data-stu-id="9808c-119">Some notable changes include:</span></span>

- <span data-ttu-id="9808c-120">Explicitní zahrnutí souborů není nutné, aby byly považovány za součást projektu.</span><span class="sxs-lookup"><span data-stu-id="9808c-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="9808c-121">Tím se snižuje riziko konfliktů při sloučení XML při práci na velkých týmech.</span><span class="sxs-lookup"><span data-stu-id="9808c-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="9808c-122">Neexistují žádné odkazy založené na identifikátorech GUID na jiné projekty, což vylepšuje čitelnost souboru.</span><span class="sxs-lookup"><span data-stu-id="9808c-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="9808c-123">Soubor lze upravovat bez jeho uvolnění v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9808c-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Možnost úpravy kontextové nabídky příkazu CSPROJ v aplikaci Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="9808c-125">Nahrazení souboru Global. asax</span><span class="sxs-lookup"><span data-stu-id="9808c-125">Global.asax file replacement</span></span>

<span data-ttu-id="9808c-126">ASP.NET Core zavádí nový mechanismus pro zavedení aplikace.</span><span class="sxs-lookup"><span data-stu-id="9808c-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="9808c-127">Vstupním bodem pro aplikace ASP.NET je soubor *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="9808c-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="9808c-128">Úlohy, jako je konfigurace směrování a registrace do oblasti, jsou zpracovávány v souboru *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="9808c-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="9808c-129">Tento přístup Couples aplikaci a server, na který je nasazený, způsobem, který je v konfliktu s implementací.</span><span class="sxs-lookup"><span data-stu-id="9808c-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="9808c-130">Ve snaze oddělit se [Owin](https://owin.org/) zavedla k tomu, aby poskytovala čisticí způsob, jak používat víc platforem dohromady.</span><span class="sxs-lookup"><span data-stu-id="9808c-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="9808c-131">OWIN poskytuje kanál pro přidání pouze těch potřebných modulů.</span><span class="sxs-lookup"><span data-stu-id="9808c-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="9808c-132">Hostující prostředí přijímá [spouštěcí](xref:fundamentals/startup) funkci ke konfiguraci služeb a kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="9808c-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="9808c-133">`Startup`registruje sadu middlewaru s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9808c-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="9808c-134">Pro každý požadavek aplikace volá každou součást middlewaru s ukazatelem na hlavní seznam s ukazatelem na existující sadu obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="9808c-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="9808c-135">Každá součást middleware může do kanálu zpracování požadavků přidat jednu nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="9808c-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="9808c-136">To je dosaženo vrácením odkazu na obslužnou rutinu, která je nového záhlaví seznamu.</span><span class="sxs-lookup"><span data-stu-id="9808c-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="9808c-137">Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužné rutiny v seznamu.</span><span class="sxs-lookup"><span data-stu-id="9808c-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="9808c-138">V ASP.NET Core je vstupním bodem aplikace `Startup` a již nemusíte mít závislost na *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="9808c-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="9808c-139">Při použití OWIN s .NET Framework použijte jako kanál něco podobného jako v následujícím tvaru:</span><span class="sxs-lookup"><span data-stu-id="9808c-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="9808c-140">Tím se nakonfigurují výchozí trasy a ve výchozím nastavení se XmlSerialization přes JSON.</span><span class="sxs-lookup"><span data-stu-id="9808c-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="9808c-141">Podle potřeby přidejte do tohoto kanálu další middleware (načítají se služby, nastavení konfigurace, statické soubory atd.).</span><span class="sxs-lookup"><span data-stu-id="9808c-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="9808c-142">ASP.NET Core používá podobný přístup, ale nespoléhá na OWIN k tomu, aby záznam zpracoval.</span><span class="sxs-lookup"><span data-stu-id="9808c-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="9808c-143">Místo toho se provádí pomocí metody *program.cs* `Main` (podobně jako u konzolových aplikací) a `Startup` načítá se přes něj.</span><span class="sxs-lookup"><span data-stu-id="9808c-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="9808c-144">`Startup`musí obsahovat `Configure` metodu.</span><span class="sxs-lookup"><span data-stu-id="9808c-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="9808c-145">Do `Configure` kanálu přidejte potřebný middleware.</span><span class="sxs-lookup"><span data-stu-id="9808c-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="9808c-146">V následujícím příkladu (z výchozí šablony webu) rozšiřující metody konfigurují kanál s podporou pro:</span><span class="sxs-lookup"><span data-stu-id="9808c-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="9808c-147">Chybové stránky</span><span class="sxs-lookup"><span data-stu-id="9808c-147">Error pages</span></span>
- <span data-ttu-id="9808c-148">Striktní přenosová zabezpečení HTTP</span><span class="sxs-lookup"><span data-stu-id="9808c-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="9808c-149">Přesměrování HTTP na HTTPS</span><span class="sxs-lookup"><span data-stu-id="9808c-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="9808c-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="9808c-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="9808c-151">Hostitel a aplikace se odlišily, což poskytuje flexibilitu přesunu na jinou platformu v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="9808c-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="9808c-152">Podrobnější informace o ASP.NET Core spouštění a middlewaru najdete v tématu [spuštění v ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="9808c-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="9808c-153">Konfigurace úložiště</span><span class="sxs-lookup"><span data-stu-id="9808c-153">Store configurations</span></span>

<span data-ttu-id="9808c-154">ASP.NET podporuje ukládání nastavení.</span><span class="sxs-lookup"><span data-stu-id="9808c-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="9808c-155">Toto nastavení se používá například pro podporu prostředí, do kterého byly aplikace nasazeny.</span><span class="sxs-lookup"><span data-stu-id="9808c-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="9808c-156">Běžný postup je uložit všechny vlastní páry klíč-hodnota do `<appSettings>` části souboru *Web.config* :</span><span class="sxs-lookup"><span data-stu-id="9808c-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="9808c-157">Aplikace čtou tato nastavení pomocí `ConfigurationManager.AppSettings` kolekce v `System.Configuration` oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="9808c-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="9808c-158">ASP.NET Core může ukládat konfigurační data pro aplikaci do libovolného souboru a načíst je jako součást zavádění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="9808c-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="9808c-159">Výchozí soubor, který se používá v šablonách projektu, je *appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="9808c-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="9808c-160">Načtení tohoto souboru do instance `IConfiguration` uvnitř aplikace se provádí v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9808c-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="9808c-161">Aplikace načte z `Configuration` a získá nastavení:</span><span class="sxs-lookup"><span data-stu-id="9808c-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="9808c-162">Existují rozšíření tohoto přístupu, aby byl proces robustnější, jako je například použití [vkládání závislostí](xref:fundamentals/dependency-injection) (di) k načtení služby s těmito hodnotami.</span><span class="sxs-lookup"><span data-stu-id="9808c-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="9808c-163">Metoda DI má k dispozici sadu konfiguračních objektů se silným typem.</span><span class="sxs-lookup"><span data-stu-id="9808c-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="9808c-164">Podrobnější informace o konfiguraci ASP.NET Core najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9808c-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="9808c-165">Vkládání nativních závislostí</span><span class="sxs-lookup"><span data-stu-id="9808c-165">Native dependency injection</span></span>

<span data-ttu-id="9808c-166">Důležitým cílem při sestavování rozsáhlých škálovatelných aplikací je volně přizpůsobitelné spojování komponent a služeb.</span><span class="sxs-lookup"><span data-stu-id="9808c-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="9808c-167">[Vkládání závislostí](xref:fundamentals/dependency-injection) je oblíbená technika pro dosažení tohoto a je to nativní součást ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9808c-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="9808c-168">V aplikacích ASP.NET se vývojáři spoléhají na knihovnu třetí strany, která implementuje vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9808c-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="9808c-169">Jedna taková knihovna je [Unity](https://github.com/unitycontainer/unity), kterou poskytují vzory Microsoftu & postupy.</span><span class="sxs-lookup"><span data-stu-id="9808c-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="9808c-170">Příkladem nastavení injektáže závislosti s Unity je implementace `IDependencyResolver` , která zabalí `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="9808c-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="9808c-171">Vytvořte instanci své `UnityContainer` služby, Zaregistrujte svoji službu a nastavte překladač závislostí `HttpConfiguration` na novou instanci `UnityResolver` pro svůj kontejner:</span><span class="sxs-lookup"><span data-stu-id="9808c-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="9808c-172">Vložení `IProductRepository` tam, kde je potřeba:</span><span class="sxs-lookup"><span data-stu-id="9808c-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="9808c-173">Protože vkládání závislostí je součástí ASP.NET Core, můžete přidat službu v `ConfigureServices` metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="9808c-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="9808c-174">Úložiště může být vloženo kdekoli, stejně jako u Unity.</span><span class="sxs-lookup"><span data-stu-id="9808c-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="9808c-175">Další informace o vkládání závislostí naleznete v tématu [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9808c-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="9808c-176">Obsluhovat statické soubory</span><span class="sxs-lookup"><span data-stu-id="9808c-176">Serve static files</span></span>

<span data-ttu-id="9808c-177">Důležitou součástí vývoje webu je schopnost poskytovat statické prostředky na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9808c-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="9808c-178">Nejběžnějšími příklady statických souborů jsou HTML, CSS, JavaScript a image.</span><span class="sxs-lookup"><span data-stu-id="9808c-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="9808c-179">Tyto soubory musí být uloženy v publikovaném umístění aplikace (nebo CDN) a odkazovány, aby je bylo možné načíst pomocí žádosti.</span><span class="sxs-lookup"><span data-stu-id="9808c-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="9808c-180">Tento proces se v ASP.NET Core změnil.</span><span class="sxs-lookup"><span data-stu-id="9808c-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="9808c-181">V ASP.NET jsou statické soubory uloženy v různých adresářích a odkazovány v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="9808c-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="9808c-182">V ASP.NET Core jsou statické soubory uloženy v kořenu "Web root" (\* &lt; kořenový adresář obsahu &gt; /wwwroot\*), pokud není nakonfigurováno jinak.</span><span class="sxs-lookup"><span data-stu-id="9808c-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="9808c-183">Soubory jsou načteny do kanálu požadavků vyvoláním `UseStaticFiles` metody rozšíření z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9808c-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="9808c-184">Pokud cílíte .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="9808c-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="9808c-185">Například prostředek obrázku ve složce *wwwroot/images* je přístupný prohlížeči v umístění, jako je například `http://<app>/images/<imageFileName>` .</span><span class="sxs-lookup"><span data-stu-id="9808c-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="9808c-186">Podrobnější informace o obsluze statických souborů v ASP.NET Core najdete v tématu [statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="9808c-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-no-loccookies"></a><span data-ttu-id="9808c-187">S více hodnotami cookie</span><span class="sxs-lookup"><span data-stu-id="9808c-187">Multi-value cookies</span></span>

<span data-ttu-id="9808c-188">V ASP.NET Core nejsou podporovány [hodnoty cookie s více hodnotami](xref:System.Web.HttpCookie.Values) .</span><span class="sxs-lookup"><span data-stu-id="9808c-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="9808c-189">Vytvořte jednu cookie na hodnotu.</span><span class="sxs-lookup"><span data-stu-id="9808c-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="9808c-190">Migrace částečné aplikace</span><span class="sxs-lookup"><span data-stu-id="9808c-190">Partial app migration</span></span>

<span data-ttu-id="9808c-191">Jedním z možností migrace částečné aplikace je vytvoření podaplikace služby IIS a přesunutí určitých tras z ASP.NET 4. x na ASP.NET Core a zachování struktury adresy URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="9808c-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="9808c-192">Zvažte například strukturu adresy URL aplikace ze souboru *applicationHost.config* :</span><span class="sxs-lookup"><span data-stu-id="9808c-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

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

<span data-ttu-id="9808c-193">Adresářová struktura:</span><span class="sxs-lookup"><span data-stu-id="9808c-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a><span data-ttu-id="9808c-194">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9808c-194">Additional resources</span></span>

- [<span data-ttu-id="9808c-195">Portování knihoven do .NET Core</span><span class="sxs-lookup"><span data-stu-id="9808c-195">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
