---
title: Zprostředkovatelé souborů v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core abstrahuje přístupu k systému souborů prostřednictvím zprostředkovatelé souborů.
ms.author: riande
ms.custom: mvc
ms.date: 08/01/2018
uid: fundamentals/file-providers
ms.openlocfilehash: 5d0d46ba82cd84e48e5a9b23d6d330d8888beb41
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51570097"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="27309-103">Zprostředkovatelé souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="27309-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="27309-104">Podle [Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="27309-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="27309-105">ASP.NET Core abstrahuje přístupu k systému souborů prostřednictvím zprostředkovatelé souborů.</span><span class="sxs-lookup"><span data-stu-id="27309-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="27309-106">Zprostředkovatelé souborů se používají v rozhraní ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="27309-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="27309-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) zpřístupní obsah kořenové a kořenový adresář webové jako aplikace `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="27309-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="27309-108">[Middleware statické soubory](xref:fundamentals/static-files) zprostředkovatelé souborů používá k vyhledání statické soubory.</span><span class="sxs-lookup"><span data-stu-id="27309-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="27309-109">[Razor](xref:mvc/views/razor) zprostředkovatelé souborů používá k nalezení stránek a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="27309-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="27309-110">Nástroje pro .NET core používá k určení, které soubory by se měly zveřejňovat zprostředkovatelé souborů a vzory glob.</span><span class="sxs-lookup"><span data-stu-id="27309-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="27309-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="27309-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="27309-112">Rozhraní poskytovatele souboru</span><span class="sxs-lookup"><span data-stu-id="27309-112">File Provider interfaces</span></span>

<span data-ttu-id="27309-113">Primární rozhraní se ale [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="27309-113">The primary interface is [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> <span data-ttu-id="27309-114">`IFileProvider` poskytuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="27309-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="27309-115">Získání informací o souboru ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span><span class="sxs-lookup"><span data-stu-id="27309-115">Obtain file information ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span></span>
* <span data-ttu-id="27309-116">Získat informace o adresáři ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span><span class="sxs-lookup"><span data-stu-id="27309-116">Obtain directory information ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span></span>
* <span data-ttu-id="27309-117">Nastavení oznámení o změnách (pomocí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span><span class="sxs-lookup"><span data-stu-id="27309-117">Set up change notifications (using an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span></span>

<span data-ttu-id="27309-118">`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="27309-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* [<span data-ttu-id="27309-119">Existuje</span><span class="sxs-lookup"><span data-stu-id="27309-119">Exists</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [<span data-ttu-id="27309-120">IsDirectory</span><span class="sxs-lookup"><span data-stu-id="27309-120">IsDirectory</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [<span data-ttu-id="27309-121">Jméno</span><span class="sxs-lookup"><span data-stu-id="27309-121">Name</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* <span data-ttu-id="27309-122">[Délka](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (v bajtech)</span><span class="sxs-lookup"><span data-stu-id="27309-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in bytes)</span></span>
* <span data-ttu-id="27309-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) datum</span><span class="sxs-lookup"><span data-stu-id="27309-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) date</span></span>

<span data-ttu-id="27309-124">Můžete číst ze souboru pomocí [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) metody.</span><span class="sxs-lookup"><span data-stu-id="27309-124">You can read from the file using the [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) method.</span></span>

<span data-ttu-id="27309-125">Ukázková aplikace předvádí, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro používání v rámci aplikace prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="27309-125">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="27309-126">Implementace zprostředkovatele souborů</span><span class="sxs-lookup"><span data-stu-id="27309-126">File Provider implementations</span></span>

<span data-ttu-id="27309-127">Tři implementace `IFileProvider` jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="27309-127">Three implementations of `IFileProvider` are available.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="27309-128">Implementace</span><span class="sxs-lookup"><span data-stu-id="27309-128">Implementation</span></span> | <span data-ttu-id="27309-129">Popis</span><span class="sxs-lookup"><span data-stu-id="27309-129">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="27309-130">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-130">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="27309-131">Fyzické zprostředkovatele se používá pro přístup k fyzické soubory v systému.</span><span class="sxs-lookup"><span data-stu-id="27309-131">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="27309-132">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-132">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="27309-133">Poskytovateli vloženého manifestu se používá pro přístup k souborům, které jsou součástí sestavení.</span><span class="sxs-lookup"><span data-stu-id="27309-133">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="27309-134">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-134">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="27309-135">Složený zprostředkovatele slouží k poskytování kombinovaný přístup k souborů a adresářů od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="27309-135">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="27309-136">Implementace</span><span class="sxs-lookup"><span data-stu-id="27309-136">Implementation</span></span> | <span data-ttu-id="27309-137">Popis</span><span class="sxs-lookup"><span data-stu-id="27309-137">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="27309-138">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-138">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="27309-139">Fyzické zprostředkovatele se používá pro přístup k fyzické soubory v systému.</span><span class="sxs-lookup"><span data-stu-id="27309-139">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="27309-140">EmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-140">EmbeddedFileProvider</span></span>](#embeddedfileprovider) | <span data-ttu-id="27309-141">Vložený zprostředkovatele se používá pro přístup k souborům, které jsou součástí sestavení.</span><span class="sxs-lookup"><span data-stu-id="27309-141">The embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="27309-142">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-142">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="27309-143">Složený zprostředkovatele slouží k poskytování kombinovaný přístup k souborů a adresářů od jiných zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="27309-143">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

::: moniker-end

### <a name="physicalfileprovider"></a><span data-ttu-id="27309-144">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-144">PhysicalFileProvider</span></span>

<span data-ttu-id="27309-145">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) poskytuje přístup k fyzické systému souborů.</span><span class="sxs-lookup"><span data-stu-id="27309-145">The [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) provides access to the physical file system.</span></span> <span data-ttu-id="27309-146">`PhysicalFileProvider` používá [System.IO.File](/dotnet/api/system.io.file) typu (u fyzického provider) a všechny cesty k adresáři a jeho podřízené obory.</span><span class="sxs-lookup"><span data-stu-id="27309-146">`PhysicalFileProvider` uses the [System.IO.File](/dotnet/api/system.io.file) type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="27309-147">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jejích potomků.</span><span class="sxs-lookup"><span data-stu-id="27309-147">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="27309-148">Při vytváření instance tohoto zprostředkovatele, cestu k adresáři je povinný a slouží jako základní cesta pro všechny požadavky vytvořené pomocí zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="27309-148">When instantiating this provider, a directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="27309-149">Můžete vytvořit instanci `PhysicalFileProvider` poskytovatele přímo, nebo můžete požádat o `IFileProvider` v konstruktoru prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="27309-149">You can instantiate a `PhysicalFileProvider` provider directly, or you can request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="27309-150">**Statické typy**</span><span class="sxs-lookup"><span data-stu-id="27309-150">**Static types**</span></span>

<span data-ttu-id="27309-151">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsah adresáře a informace o souboru:</span><span class="sxs-lookup"><span data-stu-id="27309-151">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="27309-152">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="27309-152">Types in the preceding example:</span></span>

* <span data-ttu-id="27309-153">`provider` je `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="27309-153">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="27309-154">`contents` je `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="27309-154">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="27309-155">`fileInfo` je `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="27309-155">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="27309-156">Poskytovatel souboru lze použít k iteraci v rámci adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací souboru.</span><span class="sxs-lookup"><span data-stu-id="27309-156">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="27309-157">Poskytovatel soubor nemá přístup mimo `applicationRoot` adresáře.</span><span class="sxs-lookup"><span data-stu-id="27309-157">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="27309-158">Ukázková aplikace vytvoří poskytovatel aplikace `Startup.ConfigureServices` pomocí [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span><span class="sxs-lookup"><span data-stu-id="27309-158">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

<span data-ttu-id="27309-159">**Získat typy zprostředkovatele souborů pomocí vkládání závislostí**</span><span class="sxs-lookup"><span data-stu-id="27309-159">**Obtain File Provider types with dependency injection**</span></span>

<span data-ttu-id="27309-160">Vložit do jakéhokoli konstruktoru třídy zprostředkovatele a přiřaďte ho do místního pole.</span><span class="sxs-lookup"><span data-stu-id="27309-160">Inject the provider into any class constructor and assign it to a local field.</span></span> <span data-ttu-id="27309-161">Použijte pole v rámci metod tříd pro přístup k souborům.</span><span class="sxs-lookup"><span data-stu-id="27309-161">Use the field throughout the class's methods to access files.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="27309-162">V ukázkové aplikaci `IndexModel` přijímá třídy `IFileProvider` instance získat obsah adresáře pro základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="27309-162">In the sample app, the `IndexModel` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="27309-163">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="27309-163">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="27309-164">`IDirectoryContents` Jsou vstupní stránky.</span><span class="sxs-lookup"><span data-stu-id="27309-164">The `IDirectoryContents` are iterated in the page.</span></span>

<span data-ttu-id="27309-165">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="27309-165">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="27309-166">V ukázkové aplikaci `HomeController` přijímá třídy `IFileProvider` instance získat obsah adresáře pro základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="27309-166">In the sample app, the `HomeController` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="27309-167">*Controllers/HomeController.cs*:</span><span class="sxs-lookup"><span data-stu-id="27309-167">*Controllers/HomeController.cs*:</span></span>

[!code-csharp[](file-providers/samples/1.x/FileProviderSample/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="27309-168">`IDirectoryContents` Jsou provést iteraci v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="27309-168">The `IDirectoryContents` are iterated in the view.</span></span>

<span data-ttu-id="27309-169">*Views/Home/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="27309-169">*Views/Home/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/1.x/FileProviderSample/Views/Home/Index.cshtml?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="27309-170">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-170">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="27309-171">[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) se používá pro přístup k souborům, které jsou vložené v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="27309-171">The [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="27309-172">`ManifestEmbeddedFileProvider` Manifestu kompilovány do sestavení používá k rekonstrukci původní cesty vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="27309-172">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

> [!NOTE]
> <span data-ttu-id="27309-173">`ManifestEmbeddedFileProvider` Je k dispozici v ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="27309-173">The `ManifestEmbeddedFileProvider` is available in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="27309-174">Přístup k souborům, které jsou součástí sestavení v ASP.NET Core 2.0 nebo starší, najdete v článku [ASP.NET Core 1.x verzi tohoto tématu](/aspnet/core/fundamentals/file-providers?view=aspnetcore-1.1).</span><span class="sxs-lookup"><span data-stu-id="27309-174">To access files embedded in assemblies in ASP.NET Core 2.0 or earlier, see the [ASP.NET Core 1.x version of this topic](/aspnet/core/fundamentals/file-providers?view=aspnetcore-1.1).</span></span>

<span data-ttu-id="27309-175">Chcete-li vygenerovat manifest vložené soubory, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost `true`.</span><span class="sxs-lookup"><span data-stu-id="27309-175">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="27309-176">Zadejte soubory, které chcete vložit s [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="27309-176">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="27309-177">Použití [glob vzory](#glob-patterns) určit jeden nebo více souborů určených pro vložení do sestavení.</span><span class="sxs-lookup"><span data-stu-id="27309-177">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="27309-178">Vytvoří ukázkovou aplikaci `ManifestEmbeddedFileProvider` a předává právě spuštěné sestavení do konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="27309-178">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="27309-179">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="27309-179">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="27309-180">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="27309-180">Additional overloads allow you to:</span></span>

* <span data-ttu-id="27309-181">Zadejte relativní cestou k souboru.</span><span class="sxs-lookup"><span data-stu-id="27309-181">Specify a relative file path.</span></span>
* <span data-ttu-id="27309-182">Obor soubory na datum poslední změny.</span><span class="sxs-lookup"><span data-stu-id="27309-182">Scope files to a last modified date.</span></span>
* <span data-ttu-id="27309-183">Název vloženého zdroje obsahujícího vložený soubor manifestu.</span><span class="sxs-lookup"><span data-stu-id="27309-183">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="27309-184">přetížení</span><span class="sxs-lookup"><span data-stu-id="27309-184">Overload</span></span> | <span data-ttu-id="27309-185">Popis</span><span class="sxs-lookup"><span data-stu-id="27309-185">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="27309-186">ManifestEmbeddedFileProvider (sestavení, String)</span><span class="sxs-lookup"><span data-stu-id="27309-186">ManifestEmbeddedFileProvider(Assembly, String)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | <span data-ttu-id="27309-187">Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="27309-187">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="27309-188">Zadejte `root` do oboru volání [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) k těmto prostředkům na zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="27309-188">Specify the `root` to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided path.</span></span> |
| [<span data-ttu-id="27309-189">ManifestEmbeddedFileProvider (sestavení, řetězec, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="27309-189">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | <span data-ttu-id="27309-190">Přijímá volitelný `root` parametr relativní cesty a `lastModified` datum ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parametru.</span><span class="sxs-lookup"><span data-stu-id="27309-190">Accepts an optional `root` relative path parameter and a `lastModified` date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parameter.</span></span> <span data-ttu-id="27309-191">`lastModified` Obory datum poslední změny pro datum [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instancí vrácených [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="27309-191">The `lastModified` date scopes the last modification date for the [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instances returned by the [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> |
| [<span data-ttu-id="27309-192">ManifestEmbeddedFileProvider (sestavení, String, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="27309-192">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | <span data-ttu-id="27309-193">Přijímá volitelný `root` relativní cesta `lastModified` data, a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="27309-193">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="27309-194">`manifestName` Představuje název vloženého zdroje obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="27309-194">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

### <a name="embeddedfileprovider"></a><span data-ttu-id="27309-195">EmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-195">EmbeddedFileProvider</span></span>

<span data-ttu-id="27309-196">[EmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.embeddedfileprovider) se používá pro přístup k souborům, které jsou vložené v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="27309-196">The [EmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.embeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="27309-197">Zadejte soubory, které chcete vložit [ &lt;EmbeddedResource&gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) vlastnost v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="27309-197">Specify the files to embed with the [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) property in the project file:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="Resource.txt" />
</ItemGroup>
```

<span data-ttu-id="27309-198">Použití [glob vzory](#glob-patterns) určit jeden nebo více souborů určených pro vložení do sestavení.</span><span class="sxs-lookup"><span data-stu-id="27309-198">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="27309-199">Vytvoří ukázkovou aplikaci `EmbeddedFileProvider` a předává právě spuštěné sestavení do konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="27309-199">The sample app creates an `EmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="27309-200">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="27309-200">*Startup.cs*:</span></span>

```csharp
var embeddedProvider = new EmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="27309-201">Vložené prostředky nezveřejňují adresáře.</span><span class="sxs-lookup"><span data-stu-id="27309-201">Embedded resources don't expose directories.</span></span> <span data-ttu-id="27309-202">Místo toho se vloží cestu k prostředku (přes svůj obor názvů) v jeho název souboru pomocí `.` oddělovače.</span><span class="sxs-lookup"><span data-stu-id="27309-202">Rather, the path to the resource (via its namespace) is embedded in its filename using `.` separators.</span></span> <span data-ttu-id="27309-203">V ukázkové aplikaci `baseNamespace` je `FileProviderSample.`.</span><span class="sxs-lookup"><span data-stu-id="27309-203">In the sample app, the `baseNamespace` is `FileProviderSample.`.</span></span>

<span data-ttu-id="27309-204">[EmbeddedFileProvider (sestavení, String)](/dotnet/api/microsoft.extensions.fileproviders.embeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_EmbeddedFileProvider__ctor_System_Reflection_Assembly_) konstruktor přijímá volitelný `baseNamespace` parametru.</span><span class="sxs-lookup"><span data-stu-id="27309-204">The [EmbeddedFileProvider(Assembly, String)](/dotnet/api/microsoft.extensions.fileproviders.embeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_EmbeddedFileProvider__ctor_System_Reflection_Assembly_) constructor accepts an optional `baseNamespace` parameter.</span></span> <span data-ttu-id="27309-205">Zadejte základní obor názvů do oboru volání [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) na tyto prostředky v rámci zadaného oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="27309-205">Specify the base namespace to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided namespace.</span></span>

::: moniker-end

### <a name="compositefileprovider"></a><span data-ttu-id="27309-206">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="27309-206">CompositeFileProvider</span></span>

<span data-ttu-id="27309-207">[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) kombinuje `IFileProvider` instance vystavení jednotné rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="27309-207">The [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="27309-208">Při vytváření `CompositeFileProvider`, předejte jeden nebo více `IFileProvider` instance konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="27309-208">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="27309-209">V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` zadejte soubory `CompositeFileProvider` zaregistrovaný v kontejneru aplikace služby:</span><span class="sxs-lookup"><span data-stu-id="27309-209">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="27309-210">V ukázkové aplikaci `PhysicalFileProvider` a `EmbeddedFileProvider` zadejte soubory `CompositeFileProvider` zaregistrovaný v kontejneru aplikace služby:</span><span class="sxs-lookup"><span data-stu-id="27309-210">In the sample app, a `PhysicalFileProvider` and an `EmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/1.x/FileProviderSample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="watch-for-changes"></a><span data-ttu-id="27309-211">Sledování změn</span><span class="sxs-lookup"><span data-stu-id="27309-211">Watch for changes</span></span>

<span data-ttu-id="27309-212">[IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) metoda poskytuje scénář podívejte se na jeden nebo více souborů nebo adresářů pro změny.</span><span class="sxs-lookup"><span data-stu-id="27309-212">The [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="27309-213">`Watch` přijímá řetězec cesty, které můžete použít [glob vzory](#glob-patterns) zadat více souborů.</span><span class="sxs-lookup"><span data-stu-id="27309-213">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="27309-214">`Watch` Vrátí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span><span class="sxs-lookup"><span data-stu-id="27309-214">`Watch` returns an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span></span> <span data-ttu-id="27309-215">Token zpřístupňuje změny:</span><span class="sxs-lookup"><span data-stu-id="27309-215">The change token exposes:</span></span>

* <span data-ttu-id="27309-216">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): vlastnost, která může být kontrolována k určení, pokud došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="27309-216">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="27309-217">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): volá se, když se zjistí změny na zadané cestě řetězec.</span><span class="sxs-lookup"><span data-stu-id="27309-217">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="27309-218">Každý token změnit jen volá jeho přidružené zpětné volání v reakci na jediné změny.</span><span class="sxs-lookup"><span data-stu-id="27309-218">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="27309-219">Chcete-li povolit konstantní monitorování, použijte [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (viz dole) nebo znovu vytvořit `IChangeToken` instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="27309-219">To enable constant monitoring, use a [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="27309-220">V ukázkové aplikaci *WatchConsole* konzoly aplikace je nakonfigurovaná pro zobrazení zprávy, když se změní textového souboru:</span><span class="sxs-lookup"><span data-stu-id="27309-220">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](file-providers/samples/1.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

::: moniker-end

<span data-ttu-id="27309-221">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky a nemusí spolehlivě posílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="27309-221">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="27309-222">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou prostředí, aby `1` nebo `true` k dotazování systému souborů pro změny každé čtyři sekundy (nejde konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="27309-222">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="27309-223">Vzory glob</span><span class="sxs-lookup"><span data-stu-id="27309-223">Glob patterns</span></span>

<span data-ttu-id="27309-224">Volat jeden vzor zástupných znaků použít systémové cesty k souborům *glob (nebo podpory zástupných znaků) vzory*.</span><span class="sxs-lookup"><span data-stu-id="27309-224">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="27309-225">Tyto vzory zadejte skupiny souborů.</span><span class="sxs-lookup"><span data-stu-id="27309-225">Specify groups of files with these patterns.</span></span> <span data-ttu-id="27309-226">Dva zástupné znaky jsou `*` a `**`:</span><span class="sxs-lookup"><span data-stu-id="27309-226">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="27309-227">Na aktuální úrovni složky, některý název souboru nebo libovolnou příponou odpovídá úplně všechno.</span><span class="sxs-lookup"><span data-stu-id="27309-227">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="27309-228">Shody jsou ukončeny `/` a `.` znaky v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="27309-228">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="27309-229">Napříč několika úrovněmi adresáře odpovídá úplně všechno.</span><span class="sxs-lookup"><span data-stu-id="27309-229">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="27309-230">Můžete použít k rekurzivnímu shodovat s mnoha soubory v hierarchii adresářů.</span><span class="sxs-lookup"><span data-stu-id="27309-230">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="27309-231">**Příklady glob vzor**</span><span class="sxs-lookup"><span data-stu-id="27309-231">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="27309-232">Odpovídá konkrétní soubor v konkrétní adresář.</span><span class="sxs-lookup"><span data-stu-id="27309-232">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="27309-233">Vyhledá všechny soubory s *.txt* rozšíření v konkrétní adresář.</span><span class="sxs-lookup"><span data-stu-id="27309-233">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="27309-234">Odpovídá všem `appsettings.json` soubory v adresářích přesně jednu úroveň níže *directory* složky.</span><span class="sxs-lookup"><span data-stu-id="27309-234">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="27309-235">Vyhledá všechny soubory s *.txt* rozšíření najít kdekoli v rámci *directory* složky.</span><span class="sxs-lookup"><span data-stu-id="27309-235">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>
