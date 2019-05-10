---
title: Zprostředkovatelé souborů v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core abstrahuje přístupu k systému souborů prostřednictvím zprostředkovatelé souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 93eb48d81a853061a874641e84b4875849690a93
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900498"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="441ac-103">Zprostředkovatelé souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="441ac-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="441ac-104">Podle [Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="441ac-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="441ac-105">ASP.NET Core abstrahuje přístupu k systému souborů prostřednictvím zprostředkovatelé souborů.</span><span class="sxs-lookup"><span data-stu-id="441ac-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="441ac-106">Zprostředkovatelé souborů se používají v rozhraní ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="441ac-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="441ac-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) zpřístupní obsah kořenové a kořenový adresář webové jako aplikace `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="441ac-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="441ac-108">[Middleware statické soubory](xref:fundamentals/static-files) zprostředkovatelé souborů používá k vyhledání statické soubory.</span><span class="sxs-lookup"><span data-stu-id="441ac-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="441ac-109">[Razor](xref:mvc/views/razor) zprostředkovatelé souborů používá k nalezení stránek a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="441ac-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="441ac-110">Nástroje pro .NET core používá k určení, které soubory by se měly zveřejňovat zprostředkovatelé souborů a vzory glob.</span><span class="sxs-lookup"><span data-stu-id="441ac-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="441ac-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="441ac-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="441ac-112">Rozhraní poskytovatele souboru</span><span class="sxs-lookup"><span data-stu-id="441ac-112">File Provider interfaces</span></span>

<span data-ttu-id="441ac-113">Primární rozhraní se ale [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="441ac-113">The primary interface is [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> <span data-ttu-id="441ac-114">`IFileProvider` poskytuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="441ac-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="441ac-115">Získání informací o souboru ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span><span class="sxs-lookup"><span data-stu-id="441ac-115">Obtain file information ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span></span>
* <span data-ttu-id="441ac-116">Získat informace o adresáři ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span><span class="sxs-lookup"><span data-stu-id="441ac-116">Obtain directory information ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span></span>
* <span data-ttu-id="441ac-117">Nastavení oznámení o změnách (pomocí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span><span class="sxs-lookup"><span data-stu-id="441ac-117">Set up change notifications (using an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span></span>

<span data-ttu-id="441ac-118">`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="441ac-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* [<span data-ttu-id="441ac-119">Existuje</span><span class="sxs-lookup"><span data-stu-id="441ac-119">Exists</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [<span data-ttu-id="441ac-120">IsDirectory</span><span class="sxs-lookup"><span data-stu-id="441ac-120">IsDirectory</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [<span data-ttu-id="441ac-121">Název</span><span class="sxs-lookup"><span data-stu-id="441ac-121">Name</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* <span data-ttu-id="441ac-122">[Délka](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (v bajtech)</span><span class="sxs-lookup"><span data-stu-id="441ac-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in bytes)</span></span>
* <span data-ttu-id="441ac-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) datum</span><span class="sxs-lookup"><span data-stu-id="441ac-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) date</span></span>

<span data-ttu-id="441ac-124">Můžete číst ze souboru pomocí [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) metody.</span><span class="sxs-lookup"><span data-stu-id="441ac-124">You can read from the file using the [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) method.</span></span>

<span data-ttu-id="441ac-125">Ukázková aplikace předvádí, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro používání v rámci aplikace prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="441ac-125">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="441ac-126">Implementace zprostředkovatele souborů</span><span class="sxs-lookup"><span data-stu-id="441ac-126">File Provider implementations</span></span>

<span data-ttu-id="441ac-127">Tři implementace `IFileProvider` jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="441ac-127">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="441ac-128">Implementace</span><span class="sxs-lookup"><span data-stu-id="441ac-128">Implementation</span></span> | <span data-ttu-id="441ac-129">Popis</span><span class="sxs-lookup"><span data-stu-id="441ac-129">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="441ac-130">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="441ac-130">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="441ac-131">Fyzické zprostředkovatele se používá pro přístup k fyzické soubory v systému.</span><span class="sxs-lookup"><span data-stu-id="441ac-131">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="441ac-132">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="441ac-132">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="441ac-133">Poskytovateli vloženého manifestu se používá pro přístup k souborům, které jsou součástí sestavení.</span><span class="sxs-lookup"><span data-stu-id="441ac-133">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="441ac-134">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="441ac-134">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="441ac-135">Složený zprostředkovatele slouží k poskytování kombinovaný přístup k souborů a adresářů od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="441ac-135">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="441ac-136">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="441ac-136">PhysicalFileProvider</span></span>

<span data-ttu-id="441ac-137">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) poskytuje přístup k fyzické systému souborů.</span><span class="sxs-lookup"><span data-stu-id="441ac-137">The [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) provides access to the physical file system.</span></span> <span data-ttu-id="441ac-138">`PhysicalFileProvider` používá [System.IO.File](/dotnet/api/system.io.file) typu (u fyzického provider) a všechny cesty k adresáři a jeho podřízené obory.</span><span class="sxs-lookup"><span data-stu-id="441ac-138">`PhysicalFileProvider` uses the [System.IO.File](/dotnet/api/system.io.file) type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="441ac-139">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jejích potomků.</span><span class="sxs-lookup"><span data-stu-id="441ac-139">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="441ac-140">Při vytváření instance tohoto zprostředkovatele, cestu k adresáři je povinný a slouží jako základní cesta pro všechny požadavky vytvořené pomocí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="441ac-140">When instantiating this provider, a directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="441ac-141">Můžete vytvořit instanci `PhysicalFileProvider` poskytovatele přímo, nebo můžete požádat o `IFileProvider` v konstruktoru prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="441ac-141">You can instantiate a `PhysicalFileProvider` provider directly, or you can request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="441ac-142">**Statické typy**</span><span class="sxs-lookup"><span data-stu-id="441ac-142">**Static types**</span></span>

<span data-ttu-id="441ac-143">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsah adresáře a informace o souboru:</span><span class="sxs-lookup"><span data-stu-id="441ac-143">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="441ac-144">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="441ac-144">Types in the preceding example:</span></span>

* <span data-ttu-id="441ac-145">`provider` je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="441ac-145">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="441ac-146">`contents` je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="441ac-146">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="441ac-147">`fileInfo` je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="441ac-147">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="441ac-148">Poskytovatel souboru lze použít k iteraci v rámci adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací souboru.</span><span class="sxs-lookup"><span data-stu-id="441ac-148">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="441ac-149">Poskytovatel soubor nemá přístup mimo `applicationRoot` adresáře.</span><span class="sxs-lookup"><span data-stu-id="441ac-149">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="441ac-150">Ukázková aplikace vytvoří poskytovatel aplikace `Startup.ConfigureServices` pomocí [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span><span class="sxs-lookup"><span data-stu-id="441ac-150">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

<span data-ttu-id="441ac-151">**Získat typy zprostředkovatele souborů pomocí vkládání závislostí**</span><span class="sxs-lookup"><span data-stu-id="441ac-151">**Obtain File Provider types with dependency injection**</span></span>

<span data-ttu-id="441ac-152">Vložit do jakéhokoli konstruktoru třídy zprostředkovatele a přiřaďte ho do místního pole.</span><span class="sxs-lookup"><span data-stu-id="441ac-152">Inject the provider into any class constructor and assign it to a local field.</span></span> <span data-ttu-id="441ac-153">Použijte pole v rámci metod tříd pro přístup k souborům.</span><span class="sxs-lookup"><span data-stu-id="441ac-153">Use the field throughout the class's methods to access files.</span></span>

<span data-ttu-id="441ac-154">V ukázkové aplikaci `IndexModel` přijímá třídy `IFileProvider` instance získat obsah adresáře pro základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="441ac-154">In the sample app, the `IndexModel` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="441ac-155">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="441ac-155">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="441ac-156">`IDirectoryContents` Jsou vstupní stránky.</span><span class="sxs-lookup"><span data-stu-id="441ac-156">The `IDirectoryContents` are iterated in the page.</span></span>

<span data-ttu-id="441ac-157">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="441ac-157">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="441ac-158">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="441ac-158">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="441ac-159">[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) se používá pro přístup k souborům, které jsou vložené v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="441ac-159">The [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="441ac-160">`ManifestEmbeddedFileProvider` Manifestu kompilovány do sestavení používá k rekonstrukci původní cesty vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="441ac-160">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="441ac-161">Chcete-li vygenerovat manifest vložené soubory, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost `true`.</span><span class="sxs-lookup"><span data-stu-id="441ac-161">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="441ac-162">Zadejte soubory, které chcete vložit s [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="441ac-162">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="441ac-163">Použití [glob vzory](#glob-patterns) určit jeden nebo více souborů určených pro vložení do sestavení.</span><span class="sxs-lookup"><span data-stu-id="441ac-163">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="441ac-164">Vytvoří ukázkovou aplikaci `ManifestEmbeddedFileProvider` a předává právě spuštěné sestavení do konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="441ac-164">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="441ac-165">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="441ac-165">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="441ac-166">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="441ac-166">Additional overloads allow you to:</span></span>

* <span data-ttu-id="441ac-167">Zadejte relativní cestou k souboru.</span><span class="sxs-lookup"><span data-stu-id="441ac-167">Specify a relative file path.</span></span>
* <span data-ttu-id="441ac-168">Obor soubory na datum poslední změny.</span><span class="sxs-lookup"><span data-stu-id="441ac-168">Scope files to a last modified date.</span></span>
* <span data-ttu-id="441ac-169">Název vloženého zdroje obsahujícího vložený soubor manifestu.</span><span class="sxs-lookup"><span data-stu-id="441ac-169">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="441ac-170">přetížení</span><span class="sxs-lookup"><span data-stu-id="441ac-170">Overload</span></span> | <span data-ttu-id="441ac-171">Popis</span><span class="sxs-lookup"><span data-stu-id="441ac-171">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="441ac-172">ManifestEmbeddedFileProvider(Assembly, String)</span><span class="sxs-lookup"><span data-stu-id="441ac-172">ManifestEmbeddedFileProvider(Assembly, String)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | <span data-ttu-id="441ac-173">Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="441ac-173">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="441ac-174">Zadejte `root` do oboru volání [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) k těmto prostředkům na zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="441ac-174">Specify the `root` to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided path.</span></span> |
| [<span data-ttu-id="441ac-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="441ac-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | <span data-ttu-id="441ac-176">Přijímá volitelný `root` parametr relativní cesty a `lastModified` datum ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parametru.</span><span class="sxs-lookup"><span data-stu-id="441ac-176">Accepts an optional `root` relative path parameter and a `lastModified` date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parameter.</span></span> <span data-ttu-id="441ac-177">`lastModified` Obory datum poslední změny pro datum [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instancí vrácených [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="441ac-177">The `lastModified` date scopes the last modification date for the [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instances returned by the [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> |
| [<span data-ttu-id="441ac-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="441ac-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | <span data-ttu-id="441ac-179">Přijímá volitelný `root` relativní cesta `lastModified` data, a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="441ac-179">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="441ac-180">`manifestName` Představuje název vloženého zdroje obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="441ac-180">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="441ac-181">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="441ac-181">CompositeFileProvider</span></span>

<span data-ttu-id="441ac-182">[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) kombinuje `IFileProvider` instance vystavení jednotné rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="441ac-182">The [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="441ac-183">Při vytváření `CompositeFileProvider`, předejte jeden nebo více `IFileProvider` instance konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="441ac-183">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="441ac-184">V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` zadejte soubory `CompositeFileProvider` zaregistrovaný v kontejneru aplikace služby:</span><span class="sxs-lookup"><span data-stu-id="441ac-184">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="441ac-185">Sledování změn</span><span class="sxs-lookup"><span data-stu-id="441ac-185">Watch for changes</span></span>

<span data-ttu-id="441ac-186">[IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) metoda poskytuje scénář podívejte se na jeden nebo více souborů nebo adresářů pro změny.</span><span class="sxs-lookup"><span data-stu-id="441ac-186">The [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="441ac-187">`Watch` přijímá řetězec cesty, které můžete použít [glob vzory](#glob-patterns) zadat více souborů.</span><span class="sxs-lookup"><span data-stu-id="441ac-187">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="441ac-188">`Watch` Vrátí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span><span class="sxs-lookup"><span data-stu-id="441ac-188">`Watch` returns an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span></span> <span data-ttu-id="441ac-189">Token zpřístupňuje změny:</span><span class="sxs-lookup"><span data-stu-id="441ac-189">The change token exposes:</span></span>

* <span data-ttu-id="441ac-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): Vlastnost, která může být kontrolována k určení, pokud došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="441ac-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="441ac-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Volá se, když se zjistí změny na zadané cestě řetězec.</span><span class="sxs-lookup"><span data-stu-id="441ac-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="441ac-192">Každý token změnit jen volá jeho přidružené zpětné volání v reakci na jediné změny.</span><span class="sxs-lookup"><span data-stu-id="441ac-192">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="441ac-193">Chcete-li povolit konstantní monitorování, použijte [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (viz dole) nebo znovu vytvořit `IChangeToken` instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="441ac-193">To enable constant monitoring, use a [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="441ac-194">V ukázkové aplikaci *WatchConsole* konzoly aplikace je nakonfigurovaná pro zobrazení zprávy, když se změní textového souboru:</span><span class="sxs-lookup"><span data-stu-id="441ac-194">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="441ac-195">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky a nemusí spolehlivě posílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="441ac-195">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="441ac-196">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou prostředí, aby `1` nebo `true` k dotazování systému souborů pro změny každé čtyři sekundy (nejde konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="441ac-196">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="441ac-197">Vzory glob</span><span class="sxs-lookup"><span data-stu-id="441ac-197">Glob patterns</span></span>

<span data-ttu-id="441ac-198">Volat jeden vzor zástupných znaků použít systémové cesty k souborům *glob (nebo podpory zástupných znaků) vzory*.</span><span class="sxs-lookup"><span data-stu-id="441ac-198">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="441ac-199">Tyto vzory zadejte skupiny souborů.</span><span class="sxs-lookup"><span data-stu-id="441ac-199">Specify groups of files with these patterns.</span></span> <span data-ttu-id="441ac-200">Dva zástupné znaky jsou `*` a `**`:</span><span class="sxs-lookup"><span data-stu-id="441ac-200">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="441ac-201">Na aktuální úrovni složky, některý název souboru nebo libovolnou příponou odpovídá úplně všechno.</span><span class="sxs-lookup"><span data-stu-id="441ac-201">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="441ac-202">Shody jsou ukončeny `/` a `.` znaky v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="441ac-202">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="441ac-203">Napříč několika úrovněmi adresáře odpovídá úplně všechno.</span><span class="sxs-lookup"><span data-stu-id="441ac-203">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="441ac-204">Můžete použít k rekurzivnímu shodovat s mnoha soubory v hierarchii adresářů.</span><span class="sxs-lookup"><span data-stu-id="441ac-204">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="441ac-205">**Příklady glob vzor**</span><span class="sxs-lookup"><span data-stu-id="441ac-205">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="441ac-206">Odpovídá konkrétní soubor v konkrétní adresář.</span><span class="sxs-lookup"><span data-stu-id="441ac-206">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="441ac-207">Vyhledá všechny soubory s *.txt* rozšíření v konkrétní adresář.</span><span class="sxs-lookup"><span data-stu-id="441ac-207">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="441ac-208">Odpovídá všem `appsettings.json` soubory v adresářích přesně jednu úroveň níže *directory* složky.</span><span class="sxs-lookup"><span data-stu-id="441ac-208">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="441ac-209">Vyhledá všechny soubory s *.txt* rozšíření najít kdekoli v rámci *directory* složky.</span><span class="sxs-lookup"><span data-stu-id="441ac-209">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>
