---
title: Migrace z ASP.NET do ASP.NET Core
author: isaac2004
description: Získat pokyny pro migraci stávajících rozhraní ASP.NET MVC nebo webového rozhraní API aplikací na Core.web technologie ASP.NET
ms.author: scaddie
ms.date: 12/10/2018
uid: migration/proper-to-2x/index
ms.openlocfilehash: 6808fefb890dcdec6abdd0604ab61dfd2573d910
ms.sourcegitcommit: 1872d2e6f299093c78a6795a486929ffb0bbffff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53216791"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="8dbd1-103">Migrace z ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8dbd1-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="8dbd1-104">Podle [Petr Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="8dbd1-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="8dbd1-105">Tento článek slouží jako referenční příručka pro migraci aplikace v ASP.NET do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8dbd1-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8dbd1-106">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

## <a name="target-frameworks"></a><span data-ttu-id="8dbd1-107">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="8dbd1-107">Target frameworks</span></span>

<span data-ttu-id="8dbd1-108">Projekty ASP.NET Core nabízí vývojářům flexibilitu cílí na .NET Core a .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-108">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="8dbd1-109">Zobrazit [volba mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server) k určení, která Cílová architektura je nejvhodnější.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-109">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="8dbd1-110">Při cílení na rozhraní .NET Framework, projektech muset odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-110">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="8dbd1-111">Cílení na .NET Core umožňuje eliminovat řadu odkazy na balíček explicitní, díky technologii ASP.NET Core [Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="8dbd1-111">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="8dbd1-112">Nainstalujte `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all ve vašem projektu:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-112">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="8dbd1-113">Při použití Microsoft.aspnetcore.all žádné balíčky odkazuje Microsoft.aspnetcore.all nasazených s aplikací.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-113">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="8dbd1-114">Store modulu Runtime .NET Core zahrnuje tyto prostředky a že předkompilovaný ke zlepšení výkonu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-114">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="8dbd1-115">Zobrazit [metabalíček Microsoft.aspnetcore.all pro ASP.NET Core 2.x](xref:fundamentals/metapackage) další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-115">See [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x](xref:fundamentals/metapackage) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="8dbd1-116">Rozdíly struktura projektu</span><span class="sxs-lookup"><span data-stu-id="8dbd1-116">Project structure differences</span></span>

<span data-ttu-id="8dbd1-117">*.Csproj* zjednodušili jsme formát souborů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-117">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="8dbd1-118">Některé důležité změny patří:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-118">Some notable changes include:</span></span>

- <span data-ttu-id="8dbd1-119">Explicitní zařazení souborů není nezbytné pro ně být považováno za součást projektu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-119">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="8dbd1-120">Tím se snižuje riziko konfliktů sloučení XML při práci na velkých týmů.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-120">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="8dbd1-121">Neexistují žádné na základě identifikátoru GUID odkazy na jiné projekty, což zlepšuje čitelnost souboru.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-121">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="8dbd1-122">Tento soubor lze upravovat bez uvolnění v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-122">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Upravit CSPROJ možnost místní nabídky v sadě Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="8dbd1-124">Nahrazení souboru Global.asax</span><span class="sxs-lookup"><span data-stu-id="8dbd1-124">Global.asax file replacement</span></span>

<span data-ttu-id="8dbd1-125">ASP.NET Core zavedl nový mechanismus pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-125">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="8dbd1-126">Vstupní bod pro aplikace ASP.NET je *Global.asax* souboru.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-126">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="8dbd1-127">Úlohy, jako je konfigurace směrování a filtrování a oblasti registrace zachází *Global.asax* souboru.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-127">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="8dbd1-128">Tento přístup páry v odstupu aplikace a serveru, na kterém je nasazená tak, že dochází ke kolizím s implementací.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-128">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="8dbd1-129">Abyste mohli oddělit [OWIN](http://owin.org/) se seznámili s čistší způsob, jak použít více platforem současně.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-129">In an effort to decouple, [OWIN](http://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="8dbd1-130">OWIN poskytuje kanál přidat pouze moduly, které jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-130">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="8dbd1-131">Hostitelské prostředí přijímá [spuštění](xref:fundamentals/startup) funkce ke konfiguraci služby a kanál žádosti o aplikace.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-131">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="8dbd1-132">`Startup` registruje sadu middlewaru v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-132">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="8dbd1-133">Pro každý požadavek aplikace volá všechny komponenty middlewaru pomocí hlavního ukazatele odkazovaného seznamu do existující sady rutin.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-133">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="8dbd1-134">Každá komponenta middlewaru můžete přidat jeden nebo více obslužných rutin k žádosti o zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-134">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="8dbd1-135">Toho dosahuje tak, že vrací odkaz na obslužnou rutinu, která je na novou hlavičku seznamu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-135">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="8dbd1-136">Každá obslužná rutina zodpovídá za zapamatování a vyvolání další obslužná rutina v seznamu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-136">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="8dbd1-137">Pomocí ASP.NET Core je vstupním bodem k aplikaci `Startup`, a už nebude mít závislost *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-137">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="8dbd1-138">Při použití s rozhraním .NET Framework OWIN, použijte jako kanál nějak takto:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-138">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="8dbd1-139">Nakonfiguruje výchozí trasy a výchozí hodnota je XmlSerialization přes Json.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-139">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="8dbd1-140">Podle potřeby (načítání služeb, nastavení konfigurace, statické soubory, atd.), přidejte do tohoto kanálu další Middleware.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-140">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="8dbd1-141">ASP.NET Core používá podobný přístup, ale nemusí spoléhat na OWIN pro zpracování vstupu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-141">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="8dbd1-142">Místo toho, který se provádí prostřednictvím *Program.cs* `Main` – metoda (podobně jako konzolové aplikace) a `Startup` je načteno do něj.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-142">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="8dbd1-143">`Startup` musí obsahovat `Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-143">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="8dbd1-144">V `Configure`, přidat nezbytné middleware do kanálu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-144">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="8dbd1-145">V následujícím příkladu (z výchozí šablony webové stránky) několik rozšiřující metody slouží ke konfiguraci kanálu s podporou:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-145">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="8dbd1-146">Browser Link</span><span class="sxs-lookup"><span data-stu-id="8dbd1-146">Browser Link</span></span>](xref:client-side/using-browserlink)
* <span data-ttu-id="8dbd1-147">Chybové stránky</span><span class="sxs-lookup"><span data-stu-id="8dbd1-147">Error pages</span></span>
* <span data-ttu-id="8dbd1-148">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="8dbd1-148">Static files</span></span>
* <span data-ttu-id="8dbd1-149">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8dbd1-149">ASP.NET Core MVC</span></span>
* <span data-ttu-id="8dbd1-150">Identita</span><span class="sxs-lookup"><span data-stu-id="8dbd1-150">Identity</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="8dbd1-151">Host a aplikace byla odděleném poskytující možnost přechodu na různé platformy v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="8dbd1-152">Najdete podrobnější referenční dokumentace k ASP.NET Core spuštění a Middleware, naleznete v tématu [při spuštění v ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="8dbd1-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="8dbd1-153">Konfigurace Store</span><span class="sxs-lookup"><span data-stu-id="8dbd1-153">Store configurations</span></span>

<span data-ttu-id="8dbd1-154">Podporuje ASP.NET ukládat nastavení.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="8dbd1-155">Tato nastavení se používají, například pro podporu prostředí, do kterého byly nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="8dbd1-156">Běžnou praxí je pro uložení všech vlastních páry klíč hodnota v `<appSettings>` část *Web.config* souboru:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="8dbd1-157">Číst nastavení pomocí aplikací `ConfigurationManager.AppSettings` kolekce `System.Configuration` obor názvů:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="8dbd1-158">ASP.NET Core můžete uložit konfigurační data pro aplikaci na jakýkoli soubor a načíst jako součást spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="8dbd1-159">Je výchozí soubor použitý v šablonách projektů *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="8dbd1-160">Načítají se tento soubor do instance `IConfiguration` uvnitř vaší aplikace se provádí v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="8dbd1-161">Aplikace načte z `Configuration` zobrazíte nastavení:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="8dbd1-162">Rozšíření tohoto přístupu, aby proces robustnější, jako je třeba použití [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) pro načtení služby s těmito hodnotami.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="8dbd1-163">DI přístup poskytuje sadu objektů konfigurace silného typu.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

> [!NOTE]
> <span data-ttu-id="8dbd1-164">Podrobnější odkazu ke konfiguraci ASP.NET Core, najdete v části [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8dbd1-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="8dbd1-165">Injektáž závislostí nativní</span><span class="sxs-lookup"><span data-stu-id="8dbd1-165">Native dependency injection</span></span>

<span data-ttu-id="8dbd1-166">Důležité cíle, při vytváření velké a škálovatelné aplikace je volné párování komponent a služeb.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="8dbd1-167">[Injektáž závislostí](xref:fundamentals/dependency-injection) je technika oblíbených pro dosažení tohoto cíle a jedná se o nativní součást ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="8dbd1-168">V aplikacích technologie ASP.NET vývojáři spoléhají na knihovny třetích stran k implementaci vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="8dbd1-169">Jeden takový knihovna je [Unity](https://github.com/unitycontainer/unity), k dispozici ve Microsoft Patterns a postupy.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="8dbd1-170">Příklad nastavení injektáž závislostí v Unity je implementace `IDependencyResolver` , která zabalí `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]

<span data-ttu-id="8dbd1-171">Vytvoření instance vaší `UnityContainer`, zaregistrujte vaši službu a nastavit překladač závislostí `HttpConfiguration` nové instance `UnityResolver` kontejneru:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]

<span data-ttu-id="8dbd1-172">Vložit `IProductRepository` místech:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]

<span data-ttu-id="8dbd1-173">Protože injektáž závislostí je součástí ASP.NET Core, můžete přidat služby v `ConfigureServices` metoda *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="8dbd1-174">Úložiště lze vloženy kdekoliv, protože dřív platilo pomocí Unity.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="8dbd1-175">Další informace o vkládání závislostí, naleznete v tématu [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8dbd1-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="8dbd1-176">Doručování statických souborů</span><span class="sxs-lookup"><span data-stu-id="8dbd1-176">Serve static files</span></span>

<span data-ttu-id="8dbd1-177">Důležitou součástí vývoje pro web je schopnost poskytovat statické a na straně klienta prostředky.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="8dbd1-178">Většina běžných příkladů statické soubory jsou HTML, CSS, Javascript a bitové kopie.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="8dbd1-179">Tyto soubory musí uložený v publikované umístění aplikace (nebo CDN) a odkazovat tak je možné načíst žádostí.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="8dbd1-180">Tento proces byl změněn v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="8dbd1-181">V technologii ASP.NET jsou statické soubory uložené v různých adresářích a odkazované v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="8dbd1-182">V ASP.NET Core, statické soubory se ukládají do "kořenový adresář webové" (*&lt;obsahu kořenové&gt;/wwwroot*), pokud se nenakonfiguruje.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="8dbd1-183">Soubory se načtou do kanálu požadavku vyvoláním `UseStaticFiles` rozšiřující metoda z `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="8dbd1-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="8dbd1-184">Pokud se zaměřujete na rozhraní .NET Framework, nainstalujte balíček NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="8dbd1-185">Například prostředek obrázku v *wwwroot/imagí* , jako je přístupná v prohlížeči v umístění složka `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="8dbd1-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="8dbd1-186">Podrobnější odkaz na zpracování statických souborů v ASP.NET Core, najdete v části [statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="8dbd1-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8dbd1-187">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8dbd1-187">Additional resources</span></span>

- [<span data-ttu-id="8dbd1-188">Přenos knihoven pro .NET Core</span><span class="sxs-lookup"><span data-stu-id="8dbd1-188">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
