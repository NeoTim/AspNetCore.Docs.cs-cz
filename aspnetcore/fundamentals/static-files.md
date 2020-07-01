---
title: Statické soubory v ASP.NET Core
author: rick-anderson
description: Naučte se zajišťovat a zabezpečovat statické soubory a konfigurovat statický soubor hostující chování middlewaru ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 39c5c5d4875e1d59abaa6d998a09dbffd723214d
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793412"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="90d5d-103">Statické soubory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90d5d-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90d5d-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="90d5d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="90d5d-105">Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou assety, ASP.NET Core aplikace ve výchozím nastavení obsluhuje přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="90d5d-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="90d5d-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="90d5d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="90d5d-107">Obsluhovat statické soubory</span><span class="sxs-lookup"><span data-stu-id="90d5d-107">Serve static files</span></span>

<span data-ttu-id="90d5d-108">Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="90d5d-109">Výchozí adresář je `{content root}/wwwroot` , ale dá se změnit pomocí metody [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) .</span><span class="sxs-lookup"><span data-stu-id="90d5d-109">The default directory is `{content root}/wwwroot`, but it can be changed with the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="90d5d-110">Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/index#content-root) a [webový kořenový adresář](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="90d5d-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="90d5d-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Metoda nastaví kořen obsahu na aktuální adresář:</span><span class="sxs-lookup"><span data-stu-id="90d5d-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Program2.cs?name=snippet_Main)]

<span data-ttu-id="90d5d-112">Předchozí kód byl vytvořen pomocí šablony webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="90d5d-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="90d5d-113">Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="90d5d-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="90d5d-114">Například šablony projektu **webové aplikace** obsahují ve složce několik složek `wwwroot` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="90d5d-115">Zvažte vytvoření složky *wwwroot/images* a přidání souboru *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="90d5d-116">Formát identifikátoru URI pro přístup k souboru ve `images` složce je `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="90d5d-117">Například `https://localhost:5001/images/MyImage.jpg`.</span><span class="sxs-lookup"><span data-stu-id="90d5d-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="90d5d-118">Obsluhovat soubory ve webovém kořenovém adresáři</span><span class="sxs-lookup"><span data-stu-id="90d5d-118">Serve files in web root</span></span>

<span data-ttu-id="90d5d-119">Výchozí šablony webové aplikace volají <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> metodu v `Startup.Configure` , která umožňuje obsluhovat statické soubory:</span><span class="sxs-lookup"><span data-stu-id="90d5d-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Startup.cs?name=snippet&highlight=14)]

<span data-ttu-id="90d5d-120">`UseStaticFiles`Přetížení metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé.</span><span class="sxs-lookup"><span data-stu-id="90d5d-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="90d5d-121">Následující značky odkazují na *wwwroot/image/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="90d5d-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg"  class="img" alt="My image." />
```

<span data-ttu-id="90d5d-122">V předchozím kódu znak tildy `~/` ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="90d5d-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="90d5d-123">Obsluhovat soubory mimo web root</span><span class="sxs-lookup"><span data-stu-id="90d5d-123">Serve files outside of web root</span></span>

<span data-ttu-id="90d5d-124">Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="90d5d-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="90d5d-125">Požadavek může získat přístup k `red-rose.jpg` souboru nakonfigurováním middleware statického souboru následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupRose.cs?name=snippet&highlight=14-21)]

<span data-ttu-id="90d5d-126">V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="90d5d-127">Požadavek na `https://<hostname>/StaticFiles/images/red-rose.jpg` obsluhu souboru *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="90d5d-128">Následující značky odkazují na *MyStaticFiles/image/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="90d5d-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose." />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="90d5d-129">Nastavit hlavičky HTTP odpovědi</span><span class="sxs-lookup"><span data-stu-id="90d5d-129">Set HTTP response headers</span></span>

<span data-ttu-id="90d5d-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Objekt lze použít k nastavení hlaviček odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="90d5d-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="90d5d-131">Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:</span><span class="sxs-lookup"><span data-stu-id="90d5d-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupAddHeader.cs?name=snippet&highlight=14-23)]

<span data-ttu-id="90d5d-132">Statické soubory jsou veřejně ukládat do mezipaměti po dobu 600 sekund:</span><span class="sxs-lookup"><span data-stu-id="90d5d-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="90d5d-134">Autorizace statického souboru</span><span class="sxs-lookup"><span data-stu-id="90d5d-134">Static file authorization</span></span>

<span data-ttu-id="90d5d-135">Middleware statických souborů neposkytuje kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="90d5d-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="90d5d-136">Všechny soubory obsluhované IT, včetně těch, které jsou v nástroji `wwwroot` , jsou veřejně přístupné.</span><span class="sxs-lookup"><span data-stu-id="90d5d-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="90d5d-137">Pro obsluhu souborů na základě autorizace:</span><span class="sxs-lookup"><span data-stu-id="90d5d-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="90d5d-138">Ukládejte je mimo `wwwroot` a jakýkoliv adresář přístupný pro middleware statických souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="90d5d-139">Obsluha prostřednictvím metody akce, na které se autorizace používá, a vrácení objektu [výsledku](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) typu:</span><span class="sxs-lookup"><span data-stu-id="90d5d-139">Serve them via an action method to which authorization is applied and return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="directory-browsing"></a><span data-ttu-id="90d5d-140">Procházení adresářů</span><span class="sxs-lookup"><span data-stu-id="90d5d-140">Directory browsing</span></span>

<span data-ttu-id="90d5d-141">Procházení adresářů umožňuje výpis adresáře v rámci určených adresářů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="90d5d-142">Procházení adresářů je ve výchozím nastavení zakázáno z bezpečnostních důvodů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="90d5d-143">Další informace najdete v tématu věnovaném [důležitým](#sc)informacím.</span><span class="sxs-lookup"><span data-stu-id="90d5d-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="90d5d-144">Povolit procházení adresářů pomocí:</span><span class="sxs-lookup"><span data-stu-id="90d5d-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="90d5d-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="90d5d-146">[UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-146">[UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupBrowse.cs?name=snippet&highlight=4,20-34)]

<span data-ttu-id="90d5d-147">Předchozí kód umožňuje procházení adresářů složky *wwwroot/images* pomocí adresy URL `https://<hostname>/MyImages` s odkazy na jednotlivé soubory a složky:</span><span class="sxs-lookup"><span data-stu-id="90d5d-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![procházení adresářů](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="90d5d-149">Obsluha výchozích dokumentů</span><span class="sxs-lookup"><span data-stu-id="90d5d-149">Serve default documents</span></span>

<span data-ttu-id="90d5d-150">Nastavení výchozí stránky poskytuje návštěvníkům výchozí bod na webu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="90d5d-151">Chcete-li zajišťovat výchozí stránku z `wwwroot` bez plně kvalifikovaného identifikátoru URI, zavolejte <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> metodu:</span><span class="sxs-lookup"><span data-stu-id="90d5d-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty.cs?name=snippet&highlight=14)]

<span data-ttu-id="90d5d-152">`UseDefaultFiles`musí být volána předtím, než `UseStaticFiles` bude sloužit jako výchozí soubor.</span><span class="sxs-lookup"><span data-stu-id="90d5d-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="90d5d-153">`UseDefaultFiles`je přepis adresy URL, který soubor neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="90d5d-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="90d5d-154">`UseDefaultFiles`V rámci aplikace vyhledá požadavky na složku v `wwwroot` hledání:</span><span class="sxs-lookup"><span data-stu-id="90d5d-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="90d5d-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="90d5d-155">*default.htm*</span></span>
* <span data-ttu-id="90d5d-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="90d5d-156">*default.html*</span></span>
* <span data-ttu-id="90d5d-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="90d5d-157">*index.htm*</span></span>
* <span data-ttu-id="90d5d-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="90d5d-158">*index.html*</span></span>

<span data-ttu-id="90d5d-159">První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="90d5d-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="90d5d-160">Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="90d5d-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="90d5d-161">Následující kód změní výchozí název souboru na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="90d5d-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet2)]

<span data-ttu-id="90d5d-162">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="90d5d-163">UseFileServer pro výchozí dokumenty</span><span class="sxs-lookup"><span data-stu-id="90d5d-163">UseFileServer for default documents</span></span>

<span data-ttu-id="90d5d-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles` , `UseDefaultFiles` a volitelně `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="90d5d-165">Volání `app.UseFileServer` pro povolení obsluhy statických souborů a výchozího souboru.</span><span class="sxs-lookup"><span data-stu-id="90d5d-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="90d5d-166">Procházení adresářů není povoleno.</span><span class="sxs-lookup"><span data-stu-id="90d5d-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="90d5d-167">Následující kód ukazuje `Startup.Configure` `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty2.cs?name=snippet&highlight=14)]

<span data-ttu-id="90d5d-168">Následující kód povoluje obsluhu statických souborů, výchozího souboru a procházení adresářů:</span><span class="sxs-lookup"><span data-stu-id="90d5d-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="90d5d-169">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty3.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="90d5d-170">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="90d5d-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="90d5d-171">Následující kód povoluje obsluhu statických souborů, výchozí soubor a procházení adresářů `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupUseFileServer.cs?name=snippet&highlight=4,20-30)]

<span data-ttu-id="90d5d-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>musí být volána, pokud `EnableDirectoryBrowsing` je hodnota vlastnosti `true` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="90d5d-173">Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="90d5d-174">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="90d5d-174">URI</span></span>            |      <span data-ttu-id="90d5d-175">Odpověď</span><span class="sxs-lookup"><span data-stu-id="90d5d-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="90d5d-176">*MyStaticFiles/imagí/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="90d5d-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="90d5d-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="90d5d-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="90d5d-178">Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, `https://<hostname>/StaticFiles` vrátí výpis adresáře s odkazy kliknutím:</span><span class="sxs-lookup"><span data-stu-id="90d5d-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Seznam statických souborů](static-files/_static/db2.png)

<span data-ttu-id="90d5d-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z cílového identifikátoru URI bez koncového `/` na cílový identifikátor URI s koncovou `/` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="90d5d-181">Například z `https://<hostname>/StaticFiles` na `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="90d5d-182">Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="90d5d-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="90d5d-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="90d5d-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="90d5d-184">Třída [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME.</span><span class="sxs-lookup"><span data-stu-id="90d5d-184">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="90d5d-185">V následující ukázce jsou několik přípon souborů mapovány na známé typy MIME.</span><span class="sxs-lookup"><span data-stu-id="90d5d-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="90d5d-186">Přípona *. RTF* je nahrazena a *. mp4* je odebráno:</span><span class="sxs-lookup"><span data-stu-id="90d5d-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet2)]

<span data-ttu-id="90d5d-187">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet&highlight=14-42)]

<span data-ttu-id="90d5d-188">Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="90d5d-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="90d5d-189">Nestandardní typy obsahu</span><span class="sxs-lookup"><span data-stu-id="90d5d-189">Non-standard content types</span></span>

<span data-ttu-id="90d5d-190">Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="90d5d-191">Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="90d5d-192">Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="90d5d-193">Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.</span><span class="sxs-lookup"><span data-stu-id="90d5d-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="90d5d-194">Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:</span><span class="sxs-lookup"><span data-stu-id="90d5d-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet2)]

<span data-ttu-id="90d5d-195">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="90d5d-196">V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="90d5d-197">Povolení [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="90d5d-197">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="90d5d-198">Ve výchozím nastavení je zakázané a její použití se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="90d5d-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="90d5d-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.</span><span class="sxs-lookup"><span data-stu-id="90d5d-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="90d5d-200">Obsluhovat soubory z více míst</span><span class="sxs-lookup"><span data-stu-id="90d5d-200">Serve files from multiple locations</span></span>

<span data-ttu-id="90d5d-201">`UseStaticFiles`a `UseFileServer` jako výchozí pro poskytovatele souborů odkazující na `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="90d5d-202">Další instance `UseStaticFiles` a `UseFileServer` lze poskytnout jiným poskytovatelům souborů pro obsluhu souborů z jiných umístění.</span><span class="sxs-lookup"><span data-stu-id="90d5d-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="90d5d-203">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="90d5d-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="90d5d-204">Požadavky na zabezpečení pro statické soubory</span><span class="sxs-lookup"><span data-stu-id="90d5d-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="90d5d-205">`UseDirectoryBrowser`a `UseStaticFiles` můžou neúniky tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="90d5d-206">Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje.</span><span class="sxs-lookup"><span data-stu-id="90d5d-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="90d5d-207">Pečlivě zkontrolujte, které adresáře jsou povoleny prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="90d5d-208">Celý adresář a jeho podadresáře se stanou veřejně přístupnými.</span><span class="sxs-lookup"><span data-stu-id="90d5d-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="90d5d-209">Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, jako je například `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="90d5d-210">Oddělte tyto soubory od zobrazení MVC, Razor stránek, konfiguračních souborů atd.</span><span class="sxs-lookup"><span data-stu-id="90d5d-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="90d5d-211">Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="90d5d-212">Například Windows rozlišuje malá a velká písmena, ale macOS a Linux ne.</span><span class="sxs-lookup"><span data-stu-id="90d5d-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="90d5d-213">ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory.</span><span class="sxs-lookup"><span data-stu-id="90d5d-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="90d5d-214">Obslužná rutina statických souborů služby IIS se nepoužívá a nemá možnost zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="90d5d-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="90d5d-215">Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:</span><span class="sxs-lookup"><span data-stu-id="90d5d-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="90d5d-216">Přejděte do funkce **moduly** .</span><span class="sxs-lookup"><span data-stu-id="90d5d-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="90d5d-217">V seznamu vyberte **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="90d5d-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="90d5d-218">Na bočním panelu **Akce** klikněte na **Odebrat** .</span><span class="sxs-lookup"><span data-stu-id="90d5d-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="90d5d-219">Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory.</span><span class="sxs-lookup"><span data-stu-id="90d5d-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="90d5d-220">K tomu dojde například v případě, že soubor *web.config* není nasazen.</span><span class="sxs-lookup"><span data-stu-id="90d5d-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="90d5d-221">Umístěte soubory kódu, včetně *přípon cs* a *. cshtml*, mimo [webový kořenový adresář](xref:fundamentals/index#web-root)projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="90d5d-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="90d5d-222">Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru.</span><span class="sxs-lookup"><span data-stu-id="90d5d-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="90d5d-223">Tím zabráníte úniku kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="90d5d-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90d5d-224">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="90d5d-224">Additional resources</span></span>

* [<span data-ttu-id="90d5d-225">Middleware</span><span class="sxs-lookup"><span data-stu-id="90d5d-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="90d5d-226">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90d5d-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90d5d-227">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="90d5d-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="90d5d-228">Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou prostředky, které ASP.NET Core aplikace slouží přímo klientům.</span><span class="sxs-lookup"><span data-stu-id="90d5d-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="90d5d-229">K povolení obsluhy těchto souborů je potřeba některá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="90d5d-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="90d5d-230">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="90d5d-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="90d5d-231">Obsluhovat statické soubory</span><span class="sxs-lookup"><span data-stu-id="90d5d-231">Serve static files</span></span>

<span data-ttu-id="90d5d-232">Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="90d5d-233">Výchozí adresář je *{root Content}/wwwroot*, ale dá se změnit prostřednictvím metody [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) .</span><span class="sxs-lookup"><span data-stu-id="90d5d-233">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="90d5d-234">Další informace najdete v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) a ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) .</span><span class="sxs-lookup"><span data-stu-id="90d5d-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="90d5d-235">Webový hostitel aplikace musí být informován o kořenovém adresáři obsahu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="90d5d-236">`WebHost.CreateDefaultBuilder`Metoda nastaví kořen obsahu na aktuální adresář:</span><span class="sxs-lookup"><span data-stu-id="90d5d-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="90d5d-237">Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="90d5d-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="90d5d-238">Například šablona projektu **webové aplikace** obsahuje několik složek v rámci `wwwroot` složky:</span><span class="sxs-lookup"><span data-stu-id="90d5d-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="90d5d-239">Formát identifikátoru URI pro přístup k souboru v podsložce *obrázky* je \*http:// \<server_address> /images/ \<image_file_name> \*.</span><span class="sxs-lookup"><span data-stu-id="90d5d-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="90d5d-240">Například *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="90d5d-241">Pokud cílíte .NET Framework, přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="90d5d-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="90d5d-242">Při cílení na .NET Core obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) tento balíček.</span><span class="sxs-lookup"><span data-stu-id="90d5d-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="90d5d-243">Nakonfigurujte [middleware](xref:fundamentals/middleware/index), který umožňuje obsluhu statických souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="90d5d-244">Obsluhovat soubory uvnitř webového kořenového adresáře</span><span class="sxs-lookup"><span data-stu-id="90d5d-244">Serve files inside of web root</span></span>

<span data-ttu-id="90d5d-245">Vyvolat metodu [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) v rámci `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-245">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="90d5d-246">`UseStaticFiles`Přetížení metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé.</span><span class="sxs-lookup"><span data-stu-id="90d5d-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="90d5d-247">Následující značky odkazují na *wwwroot/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="90d5d-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="90d5d-248">V předchozím kódu znak tildy `~/` ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="90d5d-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="90d5d-249">Obsluhovat soubory mimo web root</span><span class="sxs-lookup"><span data-stu-id="90d5d-249">Serve files outside of web root</span></span>

<span data-ttu-id="90d5d-250">Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="90d5d-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="90d5d-251">Požadavek může získat přístup k souboru *banner1. SVG* nakonfigurováním middleware statického souboru následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="90d5d-252">V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="90d5d-253">Požadavek na *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsluhuje soubor *banner1. SVG* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="90d5d-254">Následující značky odkazují na *MyStaticFiles/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="90d5d-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="90d5d-255">Nastavit hlavičky HTTP odpovědi</span><span class="sxs-lookup"><span data-stu-id="90d5d-255">Set HTTP response headers</span></span>

<span data-ttu-id="90d5d-256">K nastavení hlaviček odpovědí HTTP lze použít objekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) .</span><span class="sxs-lookup"><span data-stu-id="90d5d-256">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="90d5d-257">Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:</span><span class="sxs-lookup"><span data-stu-id="90d5d-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="90d5d-258">V balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) existuje metoda [HeaderDictionaryExtensions. Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) .</span><span class="sxs-lookup"><span data-stu-id="90d5d-258">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="90d5d-259">Soubory byly ve vývojovém prostředí veřejně uložené do mezipaměti po dobu 10 minut (600 sekund):</span><span class="sxs-lookup"><span data-stu-id="90d5d-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="90d5d-261">Autorizace statického souboru</span><span class="sxs-lookup"><span data-stu-id="90d5d-261">Static file authorization</span></span>

<span data-ttu-id="90d5d-262">Middleware statických souborů neposkytuje kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="90d5d-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="90d5d-263">Všechny soubory, které obsluhuje, včetně názvů *wwwroot*, jsou veřejně přístupné.</span><span class="sxs-lookup"><span data-stu-id="90d5d-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="90d5d-264">Pro obsluhu souborů na základě autorizace:</span><span class="sxs-lookup"><span data-stu-id="90d5d-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="90d5d-265">Uložte je mimo *wwwroot* a jakýkoliv adresář přístupný pro middleware statických souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="90d5d-266">Obsluhuje je pomocí metody akce, na které se autorizace používá.</span><span class="sxs-lookup"><span data-stu-id="90d5d-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="90d5d-267">Vrátit objekt [výsledku](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) objektu:</span><span class="sxs-lookup"><span data-stu-id="90d5d-267">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="90d5d-268">Povolit procházení adresářů</span><span class="sxs-lookup"><span data-stu-id="90d5d-268">Enable directory browsing</span></span>

<span data-ttu-id="90d5d-269">Procházení adresářů umožňuje uživatelům vaší webové aplikace zobrazit v zadaném adresáři výpis adresářů a soubory.</span><span class="sxs-lookup"><span data-stu-id="90d5d-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="90d5d-270">Procházení adresářů je ve výchozím nastavení zakázáno z důvodů zabezpečení (viz téma [informace](#sc)).</span><span class="sxs-lookup"><span data-stu-id="90d5d-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="90d5d-271">Povolit procházení adresářů vyvoláním metody [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-271">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="90d5d-272">Přidejte požadované služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metody z `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="90d5d-273">Předchozí kód umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*s odkazy na jednotlivé soubory a složky:</span><span class="sxs-lookup"><span data-stu-id="90d5d-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![procházení adresářů](static-files/_static/dir-browse.png)

<span data-ttu-id="90d5d-275">Při povolování procházení si přečtěte [informace](#considerations) o bezpečnostních rizicích.</span><span class="sxs-lookup"><span data-stu-id="90d5d-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="90d5d-276">Všimněte si dvou `UseStaticFiles` volání v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="90d5d-277">První volání povoluje obsluhu statických souborů ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="90d5d-278">Druhé volání umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*:</span><span class="sxs-lookup"><span data-stu-id="90d5d-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="90d5d-279">Slouží jako výchozí dokument.</span><span class="sxs-lookup"><span data-stu-id="90d5d-279">Serve a default document</span></span>

<span data-ttu-id="90d5d-280">Nastavení výchozí domovské stránky poskytuje návštěvníkům logický výchozí bod při návštěvě vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="90d5d-281">Chcete-li obsloužit výchozí stránku bez toho, aby uživatel plně kvalifikován identifikátor URI, zavolejte metodu [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-281">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="90d5d-282">`UseDefaultFiles`musí být volána předtím, než `UseStaticFiles` bude sloužit jako výchozí soubor.</span><span class="sxs-lookup"><span data-stu-id="90d5d-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="90d5d-283">`UseDefaultFiles`je přepis adresy URL, který soubor ve skutečnosti neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="90d5d-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="90d5d-284">Povolte middleware statických souborů prostřednictvím nástroje `UseStaticFiles` za účelem obsluhy souboru.</span><span class="sxs-lookup"><span data-stu-id="90d5d-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="90d5d-285">S `UseDefaultFiles` , požadavky na hledání složky pro:</span><span class="sxs-lookup"><span data-stu-id="90d5d-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="90d5d-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="90d5d-286">*default.htm*</span></span>
* <span data-ttu-id="90d5d-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="90d5d-287">*default.html*</span></span>
* <span data-ttu-id="90d5d-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="90d5d-288">*index.htm*</span></span>
* <span data-ttu-id="90d5d-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="90d5d-289">*index.html*</span></span>

<span data-ttu-id="90d5d-290">První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="90d5d-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="90d5d-291">Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="90d5d-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="90d5d-292">Následující kód změní výchozí název souboru na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="90d5d-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="90d5d-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="90d5d-293">UseFileServer</span></span>

<span data-ttu-id="90d5d-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles` , `UseDefaultFiles` a volitelně `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="90d5d-295">Následující kód povoluje obsluhu statických souborů a výchozího souboru.</span><span class="sxs-lookup"><span data-stu-id="90d5d-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="90d5d-296">Procházení adresářů není povoleno.</span><span class="sxs-lookup"><span data-stu-id="90d5d-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="90d5d-297">Následující kód je sestaven po přetěžování bez parametrů povolením procházení adresářů:</span><span class="sxs-lookup"><span data-stu-id="90d5d-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="90d5d-298">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="90d5d-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="90d5d-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="90d5d-299">**wwwroot**</span></span>
  * <span data-ttu-id="90d5d-300">**funkcí**</span><span class="sxs-lookup"><span data-stu-id="90d5d-300">**css**</span></span>
  * <span data-ttu-id="90d5d-301">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="90d5d-301">**images**</span></span>
  * <span data-ttu-id="90d5d-302">**js**</span><span class="sxs-lookup"><span data-stu-id="90d5d-302">**js**</span></span>
* <span data-ttu-id="90d5d-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="90d5d-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="90d5d-304">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="90d5d-304">**images**</span></span>
    * <span data-ttu-id="90d5d-305">*banner1. SVG*</span><span class="sxs-lookup"><span data-stu-id="90d5d-305">*banner1.svg*</span></span>
  * <span data-ttu-id="90d5d-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="90d5d-306">*default.html*</span></span>

<span data-ttu-id="90d5d-307">Následující kód povoluje statické soubory, výchozí soubory a procházení adresářů pro `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="90d5d-308">`AddDirectoryBrowser`musí být volána, pokud `EnableDirectoryBrowsing` je hodnota vlastnosti `true` :</span><span class="sxs-lookup"><span data-stu-id="90d5d-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="90d5d-309">Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="90d5d-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="90d5d-310">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="90d5d-310">URI</span></span>            |                             <span data-ttu-id="90d5d-311">Odpověď</span><span class="sxs-lookup"><span data-stu-id="90d5d-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="90d5d-312">*http:// \<server_address> /StaticFiles/images/banner1.SVG*</span><span class="sxs-lookup"><span data-stu-id="90d5d-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="90d5d-313">MyStaticFiles/images/banner1. SVG</span><span class="sxs-lookup"><span data-stu-id="90d5d-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="90d5d-314">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="90d5d-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="90d5d-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="90d5d-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="90d5d-316">Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, *http:// \<server_address> /StaticFiles* vrátí výpis adresáře s odkazy kliknutím:</span><span class="sxs-lookup"><span data-stu-id="90d5d-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Seznam statických souborů](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="90d5d-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez koncového lomítka) na `http://{SERVER ADDRESS}/StaticFiles/` (s koncovým lomítkem).</span><span class="sxs-lookup"><span data-stu-id="90d5d-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="90d5d-319">Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka.</span><span class="sxs-lookup"><span data-stu-id="90d5d-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="90d5d-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="90d5d-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="90d5d-321">Třída [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME.</span><span class="sxs-lookup"><span data-stu-id="90d5d-321">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="90d5d-322">V následující ukázce jsou pro známé typy MIME zaregistrovány několik přípon souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="90d5d-323">Přípona *. RTF* je nahrazena a *. mp4* je odebráno.</span><span class="sxs-lookup"><span data-stu-id="90d5d-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="90d5d-324">Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="90d5d-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="90d5d-325">Nestandardní typy obsahu</span><span class="sxs-lookup"><span data-stu-id="90d5d-325">Non-standard content types</span></span>

<span data-ttu-id="90d5d-326">Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="90d5d-327">Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="90d5d-328">Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* .</span><span class="sxs-lookup"><span data-stu-id="90d5d-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="90d5d-329">Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.</span><span class="sxs-lookup"><span data-stu-id="90d5d-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="90d5d-330">Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:</span><span class="sxs-lookup"><span data-stu-id="90d5d-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="90d5d-331">V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.</span><span class="sxs-lookup"><span data-stu-id="90d5d-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="90d5d-332">Povolení [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="90d5d-332">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="90d5d-333">Ve výchozím nastavení je zakázané a její použití se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="90d5d-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="90d5d-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.</span><span class="sxs-lookup"><span data-stu-id="90d5d-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="90d5d-335">Obsluhovat soubory z více míst</span><span class="sxs-lookup"><span data-stu-id="90d5d-335">Serve files from multiple locations</span></span>

<span data-ttu-id="90d5d-336">`UseStaticFiles`a `UseFileServer` výchozím nastavením je poskytovatel souborů odkazující na *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="90d5d-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="90d5d-337">Můžete poskytnout další instance `UseStaticFiles` a `UseFileServer` s dalšími poskytovateli souborů pro obsluhu souborů z jiných umístění.</span><span class="sxs-lookup"><span data-stu-id="90d5d-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="90d5d-338">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="90d5d-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="90d5d-339">Požadavky</span><span class="sxs-lookup"><span data-stu-id="90d5d-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="90d5d-340">`UseDirectoryBrowser`a `UseStaticFiles` můžou neúniky tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="90d5d-341">Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje.</span><span class="sxs-lookup"><span data-stu-id="90d5d-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="90d5d-342">Pečlivě zkontrolujte, které adresáře jsou povoleny prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="90d5d-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="90d5d-343">Celý adresář a jeho podadresáře se stanou veřejně přístupnými.</span><span class="sxs-lookup"><span data-stu-id="90d5d-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="90d5d-344">Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, například \* \<content_root> /wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="90d5d-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="90d5d-345">Oddělte tyto soubory od zobrazení MVC, Razor stránky (jenom 2. x), konfigurační soubory atd.</span><span class="sxs-lookup"><span data-stu-id="90d5d-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="90d5d-346">Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů.</span><span class="sxs-lookup"><span data-stu-id="90d5d-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="90d5d-347">Například Windows rozlišuje malá a velká písmena &mdash; MacOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="90d5d-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="90d5d-348">ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory.</span><span class="sxs-lookup"><span data-stu-id="90d5d-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="90d5d-349">Obslužná rutina statického souboru služby IIS se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="90d5d-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="90d5d-350">Nemá možnost zpracovávat požadavky předtím, než je modul zpracován.</span><span class="sxs-lookup"><span data-stu-id="90d5d-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="90d5d-351">Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:</span><span class="sxs-lookup"><span data-stu-id="90d5d-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="90d5d-352">Přejděte do funkce **moduly** .</span><span class="sxs-lookup"><span data-stu-id="90d5d-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="90d5d-353">V seznamu vyberte **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="90d5d-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="90d5d-354">Na bočním panelu **Akce** klikněte na **Odebrat** .</span><span class="sxs-lookup"><span data-stu-id="90d5d-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="90d5d-355">Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory.</span><span class="sxs-lookup"><span data-stu-id="90d5d-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="90d5d-356">K tomu dojde například v případě, že soubor *web.config* není nasazen.</span><span class="sxs-lookup"><span data-stu-id="90d5d-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="90d5d-357">Umístěte soubory kódu (včetně *. cs* a *. cshtml*) mimo [kořenový adresář webu](xref:fundamentals/index#web-root)projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="90d5d-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="90d5d-358">Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru.</span><span class="sxs-lookup"><span data-stu-id="90d5d-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="90d5d-359">Tím zabráníte úniku kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="90d5d-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90d5d-360">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="90d5d-360">Additional resources</span></span>

* [<span data-ttu-id="90d5d-361">Middleware</span><span class="sxs-lookup"><span data-stu-id="90d5d-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="90d5d-362">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90d5d-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
