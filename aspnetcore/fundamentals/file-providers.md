---
<span data-ttu-id="84585-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-102">'Blazor'</span></span>
- <span data-ttu-id="84585-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-103">'Identity'</span></span>
- <span data-ttu-id="84585-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-105">'Razor'</span></span>
- <span data-ttu-id="84585-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="84585-107">Poskytovatelé souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84585-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="84585-108">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="84585-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84585-109">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="84585-110">Poskytovatelé souborů se používají v rámci ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="84585-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="84585-111">Příklad:</span><span class="sxs-lookup"><span data-stu-id="84585-111">For example:</span></span>

* <span data-ttu-id="84585-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>zpřístupňuje [kořenový adresář obsahu](xref:fundamentals/index#content-root) aplikace a [webové kořenové adresáře](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="84585-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="84585-113">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="84585-114">[Razor](xref:mvc/views/razor)vyhledává stránky a zobrazení pomocí zprostředkovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="84585-115">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="84585-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="84585-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84585-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="84585-117">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="84585-117">File Provider interfaces</span></span>

<span data-ttu-id="84585-118">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="84585-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="84585-119">`IFileProvider`Zpřístupňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="84585-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="84585-120">Získat informace o souboru ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="84585-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="84585-121">Získat informace o adresáři ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).</span><span class="sxs-lookup"><span data-stu-id="84585-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="84585-122">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="84585-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="84585-123">`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="84585-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="84585-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)</span><span class="sxs-lookup"><span data-stu-id="84585-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="84585-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum</span><span class="sxs-lookup"><span data-stu-id="84585-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="84585-126">Můžete číst ze souboru pomocí <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> metody.</span><span class="sxs-lookup"><span data-stu-id="84585-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="84585-127">Ukázková aplikace *FileProviderSample* ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="84585-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="84585-128">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="84585-128">File Provider implementations</span></span>

<span data-ttu-id="84585-129">V následující tabulce jsou uvedeny implementace `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="84585-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="84585-130">Implementace</span><span class="sxs-lookup"><span data-stu-id="84585-130">Implementation</span></span> | <span data-ttu-id="84585-131">Description</span><span class="sxs-lookup"><span data-stu-id="84585-131">Description</span></span> |
| ---
<span data-ttu-id="84585-132">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-133">'Blazor'</span></span>
- <span data-ttu-id="84585-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-134">'Identity'</span></span>
- <span data-ttu-id="84585-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-136">'Razor'</span></span>
- <span data-ttu-id="84585-137">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-138">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-139">'Blazor'</span></span>
- <span data-ttu-id="84585-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-140">'Identity'</span></span>
- <span data-ttu-id="84585-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-142">'Razor'</span></span>
- <span data-ttu-id="84585-143">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-144">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-145">'Blazor'</span></span>
- <span data-ttu-id="84585-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-146">'Identity'</span></span>
- <span data-ttu-id="84585-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-148">'Razor'</span></span>
- <span data-ttu-id="84585-149">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-150">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-151">'Blazor'</span></span>
- <span data-ttu-id="84585-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-152">'Identity'</span></span>
- <span data-ttu-id="84585-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-154">'Razor'</span></span>
- <span data-ttu-id="84585-155">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-156">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-157">'Blazor'</span></span>
- <span data-ttu-id="84585-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-158">'Identity'</span></span>
- <span data-ttu-id="84585-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-160">'Razor'</span></span>
- <span data-ttu-id="84585-161">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-161">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-162">------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-163">'Blazor'</span></span>
- <span data-ttu-id="84585-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-164">'Identity'</span></span>
- <span data-ttu-id="84585-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-166">'Razor'</span></span>
- <span data-ttu-id="84585-167">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-168">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-169">'Blazor'</span></span>
- <span data-ttu-id="84585-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-170">'Identity'</span></span>
- <span data-ttu-id="84585-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-172">'Razor'</span></span>
- <span data-ttu-id="84585-173">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-174">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-175">'Blazor'</span></span>
- <span data-ttu-id="84585-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-176">'Identity'</span></span>
- <span data-ttu-id="84585-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-178">'Razor'</span></span>
- <span data-ttu-id="84585-179">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-179">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-180">------ | | [CompositeFileProvider](#compositefileprovider) | Slouží k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="84585-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="84585-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Používá se pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="84585-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="84585-182">| | [PhysicalFileProvider](#physicalfileprovider) | Používá se pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="84585-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="84585-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="84585-183">PhysicalFileProvider</span></span>

<span data-ttu-id="84585-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="84585-185">`PhysicalFileProvider`používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="84585-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="84585-186">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="84585-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="84585-187">Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro vyžádání `IFileProvider` v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="84585-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="84585-188">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje absolutní cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="84585-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="84585-189">Glob vzory se v cestě k adresáři nepodporují.</span><span class="sxs-lookup"><span data-stu-id="84585-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="84585-190">Následující kód ukazuje, jak použít `PhysicalFileProvider` k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="84585-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="84585-191">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="84585-191">Types in the preceding example:</span></span>

* <span data-ttu-id="84585-192">`provider`je `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="84585-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="84585-193">`contents`je `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="84585-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="84585-194">`fileInfo`je `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="84585-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="84585-195">Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="84585-196">Do metody nelze předat vzory glob `GetFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="84585-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="84585-197">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="84585-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="84585-198">Ukázková aplikace *FileProviderSample* vytvoří zprostředkovatele v `Startup.ConfigureServices` metodě pomocí <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="84585-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="84585-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="84585-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="84585-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>Slouží k přístupu k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="84585-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="84585-201">`ManifestEmbeddedFileProvider`Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="84585-202">Chcete-li vygenerovat manifest vložených souborů:</span><span class="sxs-lookup"><span data-stu-id="84585-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="84585-203">Přidejte do projektu balíček [Microsoft. Extensions. Providers. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet.</span><span class="sxs-lookup"><span data-stu-id="84585-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="84585-204">Nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="84585-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="84585-205">Zadejte soubory, které se mají vložit [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="84585-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="84585-206">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="84585-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="84585-207">Ukázková aplikace *FileProviderSample* vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="84585-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="84585-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="84585-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="84585-209">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="84585-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="84585-210">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-210">Specify a relative file path.</span></span>
* <span data-ttu-id="84585-211">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="84585-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="84585-212">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="84585-213">Metody</span><span class="sxs-lookup"><span data-stu-id="84585-213">Overload</span></span> | <span data-ttu-id="84585-214">Description</span><span class="sxs-lookup"><span data-stu-id="84585-214">Description</span></span> |
| ---
<span data-ttu-id="84585-215">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-216">'Blazor'</span></span>
- <span data-ttu-id="84585-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-217">'Identity'</span></span>
- <span data-ttu-id="84585-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-219">'Razor'</span></span>
- <span data-ttu-id="84585-220">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-221">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-222">'Blazor'</span></span>
- <span data-ttu-id="84585-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-223">'Identity'</span></span>
- <span data-ttu-id="84585-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-225">'Razor'</span></span>
- <span data-ttu-id="84585-226">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-226">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-227">---- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-228">'Blazor'</span></span>
- <span data-ttu-id="84585-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-229">'Identity'</span></span>
- <span data-ttu-id="84585-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-231">'Razor'</span></span>
- <span data-ttu-id="84585-232">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-233">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-234">'Blazor'</span></span>
- <span data-ttu-id="84585-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-235">'Identity'</span></span>
- <span data-ttu-id="84585-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-237">'Razor'</span></span>
- <span data-ttu-id="84585-238">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-239">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-240">'Blazor'</span></span>
- <span data-ttu-id="84585-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-241">'Identity'</span></span>
- <span data-ttu-id="84585-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-243">'Razor'</span></span>
- <span data-ttu-id="84585-244">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-244">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="84585-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="84585-246">Zadejte `root` obor pro volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> těchto prostředků v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="84585-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="84585-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný `root` parametr relativní cesty a `lastModified` parametr data ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="84585-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="84585-248">`lastModified`Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="84585-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="84585-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` Datum a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="84585-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="84585-250">`manifestName`Představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="84585-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="84585-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="84585-251">CompositeFileProvider</span></span>

<span data-ttu-id="84585-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Kombinuje `IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="84585-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="84585-253">Při vytváření `CompositeFileProvider` , předejte do konstruktoru jednu nebo více `IFileProvider` instancí.</span><span class="sxs-lookup"><span data-stu-id="84585-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="84585-254">V ukázkové aplikaci *FileProviderSample* `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytuje soubory `CompositeFileProvider` registrované v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="84585-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="84585-255">Následující kód naleznete v `Startup.ConfigureServices` metodě projektu:</span><span class="sxs-lookup"><span data-stu-id="84585-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="84585-256">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="84585-256">Watch for changes</span></span>

<span data-ttu-id="84585-257"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>Metoda poskytuje scénář pro sledování změn jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="84585-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="84585-258">`Watch`Metoda:</span><span class="sxs-lookup"><span data-stu-id="84585-258">The `Watch` method:</span></span>

* <span data-ttu-id="84585-259">Přijímá řetězec cesty k souboru, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="84585-260">Vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="84585-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="84585-261">Výsledný token změny zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="84585-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="84585-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="84585-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="84585-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Volá se, když se v zadaném řetězci cesty zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="84585-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="84585-264">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="84585-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="84585-265">Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (viz níže) nebo znovu vytvořte `IChangeToken` instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="84585-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="84585-266">Ukázková aplikace *WatchConsole* zapisuje zprávu vždy, když se upraví soubor *. txt* v adresáři *TextFiles* :</span><span class="sxs-lookup"><span data-stu-id="84585-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="84585-267">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="84585-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="84585-268">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou prostředí na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="84585-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="84585-269">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="84585-269">Glob patterns</span></span>

<span data-ttu-id="84585-270">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)*.</span><span class="sxs-lookup"><span data-stu-id="84585-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="84585-271">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="84585-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="84585-272">Dva zástupné znaky jsou `*` a `**` :</span><span class="sxs-lookup"><span data-stu-id="84585-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="84585-273">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="84585-274">Shody jsou zakončeny `/` `.` znaky a v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="84585-275">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="84585-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="84585-276">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="84585-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="84585-277">Následující tabulka uvádí běžné příklady glob vzorů.</span><span class="sxs-lookup"><span data-stu-id="84585-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="84585-278">Vzor</span><span class="sxs-lookup"><span data-stu-id="84585-278">Pattern</span></span>  |<span data-ttu-id="84585-279">Description</span><span class="sxs-lookup"><span data-stu-id="84585-279">Description</span></span>  |
|---
<span data-ttu-id="84585-280">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-281">'Blazor'</span></span>
- <span data-ttu-id="84585-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-282">'Identity'</span></span>
- <span data-ttu-id="84585-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-284">'Razor'</span></span>
- <span data-ttu-id="84585-285">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-286">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-287">'Blazor'</span></span>
- <span data-ttu-id="84585-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-288">'Identity'</span></span>
- <span data-ttu-id="84585-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-290">'Razor'</span></span>
- <span data-ttu-id="84585-291">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-291">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-292">-----|---
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-293">'Blazor'</span></span>
- <span data-ttu-id="84585-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-294">'Identity'</span></span>
- <span data-ttu-id="84585-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-296">'Razor'</span></span>
- <span data-ttu-id="84585-297">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-298">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-299">'Blazor'</span></span>
- <span data-ttu-id="84585-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-300">'Identity'</span></span>
- <span data-ttu-id="84585-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-302">'Razor'</span></span>
- <span data-ttu-id="84585-303">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-303">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-304">-----|
|`directory/file.txt`| Odpovídá konkrétnímu souboru v konkrétním adresáři. | |`directory/*.txt`| Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři. | |`directory/*/appsettings.json`| Vyhledá všechny soubory *appSettings. JSON* v adresářích přesně jednu úroveň pod *adresářovou* složkou. | |`directory/**/*.txt`| Porovná všechny soubory s příponou *. txt* nalezené kdekoli v rámci složky *adresáře* . |</span><span class="sxs-lookup"><span data-stu-id="84585-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84585-305">ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="84585-306">Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:</span><span class="sxs-lookup"><span data-stu-id="84585-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="84585-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zpřístupňuje [kořenový adresář obsahu](xref:fundamentals/index#content-root) aplikace a [webové kořenové adresáře](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="84585-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="84585-308">[Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="84585-309">[Razor](xref:mvc/views/razor)vyhledává stránky a zobrazení pomocí zprostředkovatelů souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="84585-310">Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.</span><span class="sxs-lookup"><span data-stu-id="84585-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="84585-311">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84585-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="84585-312">Rozhraní poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="84585-312">File Provider interfaces</span></span>

<span data-ttu-id="84585-313">Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="84585-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="84585-314">`IFileProvider`Zpřístupňuje metody pro:</span><span class="sxs-lookup"><span data-stu-id="84585-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="84585-315">Získat informace o souboru ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="84585-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="84585-316">Získat informace o adresáři ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).</span><span class="sxs-lookup"><span data-stu-id="84585-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="84585-317">Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="84585-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="84585-318">`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:</span><span class="sxs-lookup"><span data-stu-id="84585-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="84585-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)</span><span class="sxs-lookup"><span data-stu-id="84585-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="84585-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum</span><span class="sxs-lookup"><span data-stu-id="84585-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="84585-321">Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="84585-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="84585-322">Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="84585-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="84585-323">Implementace poskytovatele souborů</span><span class="sxs-lookup"><span data-stu-id="84585-323">File Provider implementations</span></span>

<span data-ttu-id="84585-324">`IFileProvider`K dispozici jsou tři implementace systému.</span><span class="sxs-lookup"><span data-stu-id="84585-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="84585-325">Implementace</span><span class="sxs-lookup"><span data-stu-id="84585-325">Implementation</span></span> | <span data-ttu-id="84585-326">Description</span><span class="sxs-lookup"><span data-stu-id="84585-326">Description</span></span> |
| ---
<span data-ttu-id="84585-327">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-328">'Blazor'</span></span>
- <span data-ttu-id="84585-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-329">'Identity'</span></span>
- <span data-ttu-id="84585-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-331">'Razor'</span></span>
- <span data-ttu-id="84585-332">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-333">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-334">'Blazor'</span></span>
- <span data-ttu-id="84585-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-335">'Identity'</span></span>
- <span data-ttu-id="84585-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-337">'Razor'</span></span>
- <span data-ttu-id="84585-338">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-339">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-340">'Blazor'</span></span>
- <span data-ttu-id="84585-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-341">'Identity'</span></span>
- <span data-ttu-id="84585-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-343">'Razor'</span></span>
- <span data-ttu-id="84585-344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-345">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-346">'Blazor'</span></span>
- <span data-ttu-id="84585-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-347">'Identity'</span></span>
- <span data-ttu-id="84585-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-349">'Razor'</span></span>
- <span data-ttu-id="84585-350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-351">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-352">'Blazor'</span></span>
- <span data-ttu-id="84585-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-353">'Identity'</span></span>
- <span data-ttu-id="84585-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-355">'Razor'</span></span>
- <span data-ttu-id="84585-356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-356">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-357">------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-358">'Blazor'</span></span>
- <span data-ttu-id="84585-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-359">'Identity'</span></span>
- <span data-ttu-id="84585-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-361">'Razor'</span></span>
- <span data-ttu-id="84585-362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-363">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-364">'Blazor'</span></span>
- <span data-ttu-id="84585-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-365">'Identity'</span></span>
- <span data-ttu-id="84585-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-367">'Razor'</span></span>
- <span data-ttu-id="84585-368">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-369">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-370">'Blazor'</span></span>
- <span data-ttu-id="84585-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-371">'Identity'</span></span>
- <span data-ttu-id="84585-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-373">'Razor'</span></span>
- <span data-ttu-id="84585-374">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-374">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému.</span><span class="sxs-lookup"><span data-stu-id="84585-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="84585-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních.</span><span class="sxs-lookup"><span data-stu-id="84585-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="84585-377">| | [CompositeFileProvider](#compositefileprovider) | Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="84585-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="84585-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="84585-378">PhysicalFileProvider</span></span>

<span data-ttu-id="84585-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Poskytuje přístup k fyzickému systému souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="84585-380">`PhysicalFileProvider`používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám.</span><span class="sxs-lookup"><span data-stu-id="84585-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="84585-381">Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky.</span><span class="sxs-lookup"><span data-stu-id="84585-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="84585-382">Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro vyžádání `IFileProvider` v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="84585-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="84585-383">Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="84585-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="84585-384">Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:</span><span class="sxs-lookup"><span data-stu-id="84585-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="84585-385">Typy v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="84585-385">Types in the preceding example:</span></span>

* <span data-ttu-id="84585-386">`provider`je `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="84585-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="84585-387">`contents`je `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="84585-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="84585-388">`fileInfo`je `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="84585-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="84585-389">Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="84585-390">Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.</span><span class="sxs-lookup"><span data-stu-id="84585-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="84585-391">Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="84585-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="84585-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="84585-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="84585-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>Slouží k přístupu k souborům integrovaným v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="84585-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="84585-394">`ManifestEmbeddedFileProvider`Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="84585-395">Chcete-li vygenerovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="84585-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="84585-396">Zadejte soubory, které se mají vložit do [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="84585-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="84585-397">Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.</span><span class="sxs-lookup"><span data-stu-id="84585-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="84585-398">Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="84585-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="84585-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="84585-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="84585-400">Další přetížení umožňují:</span><span class="sxs-lookup"><span data-stu-id="84585-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="84585-401">Zadejte relativní cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-401">Specify a relative file path.</span></span>
* <span data-ttu-id="84585-402">Umožňuje nastavit rozsah souborů na datum poslední úpravy.</span><span class="sxs-lookup"><span data-stu-id="84585-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="84585-403">Pojmenujte vložený prostředek obsahující manifest vloženého souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="84585-404">Metody</span><span class="sxs-lookup"><span data-stu-id="84585-404">Overload</span></span> | <span data-ttu-id="84585-405">Description</span><span class="sxs-lookup"><span data-stu-id="84585-405">Description</span></span> |
| ---
<span data-ttu-id="84585-406">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-407">'Blazor'</span></span>
- <span data-ttu-id="84585-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-408">'Identity'</span></span>
- <span data-ttu-id="84585-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-410">'Razor'</span></span>
- <span data-ttu-id="84585-411">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-412">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-413">'Blazor'</span></span>
- <span data-ttu-id="84585-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-414">'Identity'</span></span>
- <span data-ttu-id="84585-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-416">'Razor'</span></span>
- <span data-ttu-id="84585-417">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-417">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-418">---- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-419">'Blazor'</span></span>
- <span data-ttu-id="84585-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-420">'Identity'</span></span>
- <span data-ttu-id="84585-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-422">'Razor'</span></span>
- <span data-ttu-id="84585-423">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-424">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-425">'Blazor'</span></span>
- <span data-ttu-id="84585-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-426">'Identity'</span></span>
- <span data-ttu-id="84585-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-428">'Razor'</span></span>
- <span data-ttu-id="84585-429">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="84585-430">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="84585-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84585-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84585-431">'Blazor'</span></span>
- <span data-ttu-id="84585-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84585-432">'Identity'</span></span>
- <span data-ttu-id="84585-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84585-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="84585-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84585-434">'Razor'</span></span>
- <span data-ttu-id="84585-435">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="84585-435">'SignalR' uid:</span></span> 

<span data-ttu-id="84585-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný `root` parametr relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="84585-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="84585-437">Zadejte `root` obor pro volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> těchto prostředků v zadané cestě.</span><span class="sxs-lookup"><span data-stu-id="84585-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="84585-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný `root` parametr relativní cesty a `lastModified` parametr data ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="84585-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="84585-439">`lastModified`Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="84585-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="84585-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` Datum a `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="84585-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="84585-441">`manifestName`Představuje název vloženého prostředku obsahujícího manifest.</span><span class="sxs-lookup"><span data-stu-id="84585-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="84585-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="84585-442">CompositeFileProvider</span></span>

<span data-ttu-id="84585-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Kombinuje `IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů.</span><span class="sxs-lookup"><span data-stu-id="84585-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="84585-444">Při vytváření `CompositeFileProvider` , předejte do konstruktoru jednu nebo více `IFileProvider` instancí.</span><span class="sxs-lookup"><span data-stu-id="84585-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="84585-445">V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="84585-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="84585-446">Sledovat změny</span><span class="sxs-lookup"><span data-stu-id="84585-446">Watch for changes</span></span>

<span data-ttu-id="84585-447">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů.</span><span class="sxs-lookup"><span data-stu-id="84585-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="84585-448">`Watch`přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů.</span><span class="sxs-lookup"><span data-stu-id="84585-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="84585-449">`Watch`Vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="84585-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="84585-450">Token pro změnu zpřístupňuje:</span><span class="sxs-lookup"><span data-stu-id="84585-450">The change token exposes:</span></span>

* <span data-ttu-id="84585-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.</span><span class="sxs-lookup"><span data-stu-id="84585-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="84585-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Volá se, když se v zadaném řetězci cesty zjistí změny.</span><span class="sxs-lookup"><span data-stu-id="84585-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="84585-453">Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu.</span><span class="sxs-lookup"><span data-stu-id="84585-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="84585-454">Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (viz níže) nebo znovu vytvořte `IChangeToken` instance v reakci na změny.</span><span class="sxs-lookup"><span data-stu-id="84585-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="84585-455">V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:</span><span class="sxs-lookup"><span data-stu-id="84585-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="84585-456">Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách.</span><span class="sxs-lookup"><span data-stu-id="84585-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="84585-457">Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou prostředí na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).</span><span class="sxs-lookup"><span data-stu-id="84585-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="84585-458">Glob vzory</span><span class="sxs-lookup"><span data-stu-id="84585-458">Glob patterns</span></span>

<span data-ttu-id="84585-459">Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)*.</span><span class="sxs-lookup"><span data-stu-id="84585-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="84585-460">Určete skupiny souborů s těmito vzory.</span><span class="sxs-lookup"><span data-stu-id="84585-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="84585-461">Dva zástupné znaky jsou `*` a `**` :</span><span class="sxs-lookup"><span data-stu-id="84585-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="84585-462">Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="84585-463">Shody jsou zakončeny `/` `.` znaky a v cestě k souboru.</span><span class="sxs-lookup"><span data-stu-id="84585-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="84585-464">Odpovídá cokoli v různých úrovních adresáře.</span><span class="sxs-lookup"><span data-stu-id="84585-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="84585-465">Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.</span><span class="sxs-lookup"><span data-stu-id="84585-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="84585-466">**Příklady vzorů glob**</span><span class="sxs-lookup"><span data-stu-id="84585-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="84585-467">Odpovídá konkrétnímu souboru v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="84585-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="84585-468">Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.</span><span class="sxs-lookup"><span data-stu-id="84585-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="84585-469">Vyhledá všechny `appsettings.json` soubory v adresářích přesně jednu úroveň pod *adresářovou* složkou.</span><span class="sxs-lookup"><span data-stu-id="84585-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="84585-470">Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .</span><span class="sxs-lookup"><span data-stu-id="84585-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
