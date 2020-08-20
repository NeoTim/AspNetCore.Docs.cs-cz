---
title: Poskytovatelé souborů v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 30c28e7bd4cd9c926b157f5a7b9e6688bd5b9b9a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634602"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="38c26-103">Poskytovatelé souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38c26-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="38c26-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="38c26-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38c26-105">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="38c26-106">Poskytovatelé souborů se používají v rámci ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="38c26-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="38c26-107">Příklad:</span><span class="sxs-lookup"><span data-stu-id="38c26-107">For example:</span></span>

* <span data-ttu-id="38c26-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> zpřístupňuje [kořenový adresář obsahu](xref:fundamentals/index#content-root) aplikace a [webové kořenové adresáře](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="38c26-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="38c26-109">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="38c26-110">[Razor](xref:mvc/views/razor) vyhledává stránky a zobrazení pomocí zprostředkovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="38c26-111">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="38c26-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="38c26-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38c26-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="38c26-113">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="38c26-113">File Provider interfaces</span></span>

<span data-ttu-id="38c26-114">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="38c26-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="38c26-115">`IFileProvider` Zpřístupňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="38c26-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="38c26-116">Získat informace o souboru ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="38c26-117">Získat informace o adresáři ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="38c26-118">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="38c26-119">`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="38c26-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="38c26-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (v bajtech)</span><span class="sxs-lookup"><span data-stu-id="38c26-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="38c26-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> Datum</span><span class="sxs-lookup"><span data-stu-id="38c26-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="38c26-122">Můžete číst ze souboru pomocí <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> metody.</span><span class="sxs-lookup"><span data-stu-id="38c26-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="38c26-123">Ukázková aplikace *FileProviderSample* ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="38c26-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="38c26-124">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="38c26-124">File Provider implementations</span></span>

<span data-ttu-id="38c26-125">V následující tabulce jsou uvedeny implementace `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="38c26-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="38c26-126">Implementace</span><span class="sxs-lookup"><span data-stu-id="38c26-126">Implementation</span></span> | <span data-ttu-id="38c26-127">Popis</span><span class="sxs-lookup"><span data-stu-id="38c26-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="38c26-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="38c26-129">Slouží k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="38c26-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="38c26-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="38c26-131">Používá se pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="38c26-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="38c26-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="38c26-133">Používá se pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="38c26-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="38c26-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-134">PhysicalFileProvider</span></span>

<span data-ttu-id="38c26-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="38c26-136">`PhysicalFileProvider` používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="38c26-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="38c26-137">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="38c26-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="38c26-138">Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro vyžádání `IFileProvider` v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="38c26-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="38c26-139">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje absolutní cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="38c26-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="38c26-140">Glob vzory se v cestě k adresáři nepodporují.</span><span class="sxs-lookup"><span data-stu-id="38c26-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="38c26-141">Následující kód ukazuje, jak použít `PhysicalFileProvider` k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="38c26-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="38c26-142">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="38c26-142">Types in the preceding example:</span></span>

* <span data-ttu-id="38c26-143">`provider` je `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="38c26-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="38c26-144">`contents` je `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="38c26-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="38c26-145">`fileInfo` je `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="38c26-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="38c26-146">Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="38c26-147">Do metody nelze předat vzory glob `GetFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="38c26-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="38c26-148">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="38c26-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="38c26-149">Ukázková aplikace *FileProviderSample* vytvoří zprostředkovatele v `Startup.ConfigureServices` metodě pomocí <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="38c26-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="38c26-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="38c26-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>Slouží k přístupu k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="38c26-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="38c26-152">`ManifestEmbeddedFileProvider`Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="38c26-153">Chcete-li vygenerovat manifest vložených souborů:</span><span class="sxs-lookup"><span data-stu-id="38c26-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="38c26-154">Přidejte do projektu balíček [Microsoft. Extensions. Providers. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet.</span><span class="sxs-lookup"><span data-stu-id="38c26-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="38c26-155">Nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="38c26-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="38c26-156">Zadejte soubory, které se mají vložit [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="38c26-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="38c26-157">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="38c26-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="38c26-158">Ukázková aplikace *FileProviderSample* vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="38c26-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="38c26-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38c26-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="38c26-160">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="38c26-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="38c26-161">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-161">Specify a relative file path.</span></span>
* <span data-ttu-id="38c26-162">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="38c26-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="38c26-163">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="38c26-164">Metody</span><span class="sxs-lookup"><span data-stu-id="38c26-164">Overload</span></span> | <span data-ttu-id="38c26-165">Popis</span><span class="sxs-lookup"><span data-stu-id="38c26-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="38c26-166">Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="38c26-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="38c26-167">Zadejte `root` obor pro volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> těchto prostředků v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="38c26-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="38c26-168">Přijímá volitelný `root` parametr relativní cesty a `lastModified` parametr data ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="38c26-169">`lastModified`Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="38c26-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="38c26-170">Přijímá volitelnou `root` relativní cestu, `lastModified` Datum a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="38c26-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="38c26-171">`manifestName`Představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="38c26-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="38c26-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-172">CompositeFileProvider</span></span>

<span data-ttu-id="38c26-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Kombinuje `IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="38c26-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="38c26-174">Při vytváření `CompositeFileProvider` , předejte do konstruktoru jednu nebo více `IFileProvider` instancí.</span><span class="sxs-lookup"><span data-stu-id="38c26-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="38c26-175">V ukázkové aplikaci *FileProviderSample* `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytuje soubory `CompositeFileProvider` registrované v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="38c26-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="38c26-176">Následující kód naleznete v `Startup.ConfigureServices` metodě projektu:</span><span class="sxs-lookup"><span data-stu-id="38c26-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="38c26-177">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="38c26-177">Watch for changes</span></span>

<span data-ttu-id="38c26-178"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>Metoda poskytuje scénář pro sledování změn jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="38c26-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="38c26-179">`Watch`Metoda:</span><span class="sxs-lookup"><span data-stu-id="38c26-179">The `Watch` method:</span></span>

* <span data-ttu-id="38c26-180">Přijímá řetězec cesty k souboru, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="38c26-181">Vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="38c26-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="38c26-182">Výsledný token změny zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="38c26-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="38c26-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="38c26-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="38c26-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Volá se, když se v zadaném řetězci cesty zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="38c26-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="38c26-185">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="38c26-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="38c26-186">Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (viz níže) nebo znovu vytvořte `IChangeToken` instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="38c26-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="38c26-187">Ukázková aplikace *WatchConsole* zapisuje zprávu vždy, když se upraví soubor *. txt* v adresáři *TextFiles* :</span><span class="sxs-lookup"><span data-stu-id="38c26-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="38c26-188">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="38c26-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="38c26-189">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou prostředí na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="38c26-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="38c26-190">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="38c26-190">Glob patterns</span></span>

<span data-ttu-id="38c26-191">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)*.</span><span class="sxs-lookup"><span data-stu-id="38c26-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="38c26-192">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="38c26-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="38c26-193">Dva zástupné znaky jsou `*` a `**` :</span><span class="sxs-lookup"><span data-stu-id="38c26-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="38c26-194">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="38c26-195">Shody jsou zakončeny `/` `.` znaky a v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="38c26-196">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="38c26-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="38c26-197">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="38c26-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="38c26-198">Následující tabulka uvádí běžné příklady glob vzorů.</span><span class="sxs-lookup"><span data-stu-id="38c26-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="38c26-199">Vzor</span><span class="sxs-lookup"><span data-stu-id="38c26-199">Pattern</span></span>  |<span data-ttu-id="38c26-200">Popis</span><span class="sxs-lookup"><span data-stu-id="38c26-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="38c26-201">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="38c26-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="38c26-202">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="38c26-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="38c26-203">Vyhledá všechny *appsettings.js* souborů v adresářích přesně jednu úroveň pod *adresářovou* složkou.</span><span class="sxs-lookup"><span data-stu-id="38c26-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="38c26-204">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="38c26-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38c26-205">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="38c26-206">Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:</span><span class="sxs-lookup"><span data-stu-id="38c26-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="38c26-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> zpřístupňuje [kořenový adresář obsahu](xref:fundamentals/index#content-root) aplikace a [webové kořenové adresáře](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="38c26-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="38c26-208">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="38c26-209">[Razor](xref:mvc/views/razor) vyhledává stránky a zobrazení pomocí zprostředkovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="38c26-210">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="38c26-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="38c26-211">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38c26-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="38c26-212">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="38c26-212">File Provider interfaces</span></span>

<span data-ttu-id="38c26-213">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="38c26-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="38c26-214">`IFileProvider` Zpřístupňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="38c26-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="38c26-215">Získat informace o souboru ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="38c26-216">Získat informace o adresáři ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="38c26-217">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="38c26-218">`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="38c26-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="38c26-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (v bajtech)</span><span class="sxs-lookup"><span data-stu-id="38c26-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="38c26-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> Datum</span><span class="sxs-lookup"><span data-stu-id="38c26-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="38c26-221">Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="38c26-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="38c26-222">Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="38c26-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="38c26-223">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="38c26-223">File Provider implementations</span></span>

<span data-ttu-id="38c26-224">`IFileProvider`K dispozici jsou tři implementace systému.</span><span class="sxs-lookup"><span data-stu-id="38c26-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="38c26-225">Implementace</span><span class="sxs-lookup"><span data-stu-id="38c26-225">Implementation</span></span> | <span data-ttu-id="38c26-226">Popis</span><span class="sxs-lookup"><span data-stu-id="38c26-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="38c26-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="38c26-228">Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="38c26-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="38c26-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="38c26-230">Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="38c26-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="38c26-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="38c26-232">Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="38c26-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="38c26-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-233">PhysicalFileProvider</span></span>

<span data-ttu-id="38c26-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="38c26-235">`PhysicalFileProvider` používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="38c26-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="38c26-236">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="38c26-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="38c26-237">Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro vyžádání `IFileProvider` v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="38c26-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="38c26-238">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="38c26-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="38c26-239">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="38c26-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="38c26-240">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="38c26-240">Types in the preceding example:</span></span>

* <span data-ttu-id="38c26-241">`provider` je `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="38c26-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="38c26-242">`contents` je `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="38c26-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="38c26-243">`fileInfo` je `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="38c26-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="38c26-244">Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="38c26-245">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="38c26-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="38c26-246">Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="38c26-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="38c26-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="38c26-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>Slouží k přístupu k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="38c26-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="38c26-249">`ManifestEmbeddedFileProvider`Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="38c26-250">Chcete-li vygenerovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="38c26-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="38c26-251">Zadejte soubory, které se mají vložit do [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="38c26-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="38c26-252">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="38c26-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="38c26-253">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="38c26-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="38c26-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38c26-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="38c26-255">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="38c26-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="38c26-256">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-256">Specify a relative file path.</span></span>
* <span data-ttu-id="38c26-257">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="38c26-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="38c26-258">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="38c26-259">Metody</span><span class="sxs-lookup"><span data-stu-id="38c26-259">Overload</span></span> | <span data-ttu-id="38c26-260">Popis</span><span class="sxs-lookup"><span data-stu-id="38c26-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="38c26-261">Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="38c26-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="38c26-262">Zadejte `root` obor pro volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> těchto prostředků v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="38c26-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="38c26-263">Přijímá volitelný `root` parametr relativní cesty a `lastModified` parametr data ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="38c26-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="38c26-264">`lastModified`Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="38c26-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="38c26-265">Přijímá volitelnou `root` relativní cestu, `lastModified` Datum a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="38c26-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="38c26-266">`manifestName`Představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="38c26-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="38c26-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="38c26-267">CompositeFileProvider</span></span>

<span data-ttu-id="38c26-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Kombinuje `IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="38c26-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="38c26-269">Při vytváření `CompositeFileProvider` , předejte do konstruktoru jednu nebo více `IFileProvider` instancí.</span><span class="sxs-lookup"><span data-stu-id="38c26-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="38c26-270">V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="38c26-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="38c26-271">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="38c26-271">Watch for changes</span></span>

<span data-ttu-id="38c26-272">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="38c26-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="38c26-273">`Watch` přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="38c26-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="38c26-274">`Watch` Vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="38c26-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="38c26-275">Token pro změnu zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="38c26-275">The change token exposes:</span></span>

* <span data-ttu-id="38c26-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="38c26-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="38c26-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Volá se, když se v zadaném řetězci cesty zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="38c26-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="38c26-278">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="38c26-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="38c26-279">Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (viz níže) nebo znovu vytvořte `IChangeToken` instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="38c26-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="38c26-280">V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:</span><span class="sxs-lookup"><span data-stu-id="38c26-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="38c26-281">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="38c26-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="38c26-282">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou prostředí na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="38c26-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="38c26-283">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="38c26-283">Glob patterns</span></span>

<span data-ttu-id="38c26-284">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)*.</span><span class="sxs-lookup"><span data-stu-id="38c26-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="38c26-285">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="38c26-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="38c26-286">Dva zástupné znaky jsou `*` a `**` :</span><span class="sxs-lookup"><span data-stu-id="38c26-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="38c26-287">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="38c26-288">Shody jsou zakončeny `/` `.` znaky a v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="38c26-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="38c26-289">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="38c26-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="38c26-290">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="38c26-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="38c26-291">**Příklady vzorů glob**</span><span class="sxs-lookup"><span data-stu-id="38c26-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="38c26-292">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="38c26-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="38c26-293">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="38c26-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="38c26-294">Vyhledá všechny `appsettings.json` soubory v adresářích přesně jednu úroveň pod *adresářovou* složkou.</span><span class="sxs-lookup"><span data-stu-id="38c26-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="38c26-295">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="38c26-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
