---
title: Poskytovatelé souborů v ASP.NET Core
author: guardrex
description: Přečtěte si, jak ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: a454ca394546184968222ca2ca44d7159b19a12a
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944305"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="fc504-103">Poskytovatelé souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc504-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="fc504-104">[Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc504-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc504-105">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="fc504-106">Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:</span><span class="sxs-lookup"><span data-stu-id="fc504-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="fc504-107">`IWebHostEnvironment` zpřístupňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a [kořenový adresář webu](xref:fundamentals/index#web-root) jako typy `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-107">`IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="fc504-108">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="fc504-109">[Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="fc504-110">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="fc504-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="fc504-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc504-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="fc504-112">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="fc504-112">File Provider interfaces</span></span>

<span data-ttu-id="fc504-113">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc504-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="fc504-114">`IFileProvider` zveřejňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="fc504-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="fc504-115">Získat informace o souboru (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="fc504-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="fc504-116">Získání informací o adresáři (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="fc504-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="fc504-117">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="fc504-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="fc504-118">`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="fc504-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="fc504-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (v bajtech)</span><span class="sxs-lookup"><span data-stu-id="fc504-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="fc504-120">Datum <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="fc504-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="fc504-121">Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="fc504-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="fc504-122">Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc504-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="fc504-123">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="fc504-123">File Provider implementations</span></span>

<span data-ttu-id="fc504-124">K dispozici jsou tři implementace `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="fc504-125">Implementace</span><span class="sxs-lookup"><span data-stu-id="fc504-125">Implementation</span></span> | <span data-ttu-id="fc504-126">Popis</span><span class="sxs-lookup"><span data-stu-id="fc504-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="fc504-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="fc504-128">Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="fc504-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="fc504-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="fc504-130">Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="fc504-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="fc504-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="fc504-132">Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="fc504-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="fc504-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-133">PhysicalFileProvider</span></span>

<span data-ttu-id="fc504-134"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="fc504-135">`PhysicalFileProvider` používá typ <xref:System.IO.File?displayProperty=fullName> (pro fyzického poskytovatele) a rozsahy všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="fc504-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="fc504-136">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="fc504-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="fc504-137">Nejběžnější scénář pro vytváření a používání `PhysicalFileProvider` je vyžádání `IFileProvider` v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc504-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="fc504-138">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="fc504-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="fc504-139">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="fc504-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="fc504-140">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fc504-140">Types in the preceding example:</span></span>

* <span data-ttu-id="fc504-141">`provider` je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="fc504-142">`contents` je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="fc504-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="fc504-143">`fileInfo` je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="fc504-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="fc504-144">Zprostředkovatele souboru lze použít k iterování adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="fc504-145">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="fc504-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="fc504-146">Ukázková aplikace vytvoří zprostředkovatele v `Startup.ConfigureServices` třídy aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="fc504-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="fc504-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="fc504-148"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se používá pro přístup k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="fc504-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="fc504-149">`ManifestEmbeddedFileProvider` používá manifest kompilovaný do sestavení pro rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="fc504-150">Přidejte odkaz na balíček do projektu pro balíček [Microsoft. Extensions. Providers. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) .</span><span class="sxs-lookup"><span data-stu-id="fc504-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="fc504-151">Chcete-li vygenerovat manifest vložených souborů, nastavte vlastnost `<GenerateEmbeddedFilesManifest>` na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="fc504-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="fc504-152">Zadejte soubory pro vložení [\<EmbeddedResource](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="fc504-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="fc504-153">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="fc504-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="fc504-154">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="fc504-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="fc504-155">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc504-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="fc504-156">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="fc504-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="fc504-157">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-157">Specify a relative file path.</span></span>
* <span data-ttu-id="fc504-158">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="fc504-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="fc504-159">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="fc504-160">Přetížení</span><span class="sxs-lookup"><span data-stu-id="fc504-160">Overload</span></span> | <span data-ttu-id="fc504-161">Popis</span><span class="sxs-lookup"><span data-stu-id="fc504-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="fc504-162">Přijímá volitelný parametr relativní cesty `root`.</span><span class="sxs-lookup"><span data-stu-id="fc504-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="fc504-163">Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="fc504-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="fc504-164">Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="fc504-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="fc504-165">Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc504-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="fc504-166">Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="fc504-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="fc504-167">`manifestName` představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="fc504-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="fc504-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-168">CompositeFileProvider</span></span>

<span data-ttu-id="fc504-169"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombinuje `IFileProvider` instancí a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="fc504-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="fc504-170">Při vytváření `CompositeFileProvider`předat konstruktoru jednu nebo více instancí `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="fc504-171">V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytují soubory `CompositeFileProvider` zaregistrovaným v kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="fc504-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="fc504-172">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="fc504-172">Watch for changes</span></span>

<span data-ttu-id="fc504-173">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="fc504-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="fc504-174">`Watch` přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="fc504-175">`Watch` vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="fc504-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="fc504-176">Token pro změnu zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="fc504-176">The change token exposes:</span></span>

* <span data-ttu-id="fc504-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="fc504-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="fc504-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; volána, když jsou zjištěny změny do zadaného řetězce cesty.</span><span class="sxs-lookup"><span data-stu-id="fc504-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="fc504-179">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="fc504-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="fc504-180">Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (zobrazeno níže) nebo znovu vytvořte `IChangeToken` instancí v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="fc504-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="fc504-181">V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:</span><span class="sxs-lookup"><span data-stu-id="fc504-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="fc504-182">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="fc504-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="fc504-183">Nastavte proměnnou prostředí `DOTNET_USE_POLLING_FILE_WATCHER` na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="fc504-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="fc504-184">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="fc504-184">Glob patterns</span></span>

<span data-ttu-id="fc504-185">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* .</span><span class="sxs-lookup"><span data-stu-id="fc504-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="fc504-186">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="fc504-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="fc504-187">Dva zástupné znaky jsou `*` a `**`:</span><span class="sxs-lookup"><span data-stu-id="fc504-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="fc504-188">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="fc504-189">Shody jsou ukončeny `/` a `.` znaky v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="fc504-190">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="fc504-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="fc504-191">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="fc504-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="fc504-192">**Příklady vzorů glob**</span><span class="sxs-lookup"><span data-stu-id="fc504-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="fc504-193">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="fc504-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="fc504-194">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="fc504-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="fc504-195">Vyhledá všechny soubory `appsettings.json` v adresářích přesně jednu úroveň pod *adresářovou* složkou.</span><span class="sxs-lookup"><span data-stu-id="fc504-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="fc504-196">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="fc504-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fc504-197">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="fc504-198">Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:</span><span class="sxs-lookup"><span data-stu-id="fc504-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="fc504-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> zpřístupňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a [kořenový adresář webu](xref:fundamentals/index#web-root) jako typy `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="fc504-200">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="fc504-201">[Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="fc504-202">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="fc504-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="fc504-203">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc504-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="fc504-204">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="fc504-204">File Provider interfaces</span></span>

<span data-ttu-id="fc504-205">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc504-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="fc504-206">`IFileProvider` zveřejňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="fc504-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="fc504-207">Získat informace o souboru (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="fc504-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="fc504-208">Získání informací o adresáři (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="fc504-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="fc504-209">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="fc504-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="fc504-210">`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="fc504-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="fc504-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (v bajtech)</span><span class="sxs-lookup"><span data-stu-id="fc504-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="fc504-212">Datum <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="fc504-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="fc504-213">Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="fc504-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="fc504-214">Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc504-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="fc504-215">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="fc504-215">File Provider implementations</span></span>

<span data-ttu-id="fc504-216">K dispozici jsou tři implementace `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="fc504-217">Implementace</span><span class="sxs-lookup"><span data-stu-id="fc504-217">Implementation</span></span> | <span data-ttu-id="fc504-218">Popis</span><span class="sxs-lookup"><span data-stu-id="fc504-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="fc504-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="fc504-220">Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="fc504-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="fc504-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="fc504-222">Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="fc504-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="fc504-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="fc504-224">Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="fc504-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="fc504-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-225">PhysicalFileProvider</span></span>

<span data-ttu-id="fc504-226"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="fc504-227">`PhysicalFileProvider` používá typ <xref:System.IO.File?displayProperty=fullName> (pro fyzického poskytovatele) a rozsahy všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="fc504-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="fc504-228">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="fc504-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="fc504-229">Nejběžnější scénář pro vytváření a používání `PhysicalFileProvider` je vyžádání `IFileProvider` v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fc504-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="fc504-230">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="fc504-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="fc504-231">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="fc504-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="fc504-232">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fc504-232">Types in the preceding example:</span></span>

* <span data-ttu-id="fc504-233">`provider` je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="fc504-234">`contents` je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="fc504-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="fc504-235">`fileInfo` je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="fc504-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="fc504-236">Zprostředkovatele souboru lze použít k iterování adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="fc504-237">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="fc504-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="fc504-238">Ukázková aplikace vytvoří zprostředkovatele v `Startup.ConfigureServices` třídy aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="fc504-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="fc504-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="fc504-240"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se používá pro přístup k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="fc504-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="fc504-241">`ManifestEmbeddedFileProvider` používá manifest kompilovaný do sestavení pro rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="fc504-242">Chcete-li vygenerovat manifest vložených souborů, nastavte vlastnost `<GenerateEmbeddedFilesManifest>` na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="fc504-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="fc504-243">Zadejte soubory pro vložení [&gt;&lt;EmbeddedResource ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="fc504-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="fc504-244">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="fc504-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="fc504-245">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="fc504-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="fc504-246">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc504-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="fc504-247">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="fc504-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="fc504-248">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-248">Specify a relative file path.</span></span>
* <span data-ttu-id="fc504-249">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="fc504-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="fc504-250">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="fc504-251">Přetížení</span><span class="sxs-lookup"><span data-stu-id="fc504-251">Overload</span></span> | <span data-ttu-id="fc504-252">Popis</span><span class="sxs-lookup"><span data-stu-id="fc504-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="fc504-253">Přijímá volitelný parametr relativní cesty `root`.</span><span class="sxs-lookup"><span data-stu-id="fc504-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="fc504-254">Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="fc504-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="fc504-255">Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="fc504-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="fc504-256">Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="fc504-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="fc504-257">Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="fc504-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="fc504-258">`manifestName` představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="fc504-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="fc504-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="fc504-259">CompositeFileProvider</span></span>

<span data-ttu-id="fc504-260"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombinuje `IFileProvider` instancí a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="fc504-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="fc504-261">Při vytváření `CompositeFileProvider`předat konstruktoru jednu nebo více instancí `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="fc504-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="fc504-262">V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytují soubory `CompositeFileProvider` zaregistrovaným v kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="fc504-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="fc504-263">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="fc504-263">Watch for changes</span></span>

<span data-ttu-id="fc504-264">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="fc504-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="fc504-265">`Watch` přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="fc504-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="fc504-266">`Watch` vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="fc504-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="fc504-267">Token pro změnu zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="fc504-267">The change token exposes:</span></span>

* <span data-ttu-id="fc504-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="fc504-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="fc504-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; volána, když jsou zjištěny změny do zadaného řetězce cesty.</span><span class="sxs-lookup"><span data-stu-id="fc504-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="fc504-270">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="fc504-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="fc504-271">Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (zobrazeno níže) nebo znovu vytvořte `IChangeToken` instancí v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="fc504-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="fc504-272">V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:</span><span class="sxs-lookup"><span data-stu-id="fc504-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="fc504-273">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="fc504-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="fc504-274">Nastavte proměnnou prostředí `DOTNET_USE_POLLING_FILE_WATCHER` na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="fc504-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="fc504-275">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="fc504-275">Glob patterns</span></span>

<span data-ttu-id="fc504-276">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* .</span><span class="sxs-lookup"><span data-stu-id="fc504-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="fc504-277">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="fc504-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="fc504-278">Dva zástupné znaky jsou `*` a `**`:</span><span class="sxs-lookup"><span data-stu-id="fc504-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="fc504-279">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="fc504-280">Shody jsou ukončeny `/` a `.` znaky v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="fc504-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="fc504-281">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="fc504-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="fc504-282">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="fc504-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="fc504-283">**Příklady vzorů glob**</span><span class="sxs-lookup"><span data-stu-id="fc504-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="fc504-284">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="fc504-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="fc504-285">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="fc504-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="fc504-286">Vyhledá všechny soubory `appsettings.json` v adresářích přesně jednu úroveň pod *adresářovou* složkou.</span><span class="sxs-lookup"><span data-stu-id="fc504-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="fc504-287">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="fc504-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
