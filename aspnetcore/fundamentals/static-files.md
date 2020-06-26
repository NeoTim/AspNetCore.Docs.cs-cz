---
title: Statické soubory v ASP.NET Core
author: rick-anderson
description: Naučte se zajišťovat a zabezpečovat statické soubory a konfigurovat statický soubor hostující chování middlewaru ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 33b4d43471932ed5e9e328163672028b00e6b5b8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399475"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="59eb8-103">Statické soubory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59eb8-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="59eb8-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="59eb8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="59eb8-105">Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou prostředky, které ASP.NET Core aplikace slouží přímo klientům.</span><span class="sxs-lookup"><span data-stu-id="59eb8-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="59eb8-106">K povolení obsluhy těchto souborů je potřeba některá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="59eb8-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="59eb8-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59eb8-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="59eb8-108">Obsluhovat statické soubory</span><span class="sxs-lookup"><span data-stu-id="59eb8-108">Serve static files</span></span>

<span data-ttu-id="59eb8-109">Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="59eb8-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="59eb8-110">Výchozí adresář je *{root Content}/wwwroot*, ale dá se změnit prostřednictvím metody [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) .</span><span class="sxs-lookup"><span data-stu-id="59eb8-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="59eb8-111">Další informace najdete v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) a ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) .</span><span class="sxs-lookup"><span data-stu-id="59eb8-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="59eb8-112">Webový hostitel aplikace musí být informován o kořenovém adresáři obsahu.</span><span class="sxs-lookup"><span data-stu-id="59eb8-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="59eb8-113">`WebHost.CreateDefaultBuilder`Metoda nastaví kořen obsahu na aktuální adresář:</span><span class="sxs-lookup"><span data-stu-id="59eb8-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="59eb8-114">Nastavte kořen obsahu na aktuální adresář vyvoláním [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) uvnitř `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="59eb8-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="59eb8-115">Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="59eb8-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="59eb8-116">Například šablona projektu **webové aplikace** obsahuje několik složek ve složce *wwwroot* :</span><span class="sxs-lookup"><span data-stu-id="59eb8-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="59eb8-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="59eb8-117">**wwwroot**</span></span>
  * <span data-ttu-id="59eb8-118">**funkcí**</span><span class="sxs-lookup"><span data-stu-id="59eb8-118">**css**</span></span>
  * <span data-ttu-id="59eb8-119">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="59eb8-119">**images**</span></span>
  * <span data-ttu-id="59eb8-120">**js**</span><span class="sxs-lookup"><span data-stu-id="59eb8-120">**js**</span></span>

<span data-ttu-id="59eb8-121">Formát identifikátoru URI pro přístup k souboru v podsložce *obrázky* je \*http:// \<server_address> /images/ \<image_file_name> \*.</span><span class="sxs-lookup"><span data-stu-id="59eb8-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="59eb8-122">Například *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="59eb8-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="59eb8-123">Pokud cílíte .NET Framework, přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="59eb8-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="59eb8-124">Při cílení na .NET Core obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) tento balíček.</span><span class="sxs-lookup"><span data-stu-id="59eb8-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="59eb8-125">Pokud cílíte .NET Framework, přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="59eb8-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="59eb8-126">Při cílení na rozhraní .NET Core obsahuje soubor [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage) tento balíček.</span><span class="sxs-lookup"><span data-stu-id="59eb8-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="59eb8-127">Přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="59eb8-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="59eb8-128">Nakonfigurujte [middleware](xref:fundamentals/middleware/index) , který umožňuje obsluhu statických souborů.</span><span class="sxs-lookup"><span data-stu-id="59eb8-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="59eb8-129">Obsluhovat soubory uvnitř webového kořenového adresáře</span><span class="sxs-lookup"><span data-stu-id="59eb8-129">Serve files inside of web root</span></span>

<span data-ttu-id="59eb8-130">Vyvolat metodu [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v rámci `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="59eb8-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="59eb8-131">`UseStaticFiles`Přetížení metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé.</span><span class="sxs-lookup"><span data-stu-id="59eb8-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="59eb8-132">Následující značky odkazují na *wwwroot/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="59eb8-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="59eb8-133">V předchozím kódu znak tildy `~/` ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="59eb8-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="59eb8-134">Obsluhovat soubory mimo web root</span><span class="sxs-lookup"><span data-stu-id="59eb8-134">Serve files outside of web root</span></span>

<span data-ttu-id="59eb8-135">Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="59eb8-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="59eb8-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="59eb8-136">**wwwroot**</span></span>
  * <span data-ttu-id="59eb8-137">**funkcí**</span><span class="sxs-lookup"><span data-stu-id="59eb8-137">**css**</span></span>
  * <span data-ttu-id="59eb8-138">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="59eb8-138">**images**</span></span>
  * <span data-ttu-id="59eb8-139">**js**</span><span class="sxs-lookup"><span data-stu-id="59eb8-139">**js**</span></span>
* <span data-ttu-id="59eb8-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="59eb8-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="59eb8-141">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="59eb8-141">**images**</span></span>
    * <span data-ttu-id="59eb8-142">*banner1. SVG*</span><span class="sxs-lookup"><span data-stu-id="59eb8-142">*banner1.svg*</span></span>

<span data-ttu-id="59eb8-143">Požadavek může získat přístup k souboru *banner1. SVG* nakonfigurováním middleware statického souboru následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="59eb8-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="59eb8-144">V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="59eb8-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="59eb8-145">Požadavek na *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsluhuje soubor *banner1. SVG* .</span><span class="sxs-lookup"><span data-stu-id="59eb8-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="59eb8-146">Následující značky odkazují na *MyStaticFiles/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="59eb8-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="59eb8-147">Nastavit hlavičky HTTP odpovědi</span><span class="sxs-lookup"><span data-stu-id="59eb8-147">Set HTTP response headers</span></span>

<span data-ttu-id="59eb8-148">K nastavení hlaviček odpovědí HTTP lze použít objekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) .</span><span class="sxs-lookup"><span data-stu-id="59eb8-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="59eb8-149">Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:</span><span class="sxs-lookup"><span data-stu-id="59eb8-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="59eb8-150">V balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) existuje metoda [HeaderDictionaryExtensions. Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) .</span><span class="sxs-lookup"><span data-stu-id="59eb8-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="59eb8-151">Soubory byly ve vývojovém prostředí veřejně uložené do mezipaměti po dobu 10 minut (600 sekund):</span><span class="sxs-lookup"><span data-stu-id="59eb8-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="59eb8-153">Autorizace statického souboru</span><span class="sxs-lookup"><span data-stu-id="59eb8-153">Static file authorization</span></span>

<span data-ttu-id="59eb8-154">Middleware statických souborů neposkytuje kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="59eb8-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="59eb8-155">Všechny soubory, které obsluhuje, včetně názvů *wwwroot*, jsou veřejně přístupné.</span><span class="sxs-lookup"><span data-stu-id="59eb8-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="59eb8-156">Pro obsluhu souborů na základě autorizace:</span><span class="sxs-lookup"><span data-stu-id="59eb8-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="59eb8-157">Uložte je mimo *wwwroot* a jakýkoliv adresář přístupný pro middleware statických souborů.</span><span class="sxs-lookup"><span data-stu-id="59eb8-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="59eb8-158">Obsluhuje je pomocí metody akce, na které se autorizace používá.</span><span class="sxs-lookup"><span data-stu-id="59eb8-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="59eb8-159">Vrátit objekt [výsledku](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) objektu:</span><span class="sxs-lookup"><span data-stu-id="59eb8-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="59eb8-160">Povolit procházení adresářů</span><span class="sxs-lookup"><span data-stu-id="59eb8-160">Enable directory browsing</span></span>

<span data-ttu-id="59eb8-161">Procházení adresářů umožňuje uživatelům vaší webové aplikace zobrazit v zadaném adresáři výpis adresářů a soubory.</span><span class="sxs-lookup"><span data-stu-id="59eb8-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="59eb8-162">Procházení adresářů je ve výchozím nastavení zakázáno z důvodů zabezpečení (viz téma [informace](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="59eb8-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="59eb8-163">Povolit procházení adresářů vyvoláním metody [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="59eb8-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="59eb8-164">Přidejte požadované služby vyvoláním metody [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) z `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="59eb8-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="59eb8-165">Předchozí kód umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*s odkazy na jednotlivé soubory a složky:</span><span class="sxs-lookup"><span data-stu-id="59eb8-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![procházení adresářů](static-files/_static/dir-browse.png)

<span data-ttu-id="59eb8-167">Při povolování procházení si přečtěte [informace](#considerations) o bezpečnostních rizicích.</span><span class="sxs-lookup"><span data-stu-id="59eb8-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="59eb8-168">Všimněte si dvou `UseStaticFiles` volání v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="59eb8-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="59eb8-169">První volání povoluje obsluhu statických souborů ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="59eb8-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="59eb8-170">Druhé volání umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*:</span><span class="sxs-lookup"><span data-stu-id="59eb8-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="59eb8-171">Slouží jako výchozí dokument.</span><span class="sxs-lookup"><span data-stu-id="59eb8-171">Serve a default document</span></span>

<span data-ttu-id="59eb8-172">Nastavení výchozí domovské stránky poskytuje návštěvníkům logický výchozí bod při návštěvě vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="59eb8-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="59eb8-173">Chcete-li obsloužit výchozí stránku bez toho, aby uživatel plně kvalifikován identifikátor URI, zavolejte metodu [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="59eb8-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="59eb8-174">`UseDefaultFiles`musí být volána předtím, než `UseStaticFiles` bude sloužit jako výchozí soubor.</span><span class="sxs-lookup"><span data-stu-id="59eb8-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="59eb8-175">`UseDefaultFiles`je přepis adresy URL, který soubor ve skutečnosti neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="59eb8-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="59eb8-176">Povolte middleware statických souborů prostřednictvím nástroje `UseStaticFiles` za účelem obsluhy souboru.</span><span class="sxs-lookup"><span data-stu-id="59eb8-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="59eb8-177">S `UseDefaultFiles` , požadavky na hledání složky pro:</span><span class="sxs-lookup"><span data-stu-id="59eb8-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="59eb8-178">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="59eb8-178">*default.htm*</span></span>
* <span data-ttu-id="59eb8-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="59eb8-179">*default.html*</span></span>
* <span data-ttu-id="59eb8-180">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="59eb8-180">*index.htm*</span></span>
* <span data-ttu-id="59eb8-181">*index.html*</span><span class="sxs-lookup"><span data-stu-id="59eb8-181">*index.html*</span></span>

<span data-ttu-id="59eb8-182">První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="59eb8-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="59eb8-183">Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="59eb8-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="59eb8-184">Následující kód změní výchozí název souboru na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="59eb8-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="59eb8-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="59eb8-185">UseFileServer</span></span>

<span data-ttu-id="59eb8-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles` , `UseDefaultFiles` a volitelně `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="59eb8-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="59eb8-187">Následující kód povoluje obsluhu statických souborů a výchozího souboru.</span><span class="sxs-lookup"><span data-stu-id="59eb8-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="59eb8-188">Procházení adresářů není povoleno.</span><span class="sxs-lookup"><span data-stu-id="59eb8-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="59eb8-189">Následující kód je sestaven po přetěžování bez parametrů povolením procházení adresářů:</span><span class="sxs-lookup"><span data-stu-id="59eb8-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="59eb8-190">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="59eb8-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="59eb8-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="59eb8-191">**wwwroot**</span></span>
  * <span data-ttu-id="59eb8-192">**funkcí**</span><span class="sxs-lookup"><span data-stu-id="59eb8-192">**css**</span></span>
  * <span data-ttu-id="59eb8-193">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="59eb8-193">**images**</span></span>
  * <span data-ttu-id="59eb8-194">**js**</span><span class="sxs-lookup"><span data-stu-id="59eb8-194">**js**</span></span>
* <span data-ttu-id="59eb8-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="59eb8-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="59eb8-196">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="59eb8-196">**images**</span></span>
    * <span data-ttu-id="59eb8-197">*banner1. SVG*</span><span class="sxs-lookup"><span data-stu-id="59eb8-197">*banner1.svg*</span></span>
  * <span data-ttu-id="59eb8-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="59eb8-198">*default.html*</span></span>

<span data-ttu-id="59eb8-199">Následující kód povoluje statické soubory, výchozí soubory a procházení adresářů pro `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="59eb8-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="59eb8-200">`AddDirectoryBrowser`musí být volána, pokud `EnableDirectoryBrowsing` je hodnota vlastnosti `true` :</span><span class="sxs-lookup"><span data-stu-id="59eb8-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="59eb8-201">Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="59eb8-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="59eb8-202">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="59eb8-202">URI</span></span>            |                             <span data-ttu-id="59eb8-203">Odpověď</span><span class="sxs-lookup"><span data-stu-id="59eb8-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="59eb8-204">*http:// \<server_address> /StaticFiles/images/banner1.SVG*</span><span class="sxs-lookup"><span data-stu-id="59eb8-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="59eb8-205">MyStaticFiles/images/banner1. SVG</span><span class="sxs-lookup"><span data-stu-id="59eb8-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="59eb8-206">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="59eb8-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="59eb8-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="59eb8-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="59eb8-208">Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, *http:// \<server_address> /StaticFiles* vrátí výpis adresáře s odkazy kliknutím:</span><span class="sxs-lookup"><span data-stu-id="59eb8-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Seznam statických souborů](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="59eb8-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez koncového lomítka) na `http://{SERVER ADDRESS}/StaticFiles/` (s koncovým lomítkem).</span><span class="sxs-lookup"><span data-stu-id="59eb8-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="59eb8-211">Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka.</span><span class="sxs-lookup"><span data-stu-id="59eb8-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="59eb8-212">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="59eb8-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="59eb8-213">Třída [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME.</span><span class="sxs-lookup"><span data-stu-id="59eb8-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="59eb8-214">V následující ukázce jsou pro známé typy MIME zaregistrovány několik přípon souborů.</span><span class="sxs-lookup"><span data-stu-id="59eb8-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="59eb8-215">Přípona *. RTF* je nahrazena a *. mp4* je odebráno.</span><span class="sxs-lookup"><span data-stu-id="59eb8-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="59eb8-216">Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="59eb8-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="59eb8-217">Nestandardní typy obsahu</span><span class="sxs-lookup"><span data-stu-id="59eb8-217">Non-standard content types</span></span>

<span data-ttu-id="59eb8-218">Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="59eb8-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="59eb8-219">Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu.</span><span class="sxs-lookup"><span data-stu-id="59eb8-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="59eb8-220">Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* .</span><span class="sxs-lookup"><span data-stu-id="59eb8-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="59eb8-221">Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.</span><span class="sxs-lookup"><span data-stu-id="59eb8-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="59eb8-222">Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:</span><span class="sxs-lookup"><span data-stu-id="59eb8-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="59eb8-223">V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.</span><span class="sxs-lookup"><span data-stu-id="59eb8-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="59eb8-224">Povolení [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="59eb8-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="59eb8-225">Ve výchozím nastavení je zakázané a její použití se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="59eb8-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="59eb8-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.</span><span class="sxs-lookup"><span data-stu-id="59eb8-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="59eb8-227">Obsluhovat soubory z více míst</span><span class="sxs-lookup"><span data-stu-id="59eb8-227">Serve files from multiple locations</span></span>

<span data-ttu-id="59eb8-228">`UseStaticFiles`a `UseFileServer` výchozím nastavením je poskytovatel souborů odkazující na *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="59eb8-228">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="59eb8-229">Můžete poskytnout další instance `UseStaticFiles` a `UseFileServer` s dalšími poskytovateli souborů pro obsluhu souborů z jiných umístění.</span><span class="sxs-lookup"><span data-stu-id="59eb8-229">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="59eb8-230">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="59eb8-230">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="59eb8-231">Požadavky</span><span class="sxs-lookup"><span data-stu-id="59eb8-231">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="59eb8-232">`UseDirectoryBrowser`a `UseStaticFiles` můžou neúniky tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="59eb8-232">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="59eb8-233">Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje.</span><span class="sxs-lookup"><span data-stu-id="59eb8-233">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="59eb8-234">Pečlivě zkontrolujte, které adresáře jsou povoleny prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="59eb8-234">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="59eb8-235">Celý adresář a jeho podadresáře se stanou veřejně přístupnými.</span><span class="sxs-lookup"><span data-stu-id="59eb8-235">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="59eb8-236">Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, například \* \<content_root> /wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="59eb8-236">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="59eb8-237">Oddělte tyto soubory od zobrazení MVC, Razor stránky (jenom 2. x), konfigurační soubory atd.</span><span class="sxs-lookup"><span data-stu-id="59eb8-237">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="59eb8-238">Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů.</span><span class="sxs-lookup"><span data-stu-id="59eb8-238">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="59eb8-239">Například Windows rozlišuje malá a velká písmena &mdash; MacOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="59eb8-239">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="59eb8-240">ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory.</span><span class="sxs-lookup"><span data-stu-id="59eb8-240">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="59eb8-241">Obslužná rutina statického souboru služby IIS se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="59eb8-241">The IIS static file handler isn't used.</span></span> <span data-ttu-id="59eb8-242">Nemá možnost zpracovávat požadavky předtím, než je modul zpracován.</span><span class="sxs-lookup"><span data-stu-id="59eb8-242">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="59eb8-243">Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:</span><span class="sxs-lookup"><span data-stu-id="59eb8-243">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="59eb8-244">Přejděte do funkce **moduly** .</span><span class="sxs-lookup"><span data-stu-id="59eb8-244">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="59eb8-245">V seznamu vyberte **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="59eb8-245">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="59eb8-246">Na bočním panelu **Akce** klikněte na **Odebrat** .</span><span class="sxs-lookup"><span data-stu-id="59eb8-246">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="59eb8-247">Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory.</span><span class="sxs-lookup"><span data-stu-id="59eb8-247">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="59eb8-248">K tomu dojde například v případě, že soubor *web.config* není nasazen.</span><span class="sxs-lookup"><span data-stu-id="59eb8-248">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="59eb8-249">Umístěte soubory kódu (včetně *. cs* a *. cshtml*) mimo [kořenový adresář webu](xref:fundamentals/index#web-root)projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="59eb8-249">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="59eb8-250">Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru.</span><span class="sxs-lookup"><span data-stu-id="59eb8-250">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="59eb8-251">Tím zabráníte úniku kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="59eb8-251">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59eb8-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="59eb8-252">Additional resources</span></span>

* [<span data-ttu-id="59eb8-253">Middleware</span><span class="sxs-lookup"><span data-stu-id="59eb8-253">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="59eb8-254">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59eb8-254">Introduction to ASP.NET Core</span></span>](xref:index)
