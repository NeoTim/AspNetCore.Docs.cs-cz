---
title: Zprostředkovatelé souborů v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak ASP.NET core abstraktů přístup k systému souborů pomocí zprostředkovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751100"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="82753-103">Zprostředkovatelé souborů v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="82753-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="82753-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="82753-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82753-105">ASP.NET Core abstraktů přístup k systému souborů pomocí zprostředkovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="82753-106">Zprostředkovatelé souborů se používají v celém ASP.NET core framework.</span><span class="sxs-lookup"><span data-stu-id="82753-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="82753-107">Příklad:</span><span class="sxs-lookup"><span data-stu-id="82753-107">For example:</span></span>

* <span data-ttu-id="82753-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>zveřejňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a kořen `IFileProvider` [webu](xref:fundamentals/index#web-root) jako typy.</span><span class="sxs-lookup"><span data-stu-id="82753-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="82753-109">[Middleware statického souboru](xref:fundamentals/static-files) používá k vyhledání statických souborů zprostředkovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="82753-110">[Razor](xref:mvc/views/razor) používá zprostředkovatele souborů k vyhledání stránek a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="82753-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="82753-111">Nástroje .NET Core používají zprostředkovatele souborů a glob vzory určit, které soubory mají být publikovány.</span><span class="sxs-lookup"><span data-stu-id="82753-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="82753-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="82753-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="82753-113">Rozhraní zprostředkovatele souborů</span><span class="sxs-lookup"><span data-stu-id="82753-113">File Provider interfaces</span></span>

<span data-ttu-id="82753-114">Primární rozhraní <xref:Microsoft.Extensions.FileProviders.IFileProvider>je .</span><span class="sxs-lookup"><span data-stu-id="82753-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="82753-115">`IFileProvider`vystavuje metody:</span><span class="sxs-lookup"><span data-stu-id="82753-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="82753-116">Získat informace<xref:Microsoft.Extensions.FileProviders.IFileInfo>o souboru ( ).</span><span class="sxs-lookup"><span data-stu-id="82753-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="82753-117">Získat informace<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o adresáři ( ).</span><span class="sxs-lookup"><span data-stu-id="82753-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="82753-118">Nastavte oznámení o změnách <xref:Microsoft.Extensions.Primitives.IChangeToken>(pomocí ).</span><span class="sxs-lookup"><span data-stu-id="82753-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="82753-119">`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="82753-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="82753-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)</span><span class="sxs-lookup"><span data-stu-id="82753-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="82753-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum</span><span class="sxs-lookup"><span data-stu-id="82753-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="82753-122">Můžete číst ze souboru <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="82753-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="82753-123">Ukázková aplikace *FileProviderSample* ukazuje, jak nakonfigurovat zprostředkovatele souborů v aplikaci pro použití v `Startup.ConfigureServices` celé aplikaci prostřednictvím vkládání [závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="82753-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="82753-124">Implementace zprostředkovatele souborů</span><span class="sxs-lookup"><span data-stu-id="82753-124">File Provider implementations</span></span>

<span data-ttu-id="82753-125">V následující tabulce jsou `IFileProvider`uvedeny implementace .</span><span class="sxs-lookup"><span data-stu-id="82753-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="82753-126">Implementace</span><span class="sxs-lookup"><span data-stu-id="82753-126">Implementation</span></span> | <span data-ttu-id="82753-127">Popis</span><span class="sxs-lookup"><span data-stu-id="82753-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="82753-128">Složený souborZprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="82753-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="82753-129">Slouží k poskytnutí kombinovaného přístupu k souborům a adresářům od jednoho nebo více jiných poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="82753-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="82753-130">Objekt ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="82753-131">Slouží k přístupu k souborům vloženým do sestavení.</span><span class="sxs-lookup"><span data-stu-id="82753-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="82753-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="82753-133">Slouží k přístupu k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="82753-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="82753-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-134">PhysicalFileProvider</span></span>

<span data-ttu-id="82753-135">Poskytuje <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="82753-136">`PhysicalFileProvider`používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického zprostředkovatele) a obory všechny cesty do adresáře a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="82753-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="82753-137">Toto obory brání přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="82753-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="82753-138">Nejběžnější scénář pro vytváření a `PhysicalFileProvider` používání je `IFileProvider` požádat v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="82753-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="82753-139">Při vytváření instancí tohoto zprostředkovatele přímo je vyžadována absolutní cesta k adresáři, která slouží jako základní cesta pro všechny požadavky provedené pomocí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="82753-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="82753-140">Glob vzory nejsou podporovány v cestě adresáře.</span><span class="sxs-lookup"><span data-stu-id="82753-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="82753-141">Následující kód ukazuje, `PhysicalFileProvider` jak získat obsah adresáře a informace o souborech:</span><span class="sxs-lookup"><span data-stu-id="82753-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="82753-142">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="82753-142">Types in the preceding example:</span></span>

* <span data-ttu-id="82753-143">`provider`je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="82753-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="82753-144">`contents`je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="82753-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="82753-145">`fileInfo`je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="82753-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="82753-146">Zprostředkovatel souborů lze itetovat prostřednictvím adresáře určeného `applicationRoot` nebo volání `GetFileInfo` získat informace o souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="82753-147">Glob vzory nemohou být předány `GetFileInfo` k metodě.</span><span class="sxs-lookup"><span data-stu-id="82753-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="82753-148">Zprostředkovatel souborů nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="82753-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="82753-149">Ukázková aplikace *FileProviderSample* vytvoří `Startup.ConfigureServices` zprostředkovatele <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>v metodě pomocí :</span><span class="sxs-lookup"><span data-stu-id="82753-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="82753-150">Objekt ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="82753-151">Slouží <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> k přístupu k souborům vloženým do sestavení.</span><span class="sxs-lookup"><span data-stu-id="82753-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="82753-152">Používá `ManifestEmbeddedFileProvider` manifest zkompilovaný do sestavení k rekonstrukci původní cesty vložené soubory.</span><span class="sxs-lookup"><span data-stu-id="82753-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="82753-153">Chcete-li generovat manifest vložených souborů:</span><span class="sxs-lookup"><span data-stu-id="82753-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="82753-154">Přidejte balíček [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="82753-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="82753-155">Nastavte `<GenerateEmbeddedFilesManifest>` vlastnost `true`na .</span><span class="sxs-lookup"><span data-stu-id="82753-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="82753-156">Určete soubory, které chcete vložit [ \<s>EmbeddedResource ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="82753-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="82753-157">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které chcete vložit do sestavy.</span><span class="sxs-lookup"><span data-stu-id="82753-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="82753-158">Ukázková aplikace *FileProviderSample* vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="82753-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="82753-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="82753-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="82753-160">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="82753-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="82753-161">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-161">Specify a relative file path.</span></span>
* <span data-ttu-id="82753-162">Soubory oboru k datu poslední změny.</span><span class="sxs-lookup"><span data-stu-id="82753-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="82753-163">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="82753-164">Přetížení</span><span class="sxs-lookup"><span data-stu-id="82753-164">Overload</span></span> | <span data-ttu-id="82753-165">Popis</span><span class="sxs-lookup"><span data-stu-id="82753-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="82753-166">Přijme volitelný `root` relativní parametr cesty.</span><span class="sxs-lookup"><span data-stu-id="82753-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="82753-167">`root` Zadejte volání <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> oboru pro tyto prostředky v rámci zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="82753-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="82753-168">Přijme volitelný `root` parametr relativní cesty `lastModified` a<xref:System.DateTimeOffset>parametr data ( ).</span><span class="sxs-lookup"><span data-stu-id="82753-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="82753-169">Datum `lastModified` obory poslední změny datum <xref:Microsoft.Extensions.FileProviders.IFileInfo> pro instance <xref:Microsoft.Extensions.FileProviders.IFileProvider>vrácené .</span><span class="sxs-lookup"><span data-stu-id="82753-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="82753-170">Přijme volitelnou `root` relativní `lastModified` cestu, `manifestName` datum a parametry.</span><span class="sxs-lookup"><span data-stu-id="82753-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="82753-171">Představuje `manifestName` název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="82753-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="82753-172">Složený souborZprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="82753-172">CompositeFileProvider</span></span>

<span data-ttu-id="82753-173">Kombinuje <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` instance, vystavuje jediné rozhraní pro práci se soubory z více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="82753-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="82753-174">Při vytváření `CompositeFileProvider`, předat `IFileProvider` jednu nebo více instancí jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="82753-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="82753-175">V ukázkové aplikaci *FileProviderSample* `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a a `CompositeFileProvider` poskytují soubory registrovanému v kontejneru služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="82753-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="82753-176">V `Startup.ConfigureServices` metodě projektu se nachází následující kód:</span><span class="sxs-lookup"><span data-stu-id="82753-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="82753-177">Sledujte změny</span><span class="sxs-lookup"><span data-stu-id="82753-177">Watch for changes</span></span>

<span data-ttu-id="82753-178">Metoda <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> poskytuje scénář sledovat jeden nebo více souborů nebo adresářů pro změny.</span><span class="sxs-lookup"><span data-stu-id="82753-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="82753-179">Metoda: `Watch`</span><span class="sxs-lookup"><span data-stu-id="82753-179">The `Watch` method:</span></span>

* <span data-ttu-id="82753-180">Přijme řetězec cesty k souboru, který může použít [glob vzory](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="82753-181">Vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>hodnotu .</span><span class="sxs-lookup"><span data-stu-id="82753-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="82753-182">Výsledný token změny zveřejňuje:</span><span class="sxs-lookup"><span data-stu-id="82753-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="82753-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Vlastnost, která může být zkontrolována k určení, pokud došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="82753-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="82753-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Nazývá se, když jsou zjištěny změny zadaného řetězce cesty.</span><span class="sxs-lookup"><span data-stu-id="82753-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="82753-185">Každý token změny volá pouze jeho přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="82753-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="82753-186">Chcete-li povolit <xref:System.Threading.Tasks.TaskCompletionSource`1> konstantní monitorování, použijte `IChangeToken` (viz níže) nebo znovu vytvořte instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="82753-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="82753-187">Ukázková aplikace *WatchConsole* zapíše zprávu při každé změně souboru *TXT* v adresáři *TextFiles:*</span><span class="sxs-lookup"><span data-stu-id="82753-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="82753-188">Některé systémy souborů, jako jsou kontejnery Dockeru a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změně.</span><span class="sxs-lookup"><span data-stu-id="82753-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="82753-189">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou `1` prostředí `true` na systém souborů nebo pro dotazování na změny každé čtyři sekundy (nelze konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="82753-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="82753-190">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="82753-190">Glob patterns</span></span>

<span data-ttu-id="82753-191">Cesty systému souborů používají zástupné vzory nazývané *glob (nebo globbing) vzory*.</span><span class="sxs-lookup"><span data-stu-id="82753-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="82753-192">Určete skupiny souborů s těmito vzorky.</span><span class="sxs-lookup"><span data-stu-id="82753-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="82753-193">Dva zástupné znaky `*` `**`jsou a :</span><span class="sxs-lookup"><span data-stu-id="82753-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="82753-194">Odpovídá všemu na aktuální úrovni složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="82753-195">Shody jsou `/` ukončeny a `.` znaky v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="82753-196">Odpovídá všemu na více úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="82753-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="82753-197">Lze použít k rekurzivně shodovat mnoho souborů v hierarchii adresáře.</span><span class="sxs-lookup"><span data-stu-id="82753-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="82753-198">Následující tabulka obsahuje běžné příklady glob patterns.</span><span class="sxs-lookup"><span data-stu-id="82753-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="82753-199">Vzor</span><span class="sxs-lookup"><span data-stu-id="82753-199">Pattern</span></span>  |<span data-ttu-id="82753-200">Popis</span><span class="sxs-lookup"><span data-stu-id="82753-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="82753-201">Odpovídá určitému souboru v určitém adresáři.</span><span class="sxs-lookup"><span data-stu-id="82753-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="82753-202">Porovná všechny soubory s příponou *TXT* v určitém adresáři.</span><span class="sxs-lookup"><span data-stu-id="82753-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="82753-203">Odpovídá všem *souborům appsettings.json* v adresářích přesně o *jednu* úroveň pod adresářovou složkou.</span><span class="sxs-lookup"><span data-stu-id="82753-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="82753-204">Porovná všechny soubory s příponou *TXT,* která se nachází kdekoli ve složce *adresáře.*</span><span class="sxs-lookup"><span data-stu-id="82753-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="82753-205">ASP.NET Core abstraktů přístup k systému souborů pomocí zprostředkovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="82753-206">Zprostředkovatelé souborů se používají v celém rámci ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="82753-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="82753-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zveřejňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a kořen `IFileProvider` [webu](xref:fundamentals/index#web-root) jako typy.</span><span class="sxs-lookup"><span data-stu-id="82753-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="82753-208">[Middleware statického souboru](xref:fundamentals/static-files) používá k vyhledání statických souborů zprostředkovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="82753-209">[Razor](xref:mvc/views/razor) používá zprostředkovatele souborů k vyhledání stránek a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="82753-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="82753-210">Nástroje .NET Core používají zprostředkovatele souborů a glob vzory určit, které soubory mají být publikovány.</span><span class="sxs-lookup"><span data-stu-id="82753-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="82753-211">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="82753-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="82753-212">Rozhraní zprostředkovatele souborů</span><span class="sxs-lookup"><span data-stu-id="82753-212">File Provider interfaces</span></span>

<span data-ttu-id="82753-213">Primární rozhraní <xref:Microsoft.Extensions.FileProviders.IFileProvider>je .</span><span class="sxs-lookup"><span data-stu-id="82753-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="82753-214">`IFileProvider`vystavuje metody:</span><span class="sxs-lookup"><span data-stu-id="82753-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="82753-215">Získat informace<xref:Microsoft.Extensions.FileProviders.IFileInfo>o souboru ( ).</span><span class="sxs-lookup"><span data-stu-id="82753-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="82753-216">Získat informace<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o adresáři ( ).</span><span class="sxs-lookup"><span data-stu-id="82753-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="82753-217">Nastavte oznámení o změnách <xref:Microsoft.Extensions.Primitives.IChangeToken>(pomocí ).</span><span class="sxs-lookup"><span data-stu-id="82753-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="82753-218">`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="82753-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="82753-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)</span><span class="sxs-lookup"><span data-stu-id="82753-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="82753-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum</span><span class="sxs-lookup"><span data-stu-id="82753-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="82753-221">Ze souboru můžete číst pomocí metody [IFileInfo.CreateReadStream.](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)</span><span class="sxs-lookup"><span data-stu-id="82753-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="82753-222">Ukázková aplikace ukazuje, jak nakonfigurovat `Startup.ConfigureServices` zprostředkovatele souborů v aplikaci pro použití v celé aplikaci prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="82753-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="82753-223">Implementace zprostředkovatele souborů</span><span class="sxs-lookup"><span data-stu-id="82753-223">File Provider implementations</span></span>

<span data-ttu-id="82753-224">K dispozici `IFileProvider` jsou tři implementace.</span><span class="sxs-lookup"><span data-stu-id="82753-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="82753-225">Implementace</span><span class="sxs-lookup"><span data-stu-id="82753-225">Implementation</span></span> | <span data-ttu-id="82753-226">Popis</span><span class="sxs-lookup"><span data-stu-id="82753-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="82753-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="82753-228">Fyzický zprostředkovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="82753-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="82753-229">Objekt ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="82753-230">Vložený zprostředkovatel manifestu se používá pro přístup k souborům vloženým v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="82753-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="82753-231">Složený souborZprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="82753-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="82753-232">Složený zprostředkovatel se používá k poskytování kombinovaného přístupu k souborům a adresářům od jednoho nebo více jiných poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="82753-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="82753-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-233">PhysicalFileProvider</span></span>

<span data-ttu-id="82753-234">Poskytuje <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="82753-235">`PhysicalFileProvider`používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického zprostředkovatele) a obory všechny cesty do adresáře a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="82753-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="82753-236">Toto obory brání přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="82753-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="82753-237">Nejběžnější scénář pro vytváření a `PhysicalFileProvider` používání je `IFileProvider` požádat v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="82753-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="82753-238">Při vytváření instancí tohoto zprostředkovatele přímo je vyžadována cesta k adresáři, která slouží jako základní cesta pro všechny požadavky provedené pomocí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="82753-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="82753-239">Následující kód ukazuje, jak `PhysicalFileProvider` vytvořit a použít k získání obsahu adresáře a informace o souboru:</span><span class="sxs-lookup"><span data-stu-id="82753-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="82753-240">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="82753-240">Types in the preceding example:</span></span>

* <span data-ttu-id="82753-241">`provider`je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="82753-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="82753-242">`contents`je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="82753-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="82753-243">`fileInfo`je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="82753-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="82753-244">Zprostředkovatel souborů lze itetovat prostřednictvím adresáře určeného `applicationRoot` nebo volání `GetFileInfo` získat informace o souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="82753-245">Zprostředkovatel souborů nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="82753-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="82753-246">Ukázková aplikace vytvoří zprostředkovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="82753-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="82753-247">Objekt ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="82753-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="82753-248">Slouží <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> k přístupu k souborům vloženým do sestavení.</span><span class="sxs-lookup"><span data-stu-id="82753-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="82753-249">Používá `ManifestEmbeddedFileProvider` manifest zkompilovaný do sestavení k rekonstrukci původní cesty vložené soubory.</span><span class="sxs-lookup"><span data-stu-id="82753-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="82753-250">Chcete-li generovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na `true`.</span><span class="sxs-lookup"><span data-stu-id="82753-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="82753-251">Určete soubory, které chcete vložit pomocí [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="82753-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="82753-252">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které chcete vložit do sestavy.</span><span class="sxs-lookup"><span data-stu-id="82753-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="82753-253">Ukázková aplikace `ManifestEmbeddedFileProvider` vytvoří a předá aktuálně spuštěné sestavení svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="82753-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="82753-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="82753-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="82753-255">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="82753-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="82753-256">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-256">Specify a relative file path.</span></span>
* <span data-ttu-id="82753-257">Soubory oboru k datu poslední změny.</span><span class="sxs-lookup"><span data-stu-id="82753-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="82753-258">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="82753-259">Přetížení</span><span class="sxs-lookup"><span data-stu-id="82753-259">Overload</span></span> | <span data-ttu-id="82753-260">Popis</span><span class="sxs-lookup"><span data-stu-id="82753-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="82753-261">Přijme volitelný `root` relativní parametr cesty.</span><span class="sxs-lookup"><span data-stu-id="82753-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="82753-262">`root` Zadejte volání <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> oboru pro tyto prostředky v rámci zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="82753-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="82753-263">Přijme volitelný `root` parametr relativní cesty `lastModified` a<xref:System.DateTimeOffset>parametr data ( ).</span><span class="sxs-lookup"><span data-stu-id="82753-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="82753-264">Datum `lastModified` obory poslední změny datum <xref:Microsoft.Extensions.FileProviders.IFileInfo> pro instance <xref:Microsoft.Extensions.FileProviders.IFileProvider>vrácené .</span><span class="sxs-lookup"><span data-stu-id="82753-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="82753-265">Přijme volitelnou `root` relativní `lastModified` cestu, `manifestName` datum a parametry.</span><span class="sxs-lookup"><span data-stu-id="82753-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="82753-266">Představuje `manifestName` název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="82753-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="82753-267">Složený souborZprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="82753-267">CompositeFileProvider</span></span>

<span data-ttu-id="82753-268">Kombinuje <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` instance, vystavuje jediné rozhraní pro práci se soubory z více zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="82753-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="82753-269">Při vytváření `CompositeFileProvider`, předat `IFileProvider` jednu nebo více instancí jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="82753-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="82753-270">V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a a `CompositeFileProvider` poskytují soubory registrovanému v kontejneru služeb aplikace:</span><span class="sxs-lookup"><span data-stu-id="82753-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="82753-271">Sledujte změny</span><span class="sxs-lookup"><span data-stu-id="82753-271">Watch for changes</span></span>

<span data-ttu-id="82753-272">[Metoda IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) poskytuje scénář pro sledování jednoho nebo více souborů nebo adresářů pro změny.</span><span class="sxs-lookup"><span data-stu-id="82753-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="82753-273">`Watch`přijme řetězec cesty, který může použít [glob patterns](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="82753-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="82753-274">`Watch`vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="82753-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="82753-275">Token změny zveřejňuje:</span><span class="sxs-lookup"><span data-stu-id="82753-275">The change token exposes:</span></span>

* <span data-ttu-id="82753-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Vlastnost, která může být zkontrolována k určení, pokud došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="82753-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="82753-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Nazývá se, když jsou zjištěny změny zadaného řetězce cesty.</span><span class="sxs-lookup"><span data-stu-id="82753-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="82753-278">Každý token změny volá pouze jeho přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="82753-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="82753-279">Chcete-li povolit <xref:System.Threading.Tasks.TaskCompletionSource`1> konstantní monitorování, použijte `IChangeToken` (viz níže) nebo znovu vytvořte instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="82753-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="82753-280">V ukázkové aplikaci je konzolová aplikace *WatchConsole* nakonfigurována tak, aby při každé změně textového souboru zobrazovala zprávu:</span><span class="sxs-lookup"><span data-stu-id="82753-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="82753-281">Některé systémy souborů, jako jsou kontejnery Dockeru a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změně.</span><span class="sxs-lookup"><span data-stu-id="82753-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="82753-282">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou `1` prostředí `true` na systém souborů nebo pro dotazování na změny každé čtyři sekundy (nelze konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="82753-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="82753-283">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="82753-283">Glob patterns</span></span>

<span data-ttu-id="82753-284">Cesty systému souborů používají zástupné vzory nazývané *glob (nebo globbing) vzory*.</span><span class="sxs-lookup"><span data-stu-id="82753-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="82753-285">Určete skupiny souborů s těmito vzorky.</span><span class="sxs-lookup"><span data-stu-id="82753-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="82753-286">Dva zástupné znaky `*` `**`jsou a :</span><span class="sxs-lookup"><span data-stu-id="82753-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="82753-287">Odpovídá všemu na aktuální úrovni složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="82753-288">Shody jsou `/` ukončeny a `.` znaky v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="82753-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="82753-289">Odpovídá všemu na více úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="82753-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="82753-290">Lze použít k rekurzivně shodovat mnoho souborů v hierarchii adresáře.</span><span class="sxs-lookup"><span data-stu-id="82753-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="82753-291">**Příklady globvzor**</span><span class="sxs-lookup"><span data-stu-id="82753-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="82753-292">Odpovídá určitému souboru v určitém adresáři.</span><span class="sxs-lookup"><span data-stu-id="82753-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="82753-293">Porovná všechny soubory s příponou *TXT* v určitém adresáři.</span><span class="sxs-lookup"><span data-stu-id="82753-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="82753-294">Odpovídá `appsettings.json` všem souborům v adresářích přesně o *jednu* úroveň pod adresářovou složkou.</span><span class="sxs-lookup"><span data-stu-id="82753-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="82753-295">Porovnává všechny soubory s příponou *TXT,* která se nachází kdekoli ve složce *adresáře.*</span><span class="sxs-lookup"><span data-stu-id="82753-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
