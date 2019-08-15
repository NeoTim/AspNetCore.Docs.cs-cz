---
title: Poskytovatelé souborů v ASP.NET Core
author: guardrex
description: Přečtěte si, jak ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: b93b2df7fad7c173f43ad69aec865f09de6c9c34
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487580"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="da955-103">Poskytovatelé souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da955-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="da955-104">[Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="da955-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="da955-105">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="da955-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="da955-106">Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:</span><span class="sxs-lookup"><span data-stu-id="da955-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="da955-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) vystaví kořenový adresář obsahu aplikace a webový kořenový `IFileProvider` adresář jako typy.</span><span class="sxs-lookup"><span data-stu-id="da955-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="da955-108">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="da955-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="da955-109">[Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="da955-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="da955-110">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="da955-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="da955-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da955-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="da955-112">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="da955-112">File Provider interfaces</span></span>

<span data-ttu-id="da955-113">Primární rozhraní je [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="da955-113">The primary interface is [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> <span data-ttu-id="da955-114">`IFileProvider`Zpřístupňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="da955-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="da955-115">Získat informace o souboru ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span><span class="sxs-lookup"><span data-stu-id="da955-115">Obtain file information ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span></span>
* <span data-ttu-id="da955-116">Získat informace o adresáři ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span><span class="sxs-lookup"><span data-stu-id="da955-116">Obtain directory information ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span></span>
* <span data-ttu-id="da955-117">Nastavte oznámení o změně (pomocí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span><span class="sxs-lookup"><span data-stu-id="da955-117">Set up change notifications (using an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span></span>

<span data-ttu-id="da955-118">`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="da955-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* [<span data-ttu-id="da955-119">Neexistuje</span><span class="sxs-lookup"><span data-stu-id="da955-119">Exists</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [<span data-ttu-id="da955-120">Adresář</span><span class="sxs-lookup"><span data-stu-id="da955-120">IsDirectory</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [<span data-ttu-id="da955-121">Název</span><span class="sxs-lookup"><span data-stu-id="da955-121">Name</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* <span data-ttu-id="da955-122">[Délka](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (v bajtech)</span><span class="sxs-lookup"><span data-stu-id="da955-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in bytes)</span></span>
* <span data-ttu-id="da955-123">Datum [LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified)</span><span class="sxs-lookup"><span data-stu-id="da955-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) date</span></span>

<span data-ttu-id="da955-124">Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) .</span><span class="sxs-lookup"><span data-stu-id="da955-124">You can read from the file using the [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) method.</span></span>

<span data-ttu-id="da955-125">Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da955-125">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="da955-126">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="da955-126">File Provider implementations</span></span>

<span data-ttu-id="da955-127">K dispozici `IFileProvider` jsou tři implementace systému.</span><span class="sxs-lookup"><span data-stu-id="da955-127">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="da955-128">Implementace</span><span class="sxs-lookup"><span data-stu-id="da955-128">Implementation</span></span> | <span data-ttu-id="da955-129">Popis</span><span class="sxs-lookup"><span data-stu-id="da955-129">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="da955-130">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="da955-130">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="da955-131">Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="da955-131">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="da955-132">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="da955-132">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="da955-133">Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="da955-133">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="da955-134">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="da955-134">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="da955-135">Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="da955-135">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="da955-136">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="da955-136">PhysicalFileProvider</span></span>

<span data-ttu-id="da955-137">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="da955-137">The [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) provides access to the physical file system.</span></span> <span data-ttu-id="da955-138">`PhysicalFileProvider`používá typ [System. IO. File](/dotnet/api/system.io.file) (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="da955-138">`PhysicalFileProvider` uses the [System.IO.File](/dotnet/api/system.io.file) type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="da955-139">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="da955-139">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="da955-140">Při vytváření instance tohoto poskytovatele se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="da955-140">When instantiating this provider, a directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="da955-141">Můžete vytvořit instanci `PhysicalFileProvider` poskytovatele přímo nebo si můžete `IFileProvider` vyžádat v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da955-141">You can instantiate a `PhysicalFileProvider` provider directly, or you can request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="da955-142">**Statické typy**</span><span class="sxs-lookup"><span data-stu-id="da955-142">**Static types**</span></span>

<span data-ttu-id="da955-143">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="da955-143">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="da955-144">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="da955-144">Types in the preceding example:</span></span>

* <span data-ttu-id="da955-145">`provider``IFileProvider`je.</span><span class="sxs-lookup"><span data-stu-id="da955-145">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="da955-146">`contents``IDirectoryContents`je.</span><span class="sxs-lookup"><span data-stu-id="da955-146">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="da955-147">`fileInfo``IFileInfo`je.</span><span class="sxs-lookup"><span data-stu-id="da955-147">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="da955-148">Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="da955-148">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="da955-149">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="da955-149">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="da955-150">Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span><span class="sxs-lookup"><span data-stu-id="da955-150">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

<span data-ttu-id="da955-151">**Získání typů poskytovatele souborů pomocí injektáže závislosti**</span><span class="sxs-lookup"><span data-stu-id="da955-151">**Obtain File Provider types with dependency injection**</span></span>

<span data-ttu-id="da955-152">Vložit poskytovatele do libovolného konstruktoru třídy a přiřadit ho k místnímu poli.</span><span class="sxs-lookup"><span data-stu-id="da955-152">Inject the provider into any class constructor and assign it to a local field.</span></span> <span data-ttu-id="da955-153">Pro přístup k souborům použijte pole v rámci metod třídy.</span><span class="sxs-lookup"><span data-stu-id="da955-153">Use the field throughout the class's methods to access files.</span></span>

<span data-ttu-id="da955-154">V ukázkové aplikaci `IndexModel` třída `IFileProvider` obdrží instanci, která získá obsah adresáře pro základní cestu aplikace.</span><span class="sxs-lookup"><span data-stu-id="da955-154">In the sample app, the `IndexModel` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="da955-155">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="da955-155">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="da955-156">Na `IDirectoryContents` stránce jsou iterace.</span><span class="sxs-lookup"><span data-stu-id="da955-156">The `IDirectoryContents` are iterated in the page.</span></span>

<span data-ttu-id="da955-157">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="da955-157">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="da955-158">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="da955-158">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="da955-159">[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) se používá pro přístup k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="da955-159">The [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="da955-160">`ManifestEmbeddedFileProvider` Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="da955-160">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="da955-161">Chcete-li vygenerovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na `true`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="da955-161">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="da955-162">Zadejte soubory, které se mají [ &lt;vložit&gt;do EmbeddedResource](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="da955-162">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="da955-163">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="da955-163">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="da955-164">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="da955-164">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="da955-165">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="da955-165">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="da955-166">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="da955-166">Additional overloads allow you to:</span></span>

* <span data-ttu-id="da955-167">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="da955-167">Specify a relative file path.</span></span>
* <span data-ttu-id="da955-168">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="da955-168">Scope files to a last modified date.</span></span>
* <span data-ttu-id="da955-169">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="da955-169">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="da955-170">Metody</span><span class="sxs-lookup"><span data-stu-id="da955-170">Overload</span></span> | <span data-ttu-id="da955-171">Popis</span><span class="sxs-lookup"><span data-stu-id="da955-171">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="da955-172">ManifestEmbeddedFileProvider (sestavení, řetězec)</span><span class="sxs-lookup"><span data-stu-id="da955-172">ManifestEmbeddedFileProvider(Assembly, String)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | <span data-ttu-id="da955-173">Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="da955-173">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="da955-174">Určete rozsah volání, která se GetDirectoryContents na tyto prostředky v zadané cestě. [](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) `root`</span><span class="sxs-lookup"><span data-stu-id="da955-174">Specify the `root` to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided path.</span></span> |
| [<span data-ttu-id="da955-175">ManifestEmbeddedFileProvider (sestavení, řetězec, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="da955-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | <span data-ttu-id="da955-176">Přijímá parametr volitelné `root` relativní cesty `lastModified` a parametr Date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)).</span><span class="sxs-lookup"><span data-stu-id="da955-176">Accepts an optional `root` relative path parameter and a `lastModified` date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parameter.</span></span> <span data-ttu-id="da955-177">Datum v oboru datum poslední změny pro instance [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) , které vrátí IFileProvider. [](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) `lastModified`</span><span class="sxs-lookup"><span data-stu-id="da955-177">The `lastModified` date scopes the last modification date for the [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instances returned by the [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> |
| [<span data-ttu-id="da955-178">ManifestEmbeddedFileProvider (sestavení, řetězec, řetězec, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="da955-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | <span data-ttu-id="da955-179">Přijímá volitelnou `root` relativní cestu, `lastModified` datum a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="da955-179">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="da955-180">`manifestName` Představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="da955-180">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="da955-181">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="da955-181">CompositeFileProvider</span></span>

<span data-ttu-id="da955-182">[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) kombinuje `IFileProvider` instance a zveřejňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="da955-182">The [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="da955-183">Při vytváření `CompositeFileProvider`, předejte do konstruktoru jednu `IFileProvider` nebo více instancí.</span><span class="sxs-lookup"><span data-stu-id="da955-183">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="da955-184">V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="da955-184">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="da955-185">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="da955-185">Watch for changes</span></span>

<span data-ttu-id="da955-186">Metoda [IFileProvider. Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="da955-186">The [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="da955-187">`Watch`přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="da955-187">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="da955-188">`Watch`Vrátí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span><span class="sxs-lookup"><span data-stu-id="da955-188">`Watch` returns an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span></span> <span data-ttu-id="da955-189">Token pro změnu zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="da955-189">The change token exposes:</span></span>

* <span data-ttu-id="da955-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): Vlastnost, která se dá zkontrolovat, aby se zjistilo, jestli došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="da955-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="da955-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Volá se, když se v zadaném řetězci cesty zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="da955-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="da955-192">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="da955-192">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="da955-193">Chcete-li povolit stálé monitorování, použijte [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (zobrazený níže) nebo `IChangeToken` znovu vytvořte instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="da955-193">To enable constant monitoring, use a [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="da955-194">V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:</span><span class="sxs-lookup"><span data-stu-id="da955-194">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="da955-195">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="da955-195">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="da955-196">Nastavte proměnnou `1` `true` prostředí na nebo k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat). `DOTNET_USE_POLLING_FILE_WATCHER`</span><span class="sxs-lookup"><span data-stu-id="da955-196">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="da955-197">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="da955-197">Glob patterns</span></span>

<span data-ttu-id="da955-198">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* .</span><span class="sxs-lookup"><span data-stu-id="da955-198">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="da955-199">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="da955-199">Specify groups of files with these patterns.</span></span> <span data-ttu-id="da955-200">Dva zástupné znaky `*` jsou `**`a:</span><span class="sxs-lookup"><span data-stu-id="da955-200">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="da955-201">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="da955-201">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="da955-202">Shody jsou zakončeny `/` znaky `.` a v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="da955-202">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="da955-203">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="da955-203">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="da955-204">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="da955-204">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="da955-205">**Příklady vzorů glob**</span><span class="sxs-lookup"><span data-stu-id="da955-205">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="da955-206">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="da955-206">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="da955-207">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="da955-207">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="da955-208">Vyhledá `appsettings.json` všechny soubory v adresářích přesně jednu úroveň pod adresářovou složkou.</span><span class="sxs-lookup"><span data-stu-id="da955-208">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="da955-209">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="da955-209">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>
