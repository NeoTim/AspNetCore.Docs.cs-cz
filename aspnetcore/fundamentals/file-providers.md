---
title: Poskytovatelé souborů v ASP.NET Core
author: guardrex
description: Přečtěte si, jak ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 44c439dce893d486668bf8ac3f20cdf7952c5186
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059096"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="a11ba-103">Poskytovatelé souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a11ba-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="a11ba-104">[Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a11ba-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a11ba-105">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="a11ba-106">Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a11ba-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="a11ba-107">`IWebHostEnvironment`zpřístupňuje kořenový adresář obsahu aplikace a webové kořenové adresáře `IFileProvider` jako typy.</span><span class="sxs-lookup"><span data-stu-id="a11ba-107">`IWebHostEnvironment` exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="a11ba-108">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="a11ba-109">[Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="a11ba-110">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="a11ba-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="a11ba-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a11ba-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="a11ba-112">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="a11ba-112">File Provider interfaces</span></span>

<span data-ttu-id="a11ba-113">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="a11ba-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="a11ba-114">`IFileProvider`Zpřístupňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="a11ba-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="a11ba-115">Získat informace o souboru<xref:Microsoft.Extensions.FileProviders.IFileInfo>().</span><span class="sxs-lookup"><span data-stu-id="a11ba-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="a11ba-116">Získat informace o adresáři<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>().</span><span class="sxs-lookup"><span data-stu-id="a11ba-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="a11ba-117">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="a11ba-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="a11ba-118">`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="a11ba-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="a11ba-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)</span><span class="sxs-lookup"><span data-stu-id="a11ba-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="a11ba-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum</span><span class="sxs-lookup"><span data-stu-id="a11ba-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="a11ba-121">Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="a11ba-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="a11ba-122">Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a11ba-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="a11ba-123">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="a11ba-123">File Provider implementations</span></span>

<span data-ttu-id="a11ba-124">K dispozici `IFileProvider` jsou tři implementace systému.</span><span class="sxs-lookup"><span data-stu-id="a11ba-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="a11ba-125">Implementace</span><span class="sxs-lookup"><span data-stu-id="a11ba-125">Implementation</span></span> | <span data-ttu-id="a11ba-126">Popis</span><span class="sxs-lookup"><span data-stu-id="a11ba-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="a11ba-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="a11ba-128">Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="a11ba-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="a11ba-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="a11ba-130">Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="a11ba-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="a11ba-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="a11ba-132">Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="a11ba-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-133">PhysicalFileProvider</span></span>

<span data-ttu-id="a11ba-134"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> Poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="a11ba-135">`PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> používá typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="a11ba-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="a11ba-136">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="a11ba-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="a11ba-137">Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro `IFileProvider` vyžádání v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a11ba-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a11ba-138">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="a11ba-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="a11ba-139">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="a11ba-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="a11ba-140">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a11ba-140">Types in the preceding example:</span></span>

* <span data-ttu-id="a11ba-141">`provider``IFileProvider`je.</span><span class="sxs-lookup"><span data-stu-id="a11ba-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="a11ba-142">`contents``IDirectoryContents`je.</span><span class="sxs-lookup"><span data-stu-id="a11ba-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="a11ba-143">`fileInfo``IFileInfo`je.</span><span class="sxs-lookup"><span data-stu-id="a11ba-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="a11ba-144">Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="a11ba-145">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="a11ba-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="a11ba-146">Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="a11ba-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="a11ba-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="a11ba-148"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Slouží k přístupu k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="a11ba-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="a11ba-149">`ManifestEmbeddedFileProvider` Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="a11ba-150">Přidejte odkaz na balíček do projektu pro balíček [Microsoft. Extensions. Providers. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) .</span><span class="sxs-lookup"><span data-stu-id="a11ba-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="a11ba-151">Chcete-li vygenerovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na `true`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a11ba-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="a11ba-152">Zadejte soubory, které se mají vložit [ \<do EmbeddedResource >](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="a11ba-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="a11ba-153">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="a11ba-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="a11ba-154">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="a11ba-155">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a11ba-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="a11ba-156">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="a11ba-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="a11ba-157">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-157">Specify a relative file path.</span></span>
* <span data-ttu-id="a11ba-158">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="a11ba-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="a11ba-159">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="a11ba-160">Metody</span><span class="sxs-lookup"><span data-stu-id="a11ba-160">Overload</span></span> | <span data-ttu-id="a11ba-161">Popis</span><span class="sxs-lookup"><span data-stu-id="a11ba-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="a11ba-162">Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="a11ba-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="a11ba-163">Zadejte obor <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> pro volání do těchto prostředků v zadané cestě. `root`</span><span class="sxs-lookup"><span data-stu-id="a11ba-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="a11ba-164">Přijímá volitelný `root` parametr relativní cesty `lastModified` a parametr data (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="a11ba-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="a11ba-165">Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `lastModified`</span><span class="sxs-lookup"><span data-stu-id="a11ba-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="a11ba-166">Přijímá volitelnou `root` relativní cestu, `lastModified` datum a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="a11ba-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="a11ba-167">`manifestName` Představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="a11ba-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="a11ba-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-168">CompositeFileProvider</span></span>

<span data-ttu-id="a11ba-169"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Kombinuje`IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="a11ba-170">Při vytváření `CompositeFileProvider`, předejte do konstruktoru jednu `IFileProvider` nebo více instancí.</span><span class="sxs-lookup"><span data-stu-id="a11ba-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="a11ba-171">V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="a11ba-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="a11ba-172">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="a11ba-172">Watch for changes</span></span>

<span data-ttu-id="a11ba-173">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="a11ba-174">`Watch`přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="a11ba-175">`Watch`<xref:Microsoft.Extensions.Primitives.IChangeToken>vrátí.</span><span class="sxs-lookup"><span data-stu-id="a11ba-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="a11ba-176">Token pro změnu zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="a11ba-176">The change token exposes:</span></span>

* <span data-ttu-id="a11ba-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Vlastnost, která se dá zkontrolovat, aby se zjistilo, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="a11ba-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="a11ba-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Volá se, když se v zadaném řetězci cesty zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="a11ba-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="a11ba-179">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="a11ba-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="a11ba-180">Chcete-li povolit konstantní monitorování, <xref:System.Threading.Tasks.TaskCompletionSource`1> použijte (viz níže) nebo znovu `IChangeToken` vytvořte instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="a11ba-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="a11ba-181">V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:</span><span class="sxs-lookup"><span data-stu-id="a11ba-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="a11ba-182">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="a11ba-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="a11ba-183">Nastavte proměnnou `1` `true` prostředí na nebo k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat). `DOTNET_USE_POLLING_FILE_WATCHER`</span><span class="sxs-lookup"><span data-stu-id="a11ba-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="a11ba-184">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="a11ba-184">Glob patterns</span></span>

<span data-ttu-id="a11ba-185">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* .</span><span class="sxs-lookup"><span data-stu-id="a11ba-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="a11ba-186">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="a11ba-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="a11ba-187">Dva zástupné znaky `*` jsou `**`a:</span><span class="sxs-lookup"><span data-stu-id="a11ba-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="a11ba-188">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="a11ba-189">Shody jsou zakončeny `/` znaky `.` a v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="a11ba-190">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="a11ba-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="a11ba-191">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="a11ba-192">**Příklady vzorů glob**</span><span class="sxs-lookup"><span data-stu-id="a11ba-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="a11ba-193">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="a11ba-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="a11ba-194">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="a11ba-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="a11ba-195">Vyhledá `appsettings.json` všechny soubory v adresářích přesně jednu úroveň pod adresářovou složkou.</span><span class="sxs-lookup"><span data-stu-id="a11ba-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="a11ba-196">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="a11ba-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a11ba-197">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="a11ba-198">Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a11ba-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="a11ba-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zpřístupňuje kořenový adresář obsahu aplikace a webové kořenové adresáře `IFileProvider` jako typy.</span><span class="sxs-lookup"><span data-stu-id="a11ba-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="a11ba-200">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="a11ba-201">[Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="a11ba-202">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="a11ba-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="a11ba-203">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a11ba-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="a11ba-204">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="a11ba-204">File Provider interfaces</span></span>

<span data-ttu-id="a11ba-205">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="a11ba-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="a11ba-206">`IFileProvider`Zpřístupňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="a11ba-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="a11ba-207">Získat informace o souboru<xref:Microsoft.Extensions.FileProviders.IFileInfo>().</span><span class="sxs-lookup"><span data-stu-id="a11ba-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="a11ba-208">Získat informace o adresáři<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>().</span><span class="sxs-lookup"><span data-stu-id="a11ba-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="a11ba-209">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="a11ba-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="a11ba-210">`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="a11ba-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="a11ba-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)</span><span class="sxs-lookup"><span data-stu-id="a11ba-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="a11ba-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum</span><span class="sxs-lookup"><span data-stu-id="a11ba-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="a11ba-213">Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="a11ba-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="a11ba-214">Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a11ba-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="a11ba-215">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="a11ba-215">File Provider implementations</span></span>

<span data-ttu-id="a11ba-216">K dispozici `IFileProvider` jsou tři implementace systému.</span><span class="sxs-lookup"><span data-stu-id="a11ba-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="a11ba-217">Implementace</span><span class="sxs-lookup"><span data-stu-id="a11ba-217">Implementation</span></span> | <span data-ttu-id="a11ba-218">Popis</span><span class="sxs-lookup"><span data-stu-id="a11ba-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="a11ba-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="a11ba-220">Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="a11ba-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="a11ba-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="a11ba-222">Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="a11ba-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="a11ba-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="a11ba-224">Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="a11ba-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-225">PhysicalFileProvider</span></span>

<span data-ttu-id="a11ba-226"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> Poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="a11ba-227">`PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> používá typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="a11ba-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="a11ba-228">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="a11ba-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="a11ba-229">Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro `IFileProvider` vyžádání v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a11ba-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a11ba-230">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="a11ba-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="a11ba-231">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="a11ba-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="a11ba-232">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a11ba-232">Types in the preceding example:</span></span>

* <span data-ttu-id="a11ba-233">`provider``IFileProvider`je.</span><span class="sxs-lookup"><span data-stu-id="a11ba-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="a11ba-234">`contents``IDirectoryContents`je.</span><span class="sxs-lookup"><span data-stu-id="a11ba-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="a11ba-235">`fileInfo``IFileInfo`je.</span><span class="sxs-lookup"><span data-stu-id="a11ba-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="a11ba-236">Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="a11ba-237">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="a11ba-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="a11ba-238">Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="a11ba-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="a11ba-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="a11ba-240"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Slouží k přístupu k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="a11ba-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="a11ba-241">`ManifestEmbeddedFileProvider` Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="a11ba-242">Chcete-li vygenerovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na `true`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a11ba-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="a11ba-243">Zadejte soubory, které se mají [ &lt;vložit&gt;do EmbeddedResource](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="a11ba-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="a11ba-244">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="a11ba-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="a11ba-245">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="a11ba-246">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a11ba-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="a11ba-247">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="a11ba-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="a11ba-248">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-248">Specify a relative file path.</span></span>
* <span data-ttu-id="a11ba-249">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="a11ba-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="a11ba-250">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="a11ba-251">Metody</span><span class="sxs-lookup"><span data-stu-id="a11ba-251">Overload</span></span> | <span data-ttu-id="a11ba-252">Popis</span><span class="sxs-lookup"><span data-stu-id="a11ba-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="a11ba-253">Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="a11ba-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="a11ba-254">Zadejte obor <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> pro volání do těchto prostředků v zadané cestě. `root`</span><span class="sxs-lookup"><span data-stu-id="a11ba-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="a11ba-255">Přijímá volitelný `root` parametr relativní cesty `lastModified` a parametr data (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="a11ba-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="a11ba-256">Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `lastModified`</span><span class="sxs-lookup"><span data-stu-id="a11ba-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="a11ba-257">Přijímá volitelnou `root` relativní cestu, `lastModified` datum a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="a11ba-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="a11ba-258">`manifestName` Představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="a11ba-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="a11ba-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a11ba-259">CompositeFileProvider</span></span>

<span data-ttu-id="a11ba-260"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Kombinuje`IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="a11ba-261">Při vytváření `CompositeFileProvider`, předejte do konstruktoru jednu `IFileProvider` nebo více instancí.</span><span class="sxs-lookup"><span data-stu-id="a11ba-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="a11ba-262">V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="a11ba-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="a11ba-263">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="a11ba-263">Watch for changes</span></span>

<span data-ttu-id="a11ba-264">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="a11ba-265">`Watch`přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="a11ba-266">`Watch`<xref:Microsoft.Extensions.Primitives.IChangeToken>vrátí.</span><span class="sxs-lookup"><span data-stu-id="a11ba-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="a11ba-267">Token pro změnu zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="a11ba-267">The change token exposes:</span></span>

* <span data-ttu-id="a11ba-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Vlastnost, která se dá zkontrolovat, aby se zjistilo, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="a11ba-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="a11ba-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Volá se, když se v zadaném řetězci cesty zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="a11ba-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="a11ba-270">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="a11ba-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="a11ba-271">Chcete-li povolit konstantní monitorování, <xref:System.Threading.Tasks.TaskCompletionSource`1> použijte (viz níže) nebo znovu `IChangeToken` vytvořte instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="a11ba-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="a11ba-272">V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:</span><span class="sxs-lookup"><span data-stu-id="a11ba-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="a11ba-273">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="a11ba-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="a11ba-274">Nastavte proměnnou `1` `true` prostředí na nebo k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat). `DOTNET_USE_POLLING_FILE_WATCHER`</span><span class="sxs-lookup"><span data-stu-id="a11ba-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="a11ba-275">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="a11ba-275">Glob patterns</span></span>

<span data-ttu-id="a11ba-276">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* .</span><span class="sxs-lookup"><span data-stu-id="a11ba-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="a11ba-277">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="a11ba-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="a11ba-278">Dva zástupné znaky `*` jsou `**`a:</span><span class="sxs-lookup"><span data-stu-id="a11ba-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="a11ba-279">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="a11ba-280">Shody jsou zakončeny `/` znaky `.` a v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="a11ba-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="a11ba-281">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="a11ba-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="a11ba-282">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="a11ba-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="a11ba-283">**Příklady vzorů glob**</span><span class="sxs-lookup"><span data-stu-id="a11ba-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="a11ba-284">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="a11ba-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="a11ba-285">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="a11ba-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="a11ba-286">Vyhledá `appsettings.json` všechny soubory v adresářích přesně jednu úroveň pod adresářovou složkou.</span><span class="sxs-lookup"><span data-stu-id="a11ba-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="a11ba-287">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="a11ba-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
