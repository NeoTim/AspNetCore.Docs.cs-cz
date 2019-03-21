---
title: Migrace z ASP.NET do ASP.NET Core 2.0
author: isaac2004
description: Získat pokyny pro migraci stávajících rozhraní ASP.NET MVC nebo webového rozhraní API aplikací pro ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/mvc2
ms.openlocfilehash: 7f048f2f95f1a51a0b6ce3d36665420ff28ec26f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208470"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="b476a-103">Migrace z ASP.NET do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="b476a-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="b476a-104">Podle [Petr Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="b476a-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="b476a-105">Tento článek slouží jako referenční příručka pro migraci aplikací technologie ASP.NET do ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="b476a-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b476a-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b476a-106">Prerequisites</span></span>

<span data-ttu-id="b476a-107">Nainstalujte **jeden** z těchto věcí [.NET soubory ke stažení: Windows](https://www.microsoft.com/net/download/windows):</span><span class="sxs-lookup"><span data-stu-id="b476a-107">Install **one** of the following from [.NET Downloads: Windows](https://www.microsoft.com/net/download/windows):</span></span>

* <span data-ttu-id="b476a-108">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="b476a-108">.NET Core SDK</span></span>
* <span data-ttu-id="b476a-109">Visual Studio pro Windows</span><span class="sxs-lookup"><span data-stu-id="b476a-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="b476a-110">**Vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="b476a-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="b476a-111">**Vývoj pro různé platformy .NET core** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="b476a-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="b476a-112">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="b476a-112">Target frameworks</span></span>

<span data-ttu-id="b476a-113">Projekty ASP.NET Core 2.0 nabízejí vývojářům možnost cílení na .NET Core a .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b476a-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="b476a-114">Zobrazit [volba mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) k určení, která Cílová architektura je nejvhodnější.</span><span class="sxs-lookup"><span data-stu-id="b476a-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="b476a-115">Při cílení na rozhraní .NET Framework, projektech muset odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="b476a-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="b476a-116">Cílení na .NET Core umožňuje eliminovat řadu odkazy na balíček explicitní, díky ASP.NET Core 2.0 [Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="b476a-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="b476a-117">Nainstalujte `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all ve vašem projektu:</span><span class="sxs-lookup"><span data-stu-id="b476a-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="b476a-118">Při použití Microsoft.aspnetcore.all žádné balíčky odkazuje Microsoft.aspnetcore.all nasazených s aplikací.</span><span class="sxs-lookup"><span data-stu-id="b476a-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="b476a-119">Store modulu Runtime .NET Core zahrnuje tyto prostředky a že předkompilovaný ke zlepšení výkonu.</span><span class="sxs-lookup"><span data-stu-id="b476a-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="b476a-120">Zobrazit <xref:fundamentals/metapackage> další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="b476a-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="b476a-121">Rozdíly struktura projektu</span><span class="sxs-lookup"><span data-stu-id="b476a-121">Project structure differences</span></span>

<span data-ttu-id="b476a-122">*.Csproj* zjednodušili jsme formát souborů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b476a-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="b476a-123">Některé důležité změny patří:</span><span class="sxs-lookup"><span data-stu-id="b476a-123">Some notable changes include:</span></span>

* <span data-ttu-id="b476a-124">Explicitní zařazení souborů není nezbytné pro ně být považováno za součást projektu.</span><span class="sxs-lookup"><span data-stu-id="b476a-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="b476a-125">Tím se snižuje riziko konfliktů sloučení XML při práci na velkých týmů.</span><span class="sxs-lookup"><span data-stu-id="b476a-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="b476a-126">Neexistují žádné na základě identifikátoru GUID odkazy na jiné projekty, což zlepšuje čitelnost souboru.</span><span class="sxs-lookup"><span data-stu-id="b476a-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="b476a-127">Tento soubor lze upravovat bez uvolnění v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b476a-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Edit CSPROJ context menu option in Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="b476a-129">Nahrazení souboru Global.asax</span><span class="sxs-lookup"><span data-stu-id="b476a-129">Global.asax file replacement</span></span>

<span data-ttu-id="b476a-130">ASP.NET Core zavedl nový mechanismus pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="b476a-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="b476a-131">Vstupní bod pro aplikace ASP.NET je *Global.asax* souboru.</span><span class="sxs-lookup"><span data-stu-id="b476a-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="b476a-132">Úlohy, jako je konfigurace směrování a filtrování a oblasti registrace zachází *Global.asax* souboru.</span><span class="sxs-lookup"><span data-stu-id="b476a-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="b476a-133">Tento přístup páry v odstupu aplikace a serveru, na kterém je nasazená tak, že dochází ke kolizím s implementací.</span><span class="sxs-lookup"><span data-stu-id="b476a-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="b476a-134">Abyste mohli oddělit [OWIN](http://owin.org/) se seznámili s čistší způsob, jak použít více platforem současně.</span><span class="sxs-lookup"><span data-stu-id="b476a-134">In an effort to decouple, [OWIN](http://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="b476a-135">OWIN poskytuje kanál přidat pouze moduly, které jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="b476a-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="b476a-136">Hostitelské prostředí přijímá [spuštění](xref:fundamentals/startup) funkce ke konfiguraci služby a kanál žádosti o aplikace.</span><span class="sxs-lookup"><span data-stu-id="b476a-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="b476a-137">`Startup` registruje sadu middlewaru v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b476a-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="b476a-138">Pro každý požadavek aplikace volá všechny komponenty middlewaru pomocí hlavního ukazatele odkazovaného seznamu do existující sady rutin.</span><span class="sxs-lookup"><span data-stu-id="b476a-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="b476a-139">Každá komponenta middlewaru můžete přidat jeden nebo více obslužných rutin k žádosti o zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="b476a-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="b476a-140">Toho dosahuje tak, že vrací odkaz na obslužnou rutinu, která je na novou hlavičku seznamu.</span><span class="sxs-lookup"><span data-stu-id="b476a-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="b476a-141">Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužná rutina v seznamu.</span><span class="sxs-lookup"><span data-stu-id="b476a-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="b476a-142">Pomocí ASP.NET Core je vstupním bodem k aplikaci `Startup`, a už nebude mít závislost *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="b476a-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="b476a-143">Při použití s rozhraním .NET Framework OWIN, použijte jako kanál nějak takto:</span><span class="sxs-lookup"><span data-stu-id="b476a-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="b476a-144">Nakonfiguruje výchozí trasy a výchozí hodnota je XmlSerialization přes Json.</span><span class="sxs-lookup"><span data-stu-id="b476a-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="b476a-145">Podle potřeby (načítání služeb, nastavení konfigurace, statické soubory, atd.), přidejte do tohoto kanálu další Middleware.</span><span class="sxs-lookup"><span data-stu-id="b476a-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="b476a-146">ASP.NET Core používá podobný přístup, ale nemusí spoléhat na OWIN pro zpracování vstupu.</span><span class="sxs-lookup"><span data-stu-id="b476a-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="b476a-147">Místo toho, který se provádí prostřednictvím *Program.cs* `Main` – metoda (podobně jako konzolové aplikace) a `Startup` je načteno do něj.</span><span class="sxs-lookup"><span data-stu-id="b476a-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="b476a-148">`Startup` musí obsahovat `Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="b476a-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="b476a-149">V `Configure`, přidat nezbytné middleware do kanálu.</span><span class="sxs-lookup"><span data-stu-id="b476a-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="b476a-150">V následujícím příkladu (z výchozí šablony webové stránky) několik rozšiřující metody slouží ke konfiguraci kanálu s podporou:</span><span class="sxs-lookup"><span data-stu-id="b476a-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="b476a-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="b476a-151">BrowserLink</span></span>](http://vswebessentials.com/features/browserlink)
* <span data-ttu-id="b476a-152">Chybové stránky</span><span class="sxs-lookup"><span data-stu-id="b476a-152">Error pages</span></span>
* <span data-ttu-id="b476a-153">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="b476a-153">Static files</span></span>
* <span data-ttu-id="b476a-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="b476a-154">ASP.NET Core MVC</span></span>
* <span data-ttu-id="b476a-155">Identita</span><span class="sxs-lookup"><span data-stu-id="b476a-155">Identity</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="b476a-156">Host a aplikace byla odděleném poskytující možnost přechodu na různé platformy v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="b476a-156">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="b476a-157">Najdete podrobnější referenční dokumentace k ASP.NET Core spuštění a Middleware, naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b476a-157">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="b476a-158">Ukládání konfigurace</span><span class="sxs-lookup"><span data-stu-id="b476a-158">Storing configurations</span></span>

<span data-ttu-id="b476a-159">Podporuje ASP.NET ukládat nastavení.</span><span class="sxs-lookup"><span data-stu-id="b476a-159">ASP.NET supports storing settings.</span></span> <span data-ttu-id="b476a-160">Tato nastavení se používají, například pro podporu prostředí, do kterého byly nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b476a-160">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="b476a-161">Běžnou praxí je pro uložení všech vlastních páry klíč hodnota v `<appSettings>` část *Web.config* souboru:</span><span class="sxs-lookup"><span data-stu-id="b476a-161">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="b476a-162">Číst nastavení pomocí aplikací `ConfigurationManager.AppSettings` kolekce `System.Configuration` obor názvů:</span><span class="sxs-lookup"><span data-stu-id="b476a-162">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="b476a-163">ASP.NET Core můžete uložit konfigurační data pro aplikaci na jakýkoli soubor a načíst jako součást spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="b476a-163">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="b476a-164">Je výchozí soubor použitý v šablonách projektů *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b476a-164">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="b476a-165">Načítají se tento soubor do instance `IConfiguration` uvnitř vaší aplikace se provádí v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b476a-165">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="b476a-166">Aplikace načte z `Configuration` zobrazíte nastavení:</span><span class="sxs-lookup"><span data-stu-id="b476a-166">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="b476a-167">Rozšíření tohoto přístupu, aby proces robustnější, jako je třeba použití [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) pro načtení služby s těmito hodnotami.</span><span class="sxs-lookup"><span data-stu-id="b476a-167">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="b476a-168">DI přístup poskytuje sadu objektů konfigurace silného typu.</span><span class="sxs-lookup"><span data-stu-id="b476a-168">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

<span data-ttu-id="b476a-169">**Poznámka:** Podrobnější odkazu ke konfiguraci ASP.NET Core, najdete v části <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b476a-169">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="b476a-170">Injektáž závislostí nativní</span><span class="sxs-lookup"><span data-stu-id="b476a-170">Native dependency injection</span></span>

<span data-ttu-id="b476a-171">Důležité cíle, při vytváření velké a škálovatelné aplikace je volné párování komponent a služeb.</span><span class="sxs-lookup"><span data-stu-id="b476a-171">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="b476a-172">[Injektáž závislostí](xref:fundamentals/dependency-injection) je technika oblíbených pro dosažení tohoto cíle a jedná se o nativní součást ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b476a-172">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="b476a-173">V aplikacích ASP.NET vývojáři spoléhají na knihovny třetích stran k implementaci vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="b476a-173">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="b476a-174">Jeden takový knihovna je [Unity](https://github.com/unitycontainer/unity), k dispozici ve Microsoft Patterns a postupy.</span><span class="sxs-lookup"><span data-stu-id="b476a-174">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="b476a-175">Příklad nastavení injektáž závislostí v Unity je implementace `IDependencyResolver` , která zabalí `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="b476a-175">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="b476a-176">Vytvoření instance vaší `UnityContainer`, zaregistrujte vaši službu a nastavit překladač závislostí `HttpConfiguration` nové instance `UnityResolver` kontejneru:</span><span class="sxs-lookup"><span data-stu-id="b476a-176">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="b476a-177">Vložit `IProductRepository` místech:</span><span class="sxs-lookup"><span data-stu-id="b476a-177">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="b476a-178">Protože injektáž závislostí je součástí ASP.NET Core, můžete přidat služby v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b476a-178">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="b476a-179">Úložiště lze vloženy kdekoliv, protože dřív platilo pomocí Unity.</span><span class="sxs-lookup"><span data-stu-id="b476a-179">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="b476a-180">Další informace o injektáž závislostí v ASP.NET Core najdete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b476a-180">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="b476a-181">Zpracování statických souborů.</span><span class="sxs-lookup"><span data-stu-id="b476a-181">Serving static files</span></span>

<span data-ttu-id="b476a-182">Důležitou součástí vývoje pro web je schopnost poskytovat statické a na straně klienta prostředky.</span><span class="sxs-lookup"><span data-stu-id="b476a-182">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="b476a-183">Většina běžných příkladů statické soubory jsou HTML, CSS, Javascript a bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="b476a-183">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="b476a-184">Tyto soubory musí uložený v publikované umístění aplikace (nebo CDN) a odkazovat tak je možné načíst žádostí.</span><span class="sxs-lookup"><span data-stu-id="b476a-184">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="b476a-185">Tento proces byl změněn v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b476a-185">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="b476a-186">V technologii ASP.NET jsou statické soubory uložené v různých adresářích a odkazované v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="b476a-186">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="b476a-187">V ASP.NET Core, statické soubory se ukládají do "kořenový adresář webové" (*&lt;obsahu kořenové&gt;/wwwroot*), pokud se nenakonfiguruje.</span><span class="sxs-lookup"><span data-stu-id="b476a-187">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="b476a-188">Soubory se načtou do kanálu požadavku vyvoláním `UseStaticFiles` rozšiřující metoda z `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b476a-188">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="b476a-189">**Poznámka:** Pokud se zaměřujete na rozhraní .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="b476a-189">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="b476a-190">Například prostředek obrázku v *wwwroot/imagí* , jako je přístupná v prohlížeči v umístění složka `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="b476a-190">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="b476a-191">**Poznámka:** Podrobnější odkaz na zpracování statických souborů v ASP.NET Core, najdete v části <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="b476a-191">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b476a-192">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b476a-192">Additional resources</span></span>

* [<span data-ttu-id="b476a-193">Přenos knihoven pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="b476a-193">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
