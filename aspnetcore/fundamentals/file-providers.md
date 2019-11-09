---
title: File Providers in ASP.NET Core
author: guardrex
description: Learn how ASP.NET Core abstracts file system access through the use of File Providers.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 531f7acd7a704a74e6142d201f613f05288deecb
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896845"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="540d3-103">File Providers in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="540d3-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="540d3-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="540d3-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="540d3-105">ASP.NET Core abstracts file system access through the use of File Providers.</span><span class="sxs-lookup"><span data-stu-id="540d3-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="540d3-106">File Providers are used throughout the ASP.NET Core framework:</span><span class="sxs-lookup"><span data-stu-id="540d3-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="540d3-107">`IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span><span class="sxs-lookup"><span data-stu-id="540d3-107">`IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="540d3-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span><span class="sxs-lookup"><span data-stu-id="540d3-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="540d3-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span><span class="sxs-lookup"><span data-stu-id="540d3-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="540d3-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span><span class="sxs-lookup"><span data-stu-id="540d3-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="540d3-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="540d3-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="540d3-112">File Provider interfaces</span><span class="sxs-lookup"><span data-stu-id="540d3-112">File Provider interfaces</span></span>

<span data-ttu-id="540d3-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="540d3-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="540d3-114">`IFileProvider` exposes methods to:</span><span class="sxs-lookup"><span data-stu-id="540d3-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="540d3-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="540d3-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="540d3-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="540d3-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="540d3-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="540d3-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="540d3-118">`IFileInfo` provides methods and properties for working with files:</span><span class="sxs-lookup"><span data-stu-id="540d3-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="540d3-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span><span class="sxs-lookup"><span data-stu-id="540d3-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="540d3-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span><span class="sxs-lookup"><span data-stu-id="540d3-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="540d3-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span><span class="sxs-lookup"><span data-stu-id="540d3-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="540d3-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="540d3-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="540d3-123">File Provider implementations</span><span class="sxs-lookup"><span data-stu-id="540d3-123">File Provider implementations</span></span>

<span data-ttu-id="540d3-124">Three implementations of `IFileProvider` are available.</span><span class="sxs-lookup"><span data-stu-id="540d3-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="540d3-125">Implementace</span><span class="sxs-lookup"><span data-stu-id="540d3-125">Implementation</span></span> | <span data-ttu-id="540d3-126">Popis</span><span class="sxs-lookup"><span data-stu-id="540d3-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="540d3-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="540d3-128">The physical provider is used to access the system's physical files.</span><span class="sxs-lookup"><span data-stu-id="540d3-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="540d3-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="540d3-130">The manifest embedded provider is used to access files embedded in assemblies.</span><span class="sxs-lookup"><span data-stu-id="540d3-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="540d3-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="540d3-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span><span class="sxs-lookup"><span data-stu-id="540d3-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="540d3-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-133">PhysicalFileProvider</span></span>

<span data-ttu-id="540d3-134"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="540d3-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="540d3-135">`PhysicalFileProvider` používá typ <xref:System.IO.File?displayProperty=fullName> (pro fyzického poskytovatele) a rozsahy všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="540d3-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="540d3-136">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="540d3-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="540d3-137">Nejběžnější scénář pro vytváření a používání `PhysicalFileProvider` je vyžádání `IFileProvider` v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="540d3-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="540d3-138">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="540d3-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="540d3-139">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="540d3-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="540d3-140">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="540d3-140">Types in the preceding example:</span></span>

* <span data-ttu-id="540d3-141">`provider` je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="540d3-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="540d3-142">`contents` je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="540d3-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="540d3-143">`fileInfo` je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="540d3-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="540d3-144">Zprostředkovatele souboru lze použít k iterování adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="540d3-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="540d3-145">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="540d3-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="540d3-146">Ukázková aplikace vytvoří zprostředkovatele v `Startup.ConfigureServices` třídy aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="540d3-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="540d3-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="540d3-148"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se používá pro přístup k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="540d3-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="540d3-149">`ManifestEmbeddedFileProvider` používá manifest kompilovaný do sestavení pro rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="540d3-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="540d3-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span><span class="sxs-lookup"><span data-stu-id="540d3-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="540d3-151">Chcete-li vygenerovat manifest vložených souborů, nastavte vlastnost `<GenerateEmbeddedFilesManifest>` na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="540d3-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="540d3-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="540d3-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="540d3-153">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="540d3-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="540d3-154">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="540d3-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="540d3-155">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="540d3-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="540d3-156">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="540d3-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="540d3-157">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="540d3-157">Specify a relative file path.</span></span>
* <span data-ttu-id="540d3-158">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="540d3-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="540d3-159">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="540d3-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="540d3-160">Metody</span><span class="sxs-lookup"><span data-stu-id="540d3-160">Overload</span></span> | <span data-ttu-id="540d3-161">Popis</span><span class="sxs-lookup"><span data-stu-id="540d3-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="540d3-162">Přijímá volitelný parametr relativní cesty `root`.</span><span class="sxs-lookup"><span data-stu-id="540d3-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="540d3-163">Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="540d3-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="540d3-164">Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="540d3-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="540d3-165">Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="540d3-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="540d3-166">Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="540d3-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="540d3-167">`manifestName` představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="540d3-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="540d3-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-168">CompositeFileProvider</span></span>

<span data-ttu-id="540d3-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span><span class="sxs-lookup"><span data-stu-id="540d3-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="540d3-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span><span class="sxs-lookup"><span data-stu-id="540d3-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="540d3-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span><span class="sxs-lookup"><span data-stu-id="540d3-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="540d3-172">Watch for changes</span><span class="sxs-lookup"><span data-stu-id="540d3-172">Watch for changes</span></span>

<span data-ttu-id="540d3-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span><span class="sxs-lookup"><span data-stu-id="540d3-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="540d3-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span><span class="sxs-lookup"><span data-stu-id="540d3-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="540d3-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="540d3-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="540d3-176">The change token exposes:</span><span class="sxs-lookup"><span data-stu-id="540d3-176">The change token exposes:</span></span>

* <span data-ttu-id="540d3-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span><span class="sxs-lookup"><span data-stu-id="540d3-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="540d3-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span><span class="sxs-lookup"><span data-stu-id="540d3-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="540d3-179">Each change token only calls its associated callback in response to a single change.</span><span class="sxs-lookup"><span data-stu-id="540d3-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="540d3-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span><span class="sxs-lookup"><span data-stu-id="540d3-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="540d3-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span><span class="sxs-lookup"><span data-stu-id="540d3-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="540d3-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span><span class="sxs-lookup"><span data-stu-id="540d3-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="540d3-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span><span class="sxs-lookup"><span data-stu-id="540d3-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="540d3-184">Glob patterns</span><span class="sxs-lookup"><span data-stu-id="540d3-184">Glob patterns</span></span>

<span data-ttu-id="540d3-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span><span class="sxs-lookup"><span data-stu-id="540d3-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="540d3-186">Specify groups of files with these patterns.</span><span class="sxs-lookup"><span data-stu-id="540d3-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="540d3-187">The two wildcard characters are `*` and `**`:</span><span class="sxs-lookup"><span data-stu-id="540d3-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="540d3-188">Matches anything at the current folder level, any filename, or any file extension.</span><span class="sxs-lookup"><span data-stu-id="540d3-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="540d3-189">Matches are terminated by `/` and `.` characters in the file path.</span><span class="sxs-lookup"><span data-stu-id="540d3-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="540d3-190">Matches anything across multiple directory levels.</span><span class="sxs-lookup"><span data-stu-id="540d3-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="540d3-191">Can be used to recursively match many files within a directory hierarchy.</span><span class="sxs-lookup"><span data-stu-id="540d3-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="540d3-192">**Glob pattern examples**</span><span class="sxs-lookup"><span data-stu-id="540d3-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="540d3-193">Matches a specific file in a specific directory.</span><span class="sxs-lookup"><span data-stu-id="540d3-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="540d3-194">Matches all files with *.txt* extension in a specific directory.</span><span class="sxs-lookup"><span data-stu-id="540d3-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="540d3-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span><span class="sxs-lookup"><span data-stu-id="540d3-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="540d3-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span><span class="sxs-lookup"><span data-stu-id="540d3-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="540d3-197">ASP.NET Core abstracts file system access through the use of File Providers.</span><span class="sxs-lookup"><span data-stu-id="540d3-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="540d3-198">File Providers are used throughout the ASP.NET Core framework:</span><span class="sxs-lookup"><span data-stu-id="540d3-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="540d3-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span><span class="sxs-lookup"><span data-stu-id="540d3-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="540d3-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span><span class="sxs-lookup"><span data-stu-id="540d3-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="540d3-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span><span class="sxs-lookup"><span data-stu-id="540d3-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="540d3-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span><span class="sxs-lookup"><span data-stu-id="540d3-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="540d3-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="540d3-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="540d3-204">File Provider interfaces</span><span class="sxs-lookup"><span data-stu-id="540d3-204">File Provider interfaces</span></span>

<span data-ttu-id="540d3-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="540d3-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="540d3-206">`IFileProvider` exposes methods to:</span><span class="sxs-lookup"><span data-stu-id="540d3-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="540d3-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="540d3-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="540d3-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="540d3-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="540d3-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="540d3-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="540d3-210">`IFileInfo` provides methods and properties for working with files:</span><span class="sxs-lookup"><span data-stu-id="540d3-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="540d3-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span><span class="sxs-lookup"><span data-stu-id="540d3-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="540d3-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span><span class="sxs-lookup"><span data-stu-id="540d3-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="540d3-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span><span class="sxs-lookup"><span data-stu-id="540d3-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="540d3-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="540d3-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="540d3-215">File Provider implementations</span><span class="sxs-lookup"><span data-stu-id="540d3-215">File Provider implementations</span></span>

<span data-ttu-id="540d3-216">Three implementations of `IFileProvider` are available.</span><span class="sxs-lookup"><span data-stu-id="540d3-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="540d3-217">Implementace</span><span class="sxs-lookup"><span data-stu-id="540d3-217">Implementation</span></span> | <span data-ttu-id="540d3-218">Popis</span><span class="sxs-lookup"><span data-stu-id="540d3-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="540d3-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="540d3-220">The physical provider is used to access the system's physical files.</span><span class="sxs-lookup"><span data-stu-id="540d3-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="540d3-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="540d3-222">The manifest embedded provider is used to access files embedded in assemblies.</span><span class="sxs-lookup"><span data-stu-id="540d3-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="540d3-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="540d3-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span><span class="sxs-lookup"><span data-stu-id="540d3-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="540d3-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-225">PhysicalFileProvider</span></span>

<span data-ttu-id="540d3-226"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="540d3-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="540d3-227">`PhysicalFileProvider` používá typ <xref:System.IO.File?displayProperty=fullName> (pro fyzického poskytovatele) a rozsahy všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="540d3-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="540d3-228">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="540d3-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="540d3-229">Nejběžnější scénář pro vytváření a používání `PhysicalFileProvider` je vyžádání `IFileProvider` v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="540d3-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="540d3-230">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="540d3-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="540d3-231">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="540d3-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="540d3-232">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="540d3-232">Types in the preceding example:</span></span>

* <span data-ttu-id="540d3-233">`provider` je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="540d3-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="540d3-234">`contents` je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="540d3-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="540d3-235">`fileInfo` je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="540d3-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="540d3-236">Zprostředkovatele souboru lze použít k iterování adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="540d3-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="540d3-237">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="540d3-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="540d3-238">Ukázková aplikace vytvoří zprostředkovatele v `Startup.ConfigureServices` třídy aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="540d3-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="540d3-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="540d3-240"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se používá pro přístup k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="540d3-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="540d3-241">`ManifestEmbeddedFileProvider` používá manifest kompilovaný do sestavení pro rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="540d3-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="540d3-242">Chcete-li vygenerovat manifest vložených souborů, nastavte vlastnost `<GenerateEmbeddedFilesManifest>` na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="540d3-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="540d3-243">Zadejte soubory pro vložení [&gt;&lt;EmbeddedResource ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="540d3-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="540d3-244">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="540d3-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="540d3-245">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="540d3-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="540d3-246">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="540d3-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="540d3-247">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="540d3-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="540d3-248">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="540d3-248">Specify a relative file path.</span></span>
* <span data-ttu-id="540d3-249">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="540d3-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="540d3-250">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="540d3-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="540d3-251">Metody</span><span class="sxs-lookup"><span data-stu-id="540d3-251">Overload</span></span> | <span data-ttu-id="540d3-252">Popis</span><span class="sxs-lookup"><span data-stu-id="540d3-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="540d3-253">Přijímá volitelný parametr relativní cesty `root`.</span><span class="sxs-lookup"><span data-stu-id="540d3-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="540d3-254">Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="540d3-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="540d3-255">Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="540d3-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="540d3-256">Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="540d3-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="540d3-257">Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="540d3-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="540d3-258">`manifestName` představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="540d3-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="540d3-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="540d3-259">CompositeFileProvider</span></span>

<span data-ttu-id="540d3-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span><span class="sxs-lookup"><span data-stu-id="540d3-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="540d3-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span><span class="sxs-lookup"><span data-stu-id="540d3-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="540d3-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span><span class="sxs-lookup"><span data-stu-id="540d3-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="540d3-263">Watch for changes</span><span class="sxs-lookup"><span data-stu-id="540d3-263">Watch for changes</span></span>

<span data-ttu-id="540d3-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span><span class="sxs-lookup"><span data-stu-id="540d3-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="540d3-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span><span class="sxs-lookup"><span data-stu-id="540d3-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="540d3-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="540d3-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="540d3-267">The change token exposes:</span><span class="sxs-lookup"><span data-stu-id="540d3-267">The change token exposes:</span></span>

* <span data-ttu-id="540d3-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span><span class="sxs-lookup"><span data-stu-id="540d3-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="540d3-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span><span class="sxs-lookup"><span data-stu-id="540d3-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="540d3-270">Each change token only calls its associated callback in response to a single change.</span><span class="sxs-lookup"><span data-stu-id="540d3-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="540d3-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span><span class="sxs-lookup"><span data-stu-id="540d3-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="540d3-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span><span class="sxs-lookup"><span data-stu-id="540d3-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="540d3-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span><span class="sxs-lookup"><span data-stu-id="540d3-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="540d3-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span><span class="sxs-lookup"><span data-stu-id="540d3-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="540d3-275">Glob patterns</span><span class="sxs-lookup"><span data-stu-id="540d3-275">Glob patterns</span></span>

<span data-ttu-id="540d3-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span><span class="sxs-lookup"><span data-stu-id="540d3-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="540d3-277">Specify groups of files with these patterns.</span><span class="sxs-lookup"><span data-stu-id="540d3-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="540d3-278">The two wildcard characters are `*` and `**`:</span><span class="sxs-lookup"><span data-stu-id="540d3-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="540d3-279">Matches anything at the current folder level, any filename, or any file extension.</span><span class="sxs-lookup"><span data-stu-id="540d3-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="540d3-280">Matches are terminated by `/` and `.` characters in the file path.</span><span class="sxs-lookup"><span data-stu-id="540d3-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="540d3-281">Matches anything across multiple directory levels.</span><span class="sxs-lookup"><span data-stu-id="540d3-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="540d3-282">Can be used to recursively match many files within a directory hierarchy.</span><span class="sxs-lookup"><span data-stu-id="540d3-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="540d3-283">**Glob pattern examples**</span><span class="sxs-lookup"><span data-stu-id="540d3-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="540d3-284">Matches a specific file in a specific directory.</span><span class="sxs-lookup"><span data-stu-id="540d3-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="540d3-285">Matches all files with *.txt* extension in a specific directory.</span><span class="sxs-lookup"><span data-stu-id="540d3-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="540d3-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span><span class="sxs-lookup"><span data-stu-id="540d3-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="540d3-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span><span class="sxs-lookup"><span data-stu-id="540d3-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
