---
title: Statické soubory v ASP.NET jádru
author: rick-anderson
description: Naučte se obsluhovat a zabezpečovat statické soubory a konfigurovat statické chování middlewaru ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660123"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="308fc-103">Statické soubory v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="308fc-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="308fc-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a Scott [Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="308fc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="308fc-105">Statické soubory, jako je HTML, CSS, obrázky a JavaScript, jsou datové zdroje, které aplikace ASP.NET Core slouží přímo klientům.</span><span class="sxs-lookup"><span data-stu-id="308fc-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="308fc-106">Některé konfigurace je nutné povolit zobrazování těchto souborů.</span><span class="sxs-lookup"><span data-stu-id="308fc-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="308fc-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="308fc-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="308fc-108">Obsluha statických souborů</span><span class="sxs-lookup"><span data-stu-id="308fc-108">Serve static files</span></span>

<span data-ttu-id="308fc-109">Statické soubory jsou uloženy v [kořenovém](xref:fundamentals/index#web-root) adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="308fc-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="308fc-110">Výchozí adresář je *{content root}/wwwroot*, ale lze jej změnit pomocí metody [UseWebRoot.](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_)</span><span class="sxs-lookup"><span data-stu-id="308fc-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="308fc-111">Další informace naleznete [v tématu Kořenový](xref:fundamentals/index#content-root) obsah a [Kořen webu.](xref:fundamentals/index#web-root)</span><span class="sxs-lookup"><span data-stu-id="308fc-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="308fc-112">Webový hostitel aplikace musí být informován o kořenovém adresáři obsahu.</span><span class="sxs-lookup"><span data-stu-id="308fc-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="308fc-113">Metoda `WebHost.CreateDefaultBuilder` nastaví kořenový obsah do aktuálního adresáře:</span><span class="sxs-lookup"><span data-stu-id="308fc-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="308fc-114">Nastavte kořenový obsah na aktuální adresář vyvoláním [useContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) uvnitř `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="308fc-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="308fc-115">Statické soubory jsou přístupné prostřednictvím cesty vzhledem k [kořenovému adresáři webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="308fc-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="308fc-116">Například šablona projektu **webové aplikace** obsahuje několik složek ve složce *wwwroot:*</span><span class="sxs-lookup"><span data-stu-id="308fc-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="308fc-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="308fc-117">**wwwroot**</span></span>
  * <span data-ttu-id="308fc-118">**Css**</span><span class="sxs-lookup"><span data-stu-id="308fc-118">**css**</span></span>
  * <span data-ttu-id="308fc-119">**Obrázky**</span><span class="sxs-lookup"><span data-stu-id="308fc-119">**images**</span></span>
  * <span data-ttu-id="308fc-120">**Js**</span><span class="sxs-lookup"><span data-stu-id="308fc-120">**js**</span></span>

<span data-ttu-id="308fc-121">Formát URI pro přístup k souboru v podsložce *obrázků* je *http://\<server_address>/images/\<image_file_name>*.</span><span class="sxs-lookup"><span data-stu-id="308fc-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="308fc-122">Například *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="308fc-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="308fc-123">Pokud cílíte na rozhraní .NET Framework, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)</span><span class="sxs-lookup"><span data-stu-id="308fc-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="308fc-124">Pokud cílení .NET Core, [Microsoft.AspNetCore.App metabalíček](xref:fundamentals/metapackage-app) obsahuje tento balíček.</span><span class="sxs-lookup"><span data-stu-id="308fc-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="308fc-125">Pokud cílíte na rozhraní .NET Framework, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)</span><span class="sxs-lookup"><span data-stu-id="308fc-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="308fc-126">Pokud cílení .NET Core, [Microsoft.AspNetCore.All metabalíček](xref:fundamentals/metapackage) obsahuje tento balíček.</span><span class="sxs-lookup"><span data-stu-id="308fc-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="308fc-127">Přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)</span><span class="sxs-lookup"><span data-stu-id="308fc-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="308fc-128">Nakonfigurujte [middleware,](xref:fundamentals/middleware/index) který umožňuje zobrazování statických souborů.</span><span class="sxs-lookup"><span data-stu-id="308fc-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="308fc-129">Obsluhování souborů uvnitř kořenového adresáře webu</span><span class="sxs-lookup"><span data-stu-id="308fc-129">Serve files inside of web root</span></span>

<span data-ttu-id="308fc-130">Vyvolat [metodu UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v rámci `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="308fc-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="308fc-131">Přetížení metody `UseStaticFiles` parameterless označuje soubory v [kořenovém adresáři webu](xref:fundamentals/index#web-root) jako servable.</span><span class="sxs-lookup"><span data-stu-id="308fc-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="308fc-132">Následující odkazy na značky *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="308fc-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="308fc-133">V předchozím kódu znak `~/` vlnovky odkazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="308fc-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="308fc-134">Obsluhování souborů mimo kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="308fc-134">Serve files outside of web root</span></span>

<span data-ttu-id="308fc-135">Zvažte hierarchii adresářů, ve které jsou statické soubory, které mají být podávány, umístěny mimo [kořenový adresář webu](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="308fc-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="308fc-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="308fc-136">**wwwroot**</span></span>
  * <span data-ttu-id="308fc-137">**Css**</span><span class="sxs-lookup"><span data-stu-id="308fc-137">**css**</span></span>
  * <span data-ttu-id="308fc-138">**Obrázky**</span><span class="sxs-lookup"><span data-stu-id="308fc-138">**images**</span></span>
  * <span data-ttu-id="308fc-139">**Js**</span><span class="sxs-lookup"><span data-stu-id="308fc-139">**js**</span></span>
* <span data-ttu-id="308fc-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="308fc-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="308fc-141">**Obrázky**</span><span class="sxs-lookup"><span data-stu-id="308fc-141">**images**</span></span>
    * <span data-ttu-id="308fc-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="308fc-142">*banner1.svg*</span></span>

<span data-ttu-id="308fc-143">Požadavek může přistupovat k souboru *banner1.svg* takto:</span><span class="sxs-lookup"><span data-stu-id="308fc-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="308fc-144">V předchozím kódu je hierarchie adresářů *MyStaticFiles* veřejně vystavena prostřednictvím segmentu *StaticFiles* URI.</span><span class="sxs-lookup"><span data-stu-id="308fc-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="308fc-145">Požadavek na *http://\<server_address>/StaticFiles/images/banner1.svg* slouží souboru *banner1.svg.*</span><span class="sxs-lookup"><span data-stu-id="308fc-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="308fc-146">Následující značky odkazují na *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="308fc-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="308fc-147">Nastavení záhlaví odpovědí HTTP</span><span class="sxs-lookup"><span data-stu-id="308fc-147">Set HTTP response headers</span></span>

<span data-ttu-id="308fc-148">Objekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) lze použít k nastavení hlavičky odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="308fc-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="308fc-149">Kromě konfigurace statického souboru sloužícího z [kořenového adresáře webu](xref:fundamentals/index#web-root)nastaví `Cache-Control` záhlaví následující kód:</span><span class="sxs-lookup"><span data-stu-id="308fc-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="308fc-150">Metoda [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) existuje v balíčku [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)</span><span class="sxs-lookup"><span data-stu-id="308fc-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="308fc-151">Soubory byly veřejně cacheable po dobu 10 minut (600 sekund) ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="308fc-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Byla přidána záhlaví odpovědí zobrazující záhlaví Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="308fc-153">Autorizace statického souboru</span><span class="sxs-lookup"><span data-stu-id="308fc-153">Static file authorization</span></span>

<span data-ttu-id="308fc-154">Middleware statického souboru neposkytuje kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="308fc-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="308fc-155">Všechny soubory, které poskytuje, včetně těch pod *wwwroot*, jsou veřejně přístupné.</span><span class="sxs-lookup"><span data-stu-id="308fc-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="308fc-156">Poskytování souborů na základě autorizace:</span><span class="sxs-lookup"><span data-stu-id="308fc-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="308fc-157">Uložte je mimo *wwwroot* a jakýkoli adresář přístupný pro middleware statického souboru.</span><span class="sxs-lookup"><span data-stu-id="308fc-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="308fc-158">Podávejte je prostřednictvím akční metody, na kterou se vztahuje autorizace.</span><span class="sxs-lookup"><span data-stu-id="308fc-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="308fc-159">Vrátí objekt [FileResult:](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)</span><span class="sxs-lookup"><span data-stu-id="308fc-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="308fc-160">Povolení procházení adresářů</span><span class="sxs-lookup"><span data-stu-id="308fc-160">Enable directory browsing</span></span>

<span data-ttu-id="308fc-161">Procházení adresářů umožňuje uživatelům webové aplikace zobrazit výpis adresáře a soubory v zadaném adresáři.</span><span class="sxs-lookup"><span data-stu-id="308fc-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="308fc-162">Procházení adresářů je ve výchozím nastavení z bezpečnostních důvodů zakázáno (viz [Důležité informace).](#considerations)</span><span class="sxs-lookup"><span data-stu-id="308fc-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="308fc-163">Povolte procházení adresářů vyvoláním metody [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) v : `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="308fc-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="308fc-164">Přidejte požadované služby vyvoláním metody [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) z `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="308fc-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="308fc-165">Předchozí kód umožňuje procházení adresáře složky *wwwroot/images* pomocí url *http://\<server_address>/MyImages*s odkazy na každý soubor a složku:</span><span class="sxs-lookup"><span data-stu-id="308fc-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![procházení adresářů](static-files/_static/dir-browse.png)

<span data-ttu-id="308fc-167">Informace o bezpečnostních rizicích při povolení procházení najdete v [tématu Důležité](#considerations) informace o bezpečnostních rizicích.</span><span class="sxs-lookup"><span data-stu-id="308fc-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="308fc-168">Všimněte `UseStaticFiles` si dvou volání v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="308fc-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="308fc-169">První volání umožňuje zobrazování statických souborů ve složce *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="308fc-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="308fc-170">Druhý hovor umožňuje procházení adresáře složky *wwwroot/images* pomocí adresy URL *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="308fc-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="308fc-171">Obsluha výchozího dokumentu</span><span class="sxs-lookup"><span data-stu-id="308fc-171">Serve a default document</span></span>

<span data-ttu-id="308fc-172">Nastavení výchozí domovské stránky poskytuje návštěvníkům logický výchozí bod při návštěvě webu.</span><span class="sxs-lookup"><span data-stu-id="308fc-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="308fc-173">Chcete-li zostřit výchozí stránku, aniž by uživatel `Startup.Configure`plně kvalifikoval identifikátor URI, zavolejte metodu [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z :</span><span class="sxs-lookup"><span data-stu-id="308fc-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="308fc-174">`UseDefaultFiles`musí být `UseStaticFiles` volána před sloužit výchozí soubor.</span><span class="sxs-lookup"><span data-stu-id="308fc-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="308fc-175">`UseDefaultFiles`je url vypalovačka, která ve skutečnosti neslouží souboru.</span><span class="sxs-lookup"><span data-stu-id="308fc-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="308fc-176">Povolit statické soubor `UseStaticFiles` middleware přes sloužit souboru.</span><span class="sxs-lookup"><span data-stu-id="308fc-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="308fc-177">Pomocí `UseDefaultFiles`aplikace požadavky na složku vyhledávají:</span><span class="sxs-lookup"><span data-stu-id="308fc-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="308fc-178">*Default.htm*</span><span class="sxs-lookup"><span data-stu-id="308fc-178">*default.htm*</span></span>
* <span data-ttu-id="308fc-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="308fc-179">*default.html*</span></span>
* <span data-ttu-id="308fc-180">*Index.htm*</span><span class="sxs-lookup"><span data-stu-id="308fc-180">*index.htm*</span></span>
* <span data-ttu-id="308fc-181">*index.html*</span><span class="sxs-lookup"><span data-stu-id="308fc-181">*index.html*</span></span>

<span data-ttu-id="308fc-182">První soubor nalezený ze seznamu je obsluhován, jako by požadavek byl plně kvalifikovaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="308fc-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="308fc-183">Adresa URL prohlížeče nadále odráží požadovaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="308fc-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="308fc-184">Následující kód změní výchozí název souboru na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="308fc-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="308fc-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="308fc-185">UseFileServer</span></span>

<span data-ttu-id="308fc-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles`aplikace , `UseDefaultFiles`a volitelně `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="308fc-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="308fc-187">Následující kód umožňuje zobrazování statických souborů a výchozího souboru.</span><span class="sxs-lookup"><span data-stu-id="308fc-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="308fc-188">Procházení adresářů není povoleno.</span><span class="sxs-lookup"><span data-stu-id="308fc-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="308fc-189">Následující kód vychází z přetížení bez parametrů povolením procházení adresáře:</span><span class="sxs-lookup"><span data-stu-id="308fc-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="308fc-190">Zvažte následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="308fc-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="308fc-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="308fc-191">**wwwroot**</span></span>
  * <span data-ttu-id="308fc-192">**Css**</span><span class="sxs-lookup"><span data-stu-id="308fc-192">**css**</span></span>
  * <span data-ttu-id="308fc-193">**Obrázky**</span><span class="sxs-lookup"><span data-stu-id="308fc-193">**images**</span></span>
  * <span data-ttu-id="308fc-194">**Js**</span><span class="sxs-lookup"><span data-stu-id="308fc-194">**js**</span></span>
* <span data-ttu-id="308fc-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="308fc-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="308fc-196">**Obrázky**</span><span class="sxs-lookup"><span data-stu-id="308fc-196">**images**</span></span>
    * <span data-ttu-id="308fc-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="308fc-197">*banner1.svg*</span></span>
  * <span data-ttu-id="308fc-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="308fc-198">*default.html*</span></span>

<span data-ttu-id="308fc-199">Následující kód umožňuje statické soubory, výchozí soubory `MyStaticFiles`a procházení adresářů :</span><span class="sxs-lookup"><span data-stu-id="308fc-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="308fc-200">`AddDirectoryBrowser`musí být volána, `true`pokud je hodnota vlastnosti `EnableDirectoryBrowsing` :</span><span class="sxs-lookup"><span data-stu-id="308fc-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="308fc-201">Pomocí hierarchie souborů a předchozího kódu adresy URL řeší následující:</span><span class="sxs-lookup"><span data-stu-id="308fc-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="308fc-202">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="308fc-202">URI</span></span>            |                             <span data-ttu-id="308fc-203">Odpověď</span><span class="sxs-lookup"><span data-stu-id="308fc-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="308fc-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="308fc-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="308fc-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="308fc-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="308fc-206">*http://\<>/StaticFiles server_address*</span><span class="sxs-lookup"><span data-stu-id="308fc-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="308fc-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="308fc-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="308fc-208">Pokud v adresáři *MyStaticFiles* neexistuje žádný soubor s výchozím názvem, *vrátí http:// server_address\<>/StaticFiles* výpis adresáře s odkazy, na který lze kliknout:</span><span class="sxs-lookup"><span data-stu-id="308fc-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Seznam statických souborů](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="308fc-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> proveďte přesměrování na `http://{SERVER ADDRESS}/StaticFiles` straně klienta z (bez koncového lomítka) na `http://{SERVER ADDRESS}/StaticFiles/` (s koncovým lomítkem).</span><span class="sxs-lookup"><span data-stu-id="308fc-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="308fc-211">Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka.</span><span class="sxs-lookup"><span data-stu-id="308fc-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="308fc-212">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="308fc-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="308fc-213">Třída [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) obsahuje `Mappings` vlastnost sloužící jako mapování přípon souborů na typy obsahu MIME.</span><span class="sxs-lookup"><span data-stu-id="308fc-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="308fc-214">V následující ukázce je několik přípon souborů registrováno na známé typy MIME.</span><span class="sxs-lookup"><span data-stu-id="308fc-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="308fc-215">Rozšíření *.rtf* je nahrazeno a *.mp4* je odebráno.</span><span class="sxs-lookup"><span data-stu-id="308fc-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="308fc-216">Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="308fc-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="308fc-217">Nestandardní typy obsahu</span><span class="sxs-lookup"><span data-stu-id="308fc-217">Non-standard content types</span></span>

<span data-ttu-id="308fc-218">Statická soubor Middleware chápe téměř 400 známých typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="308fc-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="308fc-219">Pokud uživatel požaduje soubor s neznámým typem souboru, middleware statického souboru předá požadavek dalšímu middlewaru v kanálu.</span><span class="sxs-lookup"><span data-stu-id="308fc-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="308fc-220">Pokud žádný middleware zpracovává požadavek, je vrácena odpověď *404 Not Found.*</span><span class="sxs-lookup"><span data-stu-id="308fc-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="308fc-221">Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na soubor.</span><span class="sxs-lookup"><span data-stu-id="308fc-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="308fc-222">Následující kód umožňuje zobrazování neznámých typů a vykresluje neznámý soubor jako obrázek:</span><span class="sxs-lookup"><span data-stu-id="308fc-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="308fc-223">S předchozím kódem je jako obrázek vrácen požadavek na soubor s neznámým typem obsahu.</span><span class="sxs-lookup"><span data-stu-id="308fc-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="308fc-224">Povolení [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="308fc-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="308fc-225">Je ve výchozím nastavení zakázána a její použití se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="308fc-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="308fc-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu k zobrazování souborů s nestandardními příponami.</span><span class="sxs-lookup"><span data-stu-id="308fc-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="308fc-227">Obsluhovat soubory z více míst</span><span class="sxs-lookup"><span data-stu-id="308fc-227">Serve files from multiple locations</span></span>

<span data-ttu-id="308fc-228">`UseStaticFiles`a `UseFileServer` výchozí nastavení poskytovatele souborů ukazuje na *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="308fc-228">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="308fc-229">Můžete poskytnout další instance `UseStaticFiles` `UseFileServer` a s dalšími poskytovateli souborů k poskytování souborů z jiných umístění.</span><span class="sxs-lookup"><span data-stu-id="308fc-229">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="308fc-230">Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="308fc-230">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="308fc-231">Požadavky</span><span class="sxs-lookup"><span data-stu-id="308fc-231">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="308fc-232">`UseDirectoryBrowser`a `UseStaticFiles` může prozradit tajemství.</span><span class="sxs-lookup"><span data-stu-id="308fc-232">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="308fc-233">Důrazně doporučujeme zakázat procházení adresářů v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="308fc-233">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="308fc-234">Pečlivě zkontrolujte, které `UseStaticFiles` adresáře jsou povoleny prostřednictvím nebo `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="308fc-234">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="308fc-235">Celý adresář a jeho podadresáře se stanou veřejně přístupnými.</span><span class="sxs-lookup"><span data-stu-id="308fc-235">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="308fc-236">Uklápěte soubory vhodné pro zobrazování veřejnosti ve vyhrazeném adresáři, například \* \<content_root>/wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="308fc-236">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="308fc-237">Oddělit tyto soubory od Zobrazení MVC, Razor Stránky (2.x pouze), konfigurační soubory, atd.</span><span class="sxs-lookup"><span data-stu-id="308fc-237">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="308fc-238">Adresy URL pro obsah `UseDirectoryBrowser` vystavený a `UseStaticFiles` podléhají omezení rozlišování malých a velkých písmen a omezení znaků základního systému souborů.</span><span class="sxs-lookup"><span data-stu-id="308fc-238">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="308fc-239">Například Windows je malá&mdash;a velká písmena macOS a Linux nejsou.</span><span class="sxs-lookup"><span data-stu-id="308fc-239">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="308fc-240">ASP.NET základní aplikace hostované ve službě IIS používají [ASP.NET core modul](xref:host-and-deploy/aspnet-core-module) k předávání všech požadavků do aplikace, včetně požadavků na statické soubory.</span><span class="sxs-lookup"><span data-stu-id="308fc-240">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="308fc-241">Obslužná rutina statického souboru iis se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="308fc-241">The IIS static file handler isn't used.</span></span> <span data-ttu-id="308fc-242">Nemá šanci zpracovat požadavky před jejich zpracováním modulem.</span><span class="sxs-lookup"><span data-stu-id="308fc-242">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="308fc-243">Chcete-li odebrat obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="308fc-243">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="308fc-244">Přejděte na funkci **Moduly.**</span><span class="sxs-lookup"><span data-stu-id="308fc-244">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="308fc-245">V seznamu vyberte **StaticFileModule.**</span><span class="sxs-lookup"><span data-stu-id="308fc-245">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="308fc-246">Na postranním panelu **Akce** klikněte na **Odebrat.**</span><span class="sxs-lookup"><span data-stu-id="308fc-246">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="308fc-247">Pokud je povolena obslužná rutina statického souboru služby IIS **a** ASP.NET je nesprávně nakonfigurován základní modul, jsou obsluhovány statické soubory.</span><span class="sxs-lookup"><span data-stu-id="308fc-247">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="308fc-248">K tomu dochází například v případě, že soubor *web.config* není nasazen.</span><span class="sxs-lookup"><span data-stu-id="308fc-248">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="308fc-249">Umístěte soubory kódu (včetně *.cs* a *.cshtml)* mimo [kořen ový adresář](xref:fundamentals/index#web-root)projektu aplikace .</span><span class="sxs-lookup"><span data-stu-id="308fc-249">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="308fc-250">Proto je vytvořeno logické oddělení mezi obsahem aplikace na straně klienta a kódem založeným na serveru.</span><span class="sxs-lookup"><span data-stu-id="308fc-250">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="308fc-251">Tím zabráníte úniku kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="308fc-251">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="308fc-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="308fc-252">Additional resources</span></span>

* [<span data-ttu-id="308fc-253">Middleware</span><span class="sxs-lookup"><span data-stu-id="308fc-253">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="308fc-254">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="308fc-254">Introduction to ASP.NET Core</span></span>](xref:index)
