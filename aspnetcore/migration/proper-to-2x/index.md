---
title: Migrace z ASP.NET na ASP.NET Core
author: isaac2004
description: Dodržení pokynů k migraci stávajících ASP.NET MVC nebo webových aplikací API na ASP.NET Core. Web
ms.author: scaddie
ms.date: 10/18/2019
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: f1a5af60f8dce83d9622ed9d2c6bcb4b8fc22b73
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712490"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="d03c0-103">Migrace z ASP.NET na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d03c0-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="d03c0-104">Od [Petr Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="d03c0-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="d03c0-105">Tento článek slouží jako referenční příručka pro migraci aplikací ASP.NET na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d03c0-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d03c0-106">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="d03c0-106">Prerequisites</span></span>

[<span data-ttu-id="d03c0-107">.NET Core SDK 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d03c0-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="d03c0-108">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="d03c0-108">Target frameworks</span></span>

<span data-ttu-id="d03c0-109">Projekty ASP.NET Core nabízejí vývojářům flexibilitu při cílení na rozhraní .NET Core, .NET Framework nebo obojí.</span><span class="sxs-lookup"><span data-stu-id="d03c0-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="d03c0-110">V tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) určíte, které cílové rozhraní je nejvhodnější.</span><span class="sxs-lookup"><span data-stu-id="d03c0-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="d03c0-111">Při cílení na .NET Framework musí projekty odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="d03c0-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="d03c0-112">Cílení na .NET Core vám umožní eliminovat mnoho explicitních odkazů na balíčky díky ASP.NET Core [Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d03c0-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d03c0-113">Nainstalujte do `Microsoft.AspNetCore.App` projektu Metapackage:</span><span class="sxs-lookup"><span data-stu-id="d03c0-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="d03c0-114">Při použití Metapackage se s aplikací nasadí žádné balíčky, na které se neodkazuje v Metapackage.</span><span class="sxs-lookup"><span data-stu-id="d03c0-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="d03c0-115">Běhové úložiště .NET Core zahrnuje tyto prostředky a jsou předkompilovány pro zlepšení výkonu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="d03c0-116">Další podrobnosti najdete v článku [Microsoft. AspNetCore. app Metapackage pro ASP.NET Core](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="d03c0-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="d03c0-117">Rozdíly struktury projektu</span><span class="sxs-lookup"><span data-stu-id="d03c0-117">Project structure differences</span></span>

<span data-ttu-id="d03c0-118">Formát souboru *. csproj* byl v ASP.NET Core zjednodušený.</span><span class="sxs-lookup"><span data-stu-id="d03c0-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="d03c0-119">Mezi významné změny patří:</span><span class="sxs-lookup"><span data-stu-id="d03c0-119">Some notable changes include:</span></span>

- <span data-ttu-id="d03c0-120">Explicitní zahrnutí souborů není nutné, aby byly považovány za součást projektu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="d03c0-121">Tím se snižuje riziko konfliktů při sloučení XML při práci na velkých týmech.</span><span class="sxs-lookup"><span data-stu-id="d03c0-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="d03c0-122">Neexistují žádné odkazy založené na identifikátorech GUID na jiné projekty, což vylepšuje čitelnost souboru.</span><span class="sxs-lookup"><span data-stu-id="d03c0-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="d03c0-123">Soubor lze upravovat bez jeho uvolnění v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d03c0-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Možnost úpravy kontextové nabídky příkazu CSPROJ v aplikaci Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="d03c0-125">Nahrazení souboru Global. asax</span><span class="sxs-lookup"><span data-stu-id="d03c0-125">Global.asax file replacement</span></span>

<span data-ttu-id="d03c0-126">ASP.NET Core zavádí nový mechanismus pro zavedení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d03c0-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="d03c0-127">Vstupním bodem pro aplikace ASP.NET je soubor *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="d03c0-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="d03c0-128">Úlohy, jako je konfigurace směrování a registrace do oblasti, jsou zpracovávány v souboru *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="d03c0-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="d03c0-129">Tento přístup Couples aplikaci a server, na který je nasazený, způsobem, který je v konfliktu s implementací.</span><span class="sxs-lookup"><span data-stu-id="d03c0-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="d03c0-130">Ve snaze oddělit se [Owin](https://owin.org/) zavedla k tomu, aby poskytovala čisticí způsob, jak používat víc platforem dohromady.</span><span class="sxs-lookup"><span data-stu-id="d03c0-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="d03c0-131">OWIN poskytuje kanál pro přidání pouze těch potřebných modulů.</span><span class="sxs-lookup"><span data-stu-id="d03c0-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="d03c0-132">Hostující prostředí přijímá [spouštěcí](xref:fundamentals/startup) funkci ke konfiguraci služeb a kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="d03c0-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="d03c0-133">`Startup` registruje sadu middlewaru s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d03c0-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="d03c0-134">Pro každý požadavek aplikace volá každou součást middlewaru s ukazatelem na hlavní seznam s ukazatelem na existující sadu obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="d03c0-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="d03c0-135">Každá součást middleware může do kanálu zpracování požadavků přidat jednu nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="d03c0-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="d03c0-136">To je dosaženo vrácením odkazu na obslužnou rutinu, která je nového záhlaví seznamu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="d03c0-137">Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužné rutiny v seznamu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="d03c0-138">V ASP.NET Core je vstupním bodem aplikace `Startup` a již nemusíte mít závislost na *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="d03c0-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="d03c0-139">Při použití OWIN s .NET Framework použijte jako kanál něco podobného jako v následujícím tvaru:</span><span class="sxs-lookup"><span data-stu-id="d03c0-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="d03c0-140">Tím se nakonfigurují výchozí trasy a ve výchozím nastavení se XmlSerialization přes JSON.</span><span class="sxs-lookup"><span data-stu-id="d03c0-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="d03c0-141">Podle potřeby přidejte do tohoto kanálu další middleware (načítají se služby, nastavení konfigurace, statické soubory atd.).</span><span class="sxs-lookup"><span data-stu-id="d03c0-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="d03c0-142">ASP.NET Core používá podobný přístup, ale nespoléhá na OWIN k tomu, aby záznam zpracoval.</span><span class="sxs-lookup"><span data-stu-id="d03c0-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="d03c0-143">Místo toho se provádí pomocí metody *program.cs* `Main` (podobně jako u konzolových aplikací) a `Startup` načítá se přes něj.</span><span class="sxs-lookup"><span data-stu-id="d03c0-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="d03c0-144">`Startup` musí obsahovat `Configure` metodu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="d03c0-145">Do `Configure` kanálu přidejte potřebný middleware.</span><span class="sxs-lookup"><span data-stu-id="d03c0-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="d03c0-146">V následujícím příkladu (z výchozí šablony webu) rozšiřující metody konfigurují kanál s podporou pro:</span><span class="sxs-lookup"><span data-stu-id="d03c0-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="d03c0-147">Chybové stránky</span><span class="sxs-lookup"><span data-stu-id="d03c0-147">Error pages</span></span>
- <span data-ttu-id="d03c0-148">Striktní přenosová zabezpečení HTTP</span><span class="sxs-lookup"><span data-stu-id="d03c0-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="d03c0-149">Přesměrování HTTP na HTTPS</span><span class="sxs-lookup"><span data-stu-id="d03c0-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="d03c0-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d03c0-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="d03c0-151">Hostitel a aplikace se odlišily, což poskytuje flexibilitu přesunu na jinou platformu v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="d03c0-152">Podrobnější informace o ASP.NET Core spouštění a middlewaru najdete v tématu [spuštění v ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="d03c0-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="d03c0-153">Konfigurace úložiště</span><span class="sxs-lookup"><span data-stu-id="d03c0-153">Store configurations</span></span>

<span data-ttu-id="d03c0-154">ASP.NET podporuje ukládání nastavení.</span><span class="sxs-lookup"><span data-stu-id="d03c0-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="d03c0-155">Toto nastavení se používá například pro podporu prostředí, do kterého byly aplikace nasazeny.</span><span class="sxs-lookup"><span data-stu-id="d03c0-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="d03c0-156">Běžný postup je uložit všechny vlastní páry klíč-hodnota do `<appSettings>` části souboru *Web.config* :</span><span class="sxs-lookup"><span data-stu-id="d03c0-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="d03c0-157">Aplikace čtou tato nastavení pomocí `ConfigurationManager.AppSettings` kolekce v `System.Configuration` oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="d03c0-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="d03c0-158">ASP.NET Core může ukládat konfigurační data pro aplikaci do libovolného souboru a načíst je jako součást zavádění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d03c0-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="d03c0-159">Výchozí soubor, který se používá v šablonách projektu, je *appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="d03c0-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="d03c0-160">Načtení tohoto souboru do instance `IConfiguration` uvnitř aplikace se provádí v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d03c0-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="d03c0-161">Aplikace načte z `Configuration` a získá nastavení:</span><span class="sxs-lookup"><span data-stu-id="d03c0-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="d03c0-162">Existují rozšíření tohoto přístupu, aby byl proces robustnější, jako je například použití [vkládání závislostí](xref:fundamentals/dependency-injection) (di) k načtení služby s těmito hodnotami.</span><span class="sxs-lookup"><span data-stu-id="d03c0-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="d03c0-163">Metoda DI má k dispozici sadu konfiguračních objektů se silným typem.</span><span class="sxs-lookup"><span data-stu-id="d03c0-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="d03c0-164">Podrobnější informace o konfiguraci ASP.NET Core najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d03c0-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="d03c0-165">Vkládání nativních závislostí</span><span class="sxs-lookup"><span data-stu-id="d03c0-165">Native dependency injection</span></span>

<span data-ttu-id="d03c0-166">Důležitým cílem při sestavování rozsáhlých škálovatelných aplikací je volně přizpůsobitelné spojování komponent a služeb.</span><span class="sxs-lookup"><span data-stu-id="d03c0-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="d03c0-167">[Vkládání závislostí](xref:fundamentals/dependency-injection) je oblíbená technika pro dosažení tohoto a je to nativní součást ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d03c0-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="d03c0-168">V aplikacích ASP.NET se vývojáři spoléhají na knihovnu třetí strany, která implementuje vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="d03c0-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="d03c0-169">Jedna taková knihovna je [Unity](https://github.com/unitycontainer/unity), kterou poskytují vzory Microsoftu & postupy.</span><span class="sxs-lookup"><span data-stu-id="d03c0-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="d03c0-170">Příkladem nastavení injektáže závislosti s Unity je implementace `IDependencyResolver` , která zabalí `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="d03c0-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="d03c0-171">Vytvořte instanci své `UnityContainer` služby, Zaregistrujte svoji službu a nastavte překladač závislostí `HttpConfiguration` na novou instanci `UnityResolver` pro svůj kontejner:</span><span class="sxs-lookup"><span data-stu-id="d03c0-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="d03c0-172">Vložení `IProductRepository` tam, kde je potřeba:</span><span class="sxs-lookup"><span data-stu-id="d03c0-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="d03c0-173">Protože vkládání závislostí je součástí ASP.NET Core, můžete přidat službu v `ConfigureServices` metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d03c0-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="d03c0-174">Úložiště může být vloženo kdekoli, stejně jako u Unity.</span><span class="sxs-lookup"><span data-stu-id="d03c0-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="d03c0-175">Další informace o vkládání závislostí naleznete v tématu [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d03c0-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="d03c0-176">Obsluhovat statické soubory</span><span class="sxs-lookup"><span data-stu-id="d03c0-176">Serve static files</span></span>

<span data-ttu-id="d03c0-177">Důležitou součástí vývoje webu je schopnost poskytovat statické prostředky na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d03c0-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="d03c0-178">Nejběžnějšími příklady statických souborů jsou HTML, CSS, JavaScript a image.</span><span class="sxs-lookup"><span data-stu-id="d03c0-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="d03c0-179">Tyto soubory musí být uloženy v publikovaném umístění aplikace (nebo CDN) a odkazovány, aby je bylo možné načíst pomocí žádosti.</span><span class="sxs-lookup"><span data-stu-id="d03c0-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="d03c0-180">Tento proces se v ASP.NET Core změnil.</span><span class="sxs-lookup"><span data-stu-id="d03c0-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="d03c0-181">V ASP.NET jsou statické soubory uloženy v různých adresářích a odkazovány v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="d03c0-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="d03c0-182">V ASP.NET Core jsou statické soubory uloženy v kořenu "Web root" (\* &lt; kořenový adresář obsahu &gt; /wwwroot\*), pokud není nakonfigurováno jinak.</span><span class="sxs-lookup"><span data-stu-id="d03c0-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="d03c0-183">Soubory jsou načteny do kanálu požadavků vyvoláním `UseStaticFiles` metody rozšíření z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d03c0-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="d03c0-184">Pokud cílíte .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="d03c0-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="d03c0-185">Například prostředek obrázku ve složce *wwwroot/images* je přístupný prohlížeči v umístění, jako je například `http://<app>/images/<imageFileName>` .</span><span class="sxs-lookup"><span data-stu-id="d03c0-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="d03c0-186">Podrobnější informace o obsluze statických souborů v ASP.NET Core najdete v tématu [statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="d03c0-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-no-loccookies"></a><span data-ttu-id="d03c0-187">S více hodnotami cookie</span><span class="sxs-lookup"><span data-stu-id="d03c0-187">Multi-value cookies</span></span>

<span data-ttu-id="d03c0-188">V ASP.NET Core nejsou podporovány [hodnoty cookie s více hodnotami](xref:System.Web.HttpCookie.Values) .</span><span class="sxs-lookup"><span data-stu-id="d03c0-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="d03c0-189">Vytvořte jednu cookie na hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="d03c0-190">Migrace částečné aplikace</span><span class="sxs-lookup"><span data-stu-id="d03c0-190">Partial app migration</span></span>

<span data-ttu-id="d03c0-191">Jedním z možností migrace částečné aplikace je vytvoření podaplikace služby IIS a přesunutí určitých tras z ASP.NET 4. x na ASP.NET Core a zachování struktury adresy URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="d03c0-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="d03c0-192">Zvažte například strukturu adresy URL aplikace ze souboru *applicationHost.config* :</span><span class="sxs-lookup"><span data-stu-id="d03c0-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

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

<span data-ttu-id="d03c0-193">Adresářová struktura:</span><span class="sxs-lookup"><span data-stu-id="d03c0-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a><span data-ttu-id="d03c0-194">[BIND] a vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="d03c0-194">[BIND] and Input Formatters</span></span>

<span data-ttu-id="d03c0-195">[Předchozí verze ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) použily `[Bind]` atribut k ochraně před útoky, které se přestavují.</span><span class="sxs-lookup"><span data-stu-id="d03c0-195">[Previous versions of ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) used the `[Bind]` attribute to protect against overposting attacks.</span></span> <span data-ttu-id="d03c0-196">[Vstupní formátovací](xref:mvc/models/model-binding#input-formatters) moduly fungují jinak v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d03c0-196">[Input formatters](xref:mvc/models/model-binding#input-formatters) work differently in ASP.NET Core.</span></span> <span data-ttu-id="d03c0-197">`[Bind]`Atribut již není navržen tak, aby zabránil přestavení při použití se vstupními formátovacími moduly k analýze formátu JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="d03c0-197">The `[Bind]` attribute is no longer designed to prevent overposting when used with input formatters to parse JSON or XML.</span></span> <span data-ttu-id="d03c0-198">Tyto atributy ovlivňují vazbu modelu, pokud je zdrojem dat formulářová data publikovaná s `x-www-form-urlencoded` typem obsahu.</span><span class="sxs-lookup"><span data-stu-id="d03c0-198">These attributes affect model binding when the source of data is form data posted with the `x-www-form-urlencoded` content type.</span></span>

<span data-ttu-id="d03c0-199">Pro aplikace, které odesílají informace JSON na řadiče a používají vstupní formátovací moduly JSON k analýze dat, doporučujeme, abyste atribut nahradili `[Bind]` modelem zobrazení, který odpovídá vlastnostem definovaným `[Bind]` atributem.</span><span class="sxs-lookup"><span data-stu-id="d03c0-199">For apps that post JSON information to controllers and use JSON Input Formatters to parse the data, we recommend replacing the `[Bind]` attribute with a view model that matches the properties defined by the `[Bind]` attribute.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d03c0-200">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d03c0-200">Additional resources</span></span>

- [<span data-ttu-id="d03c0-201">Portování knihoven do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d03c0-201">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
