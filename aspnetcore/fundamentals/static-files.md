---
title: Statické soubory v ASP.NET Core
author: rick-anderson
description: Naučte se zajišťovat a zabezpečovat statické soubory a konfigurovat statický soubor hostující chování middlewaru ve ASP.NET Core webové aplikaci.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 32f794d498533e846343831d2360e98939105fb1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016672"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="c5874-103">Statické soubory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5874-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5874-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c5874-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c5874-105">Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou assety, ASP.NET Core aplikace ve výchozím nastavení obsluhuje přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="c5874-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="c5874-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c5874-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="c5874-107">Obsluhovat statické soubory</span><span class="sxs-lookup"><span data-stu-id="c5874-107">Serve static files</span></span>

<span data-ttu-id="c5874-108">Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="c5874-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="c5874-109">Výchozí adresář je `{content root}/wwwroot` , ale dá se změnit pomocí <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="c5874-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="c5874-110">Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/index#content-root) a [webový kořenový adresář](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="c5874-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="c5874-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Metoda nastaví kořen obsahu na aktuální adresář:</span><span class="sxs-lookup"><span data-stu-id="c5874-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="c5874-112">Předchozí kód byl vytvořen pomocí šablony webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c5874-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="c5874-113">Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="c5874-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c5874-114">Například šablony projektu **webové aplikace** obsahují ve složce několik složek `wwwroot` :</span><span class="sxs-lookup"><span data-stu-id="c5874-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="c5874-115">Zvažte vytvoření složky *wwwroot/images* a přidání souboru *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="c5874-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="c5874-116">Formát identifikátoru URI pro přístup k souboru ve `images` složce je `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="c5874-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="c5874-117">Například `https://localhost:5001/images/MyImage.jpg`.</span><span class="sxs-lookup"><span data-stu-id="c5874-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="c5874-118">Obsluhovat soubory ve webovém kořenovém adresáři</span><span class="sxs-lookup"><span data-stu-id="c5874-118">Serve files in web root</span></span>

<span data-ttu-id="c5874-119">Výchozí šablony webové aplikace volají <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> metodu v `Startup.Configure` , která umožňuje obsluhovat statické soubory:</span><span class="sxs-lookup"><span data-stu-id="c5874-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c5874-120">`UseStaticFiles`Přetížení metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé.</span><span class="sxs-lookup"><span data-stu-id="c5874-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="c5874-121">Následující značky odkazují na *wwwroot/image/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="c5874-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="c5874-122">V předchozím kódu znak tildy `~/` ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="c5874-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="c5874-123">Obsluhovat soubory mimo web root</span><span class="sxs-lookup"><span data-stu-id="c5874-123">Serve files outside of web root</span></span>

<span data-ttu-id="c5874-124">Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="c5874-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="c5874-125">Požadavek může získat přístup k `red-rose.jpg` souboru nakonfigurováním middleware statického souboru následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c5874-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="c5874-126">V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="c5874-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="c5874-127">Požadavek na `https://<hostname>/StaticFiles/images/red-rose.jpg` obsluhu souboru *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="c5874-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="c5874-128">Následující značky odkazují na *MyStaticFiles/image/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="c5874-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="c5874-129">Nastavit hlavičky HTTP odpovědi</span><span class="sxs-lookup"><span data-stu-id="c5874-129">Set HTTP response headers</span></span>

<span data-ttu-id="c5874-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Objekt lze použít k nastavení hlaviček odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5874-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="c5874-131">Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:</span><span class="sxs-lookup"><span data-stu-id="c5874-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="c5874-132">Statické soubory jsou veřejně ukládat do mezipaměti po dobu 600 sekund:</span><span class="sxs-lookup"><span data-stu-id="c5874-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="c5874-134">Autorizace statického souboru</span><span class="sxs-lookup"><span data-stu-id="c5874-134">Static file authorization</span></span>

<span data-ttu-id="c5874-135">Middleware statických souborů neposkytuje kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="c5874-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="c5874-136">Všechny soubory obsluhované IT, včetně těch, které jsou v nástroji `wwwroot` , jsou veřejně přístupné.</span><span class="sxs-lookup"><span data-stu-id="c5874-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="c5874-137">Pro obsluhu souborů na základě autorizace:</span><span class="sxs-lookup"><span data-stu-id="c5874-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="c5874-138">Ukládejte je mimo `wwwroot` a jakýkoliv adresář přístupný pro middleware statických souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="c5874-139">Obsluha prostřednictvím metody akce, na které se autorizace používá, a vrácení <xref:Microsoft.AspNetCore.Mvc.FileResult> objektu:</span><span class="sxs-lookup"><span data-stu-id="c5874-139">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="c5874-140">Procházení adresářů</span><span class="sxs-lookup"><span data-stu-id="c5874-140">Directory browsing</span></span>

<span data-ttu-id="c5874-141">Procházení adresářů umožňuje výpis adresáře v rámci určených adresářů.</span><span class="sxs-lookup"><span data-stu-id="c5874-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="c5874-142">Procházení adresářů je ve výchozím nastavení zakázáno z bezpečnostních důvodů.</span><span class="sxs-lookup"><span data-stu-id="c5874-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="c5874-143">Další informace najdete v tématu věnovaném [důležitým](#sc)informacím.</span><span class="sxs-lookup"><span data-stu-id="c5874-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="c5874-144">Povolit procházení adresářů pomocí:</span><span class="sxs-lookup"><span data-stu-id="c5874-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="c5874-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c5874-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="c5874-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A>v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c5874-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="c5874-147">Předchozí kód umožňuje procházení adresářů složky *wwwroot/images* pomocí adresy URL `https://<hostname>/MyImages` s odkazy na jednotlivé soubory a složky:</span><span class="sxs-lookup"><span data-stu-id="c5874-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![procházení adresářů](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="c5874-149">Obsluha výchozích dokumentů</span><span class="sxs-lookup"><span data-stu-id="c5874-149">Serve default documents</span></span>

<span data-ttu-id="c5874-150">Nastavení výchozí stránky poskytuje návštěvníkům výchozí bod na webu.</span><span class="sxs-lookup"><span data-stu-id="c5874-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="c5874-151">Chcete-li zajišťovat výchozí stránku z `wwwroot` bez plně kvalifikovaného identifikátoru URI, zavolejte <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> metodu:</span><span class="sxs-lookup"><span data-stu-id="c5874-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c5874-152">`UseDefaultFiles`musí být volána předtím, než `UseStaticFiles` bude sloužit jako výchozí soubor.</span><span class="sxs-lookup"><span data-stu-id="c5874-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="c5874-153">`UseDefaultFiles`je přepis adresy URL, který soubor neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="c5874-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="c5874-154">`UseDefaultFiles`V rámci aplikace vyhledá požadavky na složku v `wwwroot` hledání:</span><span class="sxs-lookup"><span data-stu-id="c5874-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="c5874-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="c5874-155">*default.htm*</span></span>
* <span data-ttu-id="c5874-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="c5874-156">*default.html*</span></span>
* <span data-ttu-id="c5874-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="c5874-157">*index.htm*</span></span>
* <span data-ttu-id="c5874-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="c5874-158">*index.html*</span></span>

<span data-ttu-id="c5874-159">První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="c5874-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="c5874-160">Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c5874-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="c5874-161">Následující kód změní výchozí název souboru na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="c5874-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="c5874-162">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="c5874-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="c5874-163">UseFileServer pro výchozí dokumenty</span><span class="sxs-lookup"><span data-stu-id="c5874-163">UseFileServer for default documents</span></span>

<span data-ttu-id="c5874-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles` , `UseDefaultFiles` a volitelně `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="c5874-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="c5874-165">Volání `app.UseFileServer` pro povolení obsluhy statických souborů a výchozího souboru.</span><span class="sxs-lookup"><span data-stu-id="c5874-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="c5874-166">Procházení adresářů není povoleno.</span><span class="sxs-lookup"><span data-stu-id="c5874-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="c5874-167">Následující kód ukazuje `Startup.Configure` `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="c5874-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c5874-168">Následující kód povoluje obsluhu statických souborů, výchozího souboru a procházení adresářů:</span><span class="sxs-lookup"><span data-stu-id="c5874-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="c5874-169">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="c5874-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c5874-170">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="c5874-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="c5874-171">Následující kód povoluje obsluhu statických souborů, výchozí soubor a procházení adresářů `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="c5874-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="c5874-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>musí být volána, pokud `EnableDirectoryBrowsing` je hodnota vlastnosti `true` .</span><span class="sxs-lookup"><span data-stu-id="c5874-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="c5874-173">Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c5874-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="c5874-174">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="c5874-174">URI</span></span>            |      <span data-ttu-id="c5874-175">Odpověď</span><span class="sxs-lookup"><span data-stu-id="c5874-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="c5874-176">*MyStaticFiles/imagí/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="c5874-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="c5874-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="c5874-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="c5874-178">Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, `https://<hostname>/StaticFiles` vrátí výpis adresáře s odkazy kliknutím:</span><span class="sxs-lookup"><span data-stu-id="c5874-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Seznam statických souborů](static-files/_static/db2.png)

<span data-ttu-id="c5874-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z cílového identifikátoru URI bez koncového `/` na cílový identifikátor URI s koncovou `/` .</span><span class="sxs-lookup"><span data-stu-id="c5874-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="c5874-181">Například z `https://<hostname>/StaticFiles` na `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="c5874-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="c5874-182">Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="c5874-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="c5874-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="c5874-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="c5874-184"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Třída obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME.</span><span class="sxs-lookup"><span data-stu-id="c5874-184">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="c5874-185">V následující ukázce jsou několik přípon souborů mapovány na známé typy MIME.</span><span class="sxs-lookup"><span data-stu-id="c5874-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="c5874-186">Přípona *. RTF* je nahrazena a *. mp4* je odebráno:</span><span class="sxs-lookup"><span data-stu-id="c5874-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="c5874-187">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="c5874-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="c5874-188">Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="c5874-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="c5874-189">Nestandardní typy obsahu</span><span class="sxs-lookup"><span data-stu-id="c5874-189">Non-standard content types</span></span>

<span data-ttu-id="c5874-190">Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="c5874-191">Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c5874-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="c5874-192">Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* .</span><span class="sxs-lookup"><span data-stu-id="c5874-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="c5874-193">Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.</span><span class="sxs-lookup"><span data-stu-id="c5874-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="c5874-194">Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:</span><span class="sxs-lookup"><span data-stu-id="c5874-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="c5874-195">Následující kód ukazuje `Startup.Configure` s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="c5874-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="c5874-196">V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.</span><span class="sxs-lookup"><span data-stu-id="c5874-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="c5874-197">Povolení <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="c5874-197">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="c5874-198">Ve výchozím nastavení je zakázané a její použití se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="c5874-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="c5874-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.</span><span class="sxs-lookup"><span data-stu-id="c5874-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="c5874-200">Obsluhovat soubory z více míst</span><span class="sxs-lookup"><span data-stu-id="c5874-200">Serve files from multiple locations</span></span>

<span data-ttu-id="c5874-201">`UseStaticFiles`a `UseFileServer` jako výchozí pro poskytovatele souborů odkazující na `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="c5874-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="c5874-202">Další instance `UseStaticFiles` a `UseFileServer` lze poskytnout jiným poskytovatelům souborů pro obsluhu souborů z jiných umístění.</span><span class="sxs-lookup"><span data-stu-id="c5874-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="c5874-203">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="c5874-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="c5874-204">Požadavky na zabezpečení pro statické soubory</span><span class="sxs-lookup"><span data-stu-id="c5874-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="c5874-205">`UseDirectoryBrowser`a `UseStaticFiles` můžou neúniky tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="c5874-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="c5874-206">Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje.</span><span class="sxs-lookup"><span data-stu-id="c5874-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="c5874-207">Pečlivě zkontrolujte, které adresáře jsou povoleny prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="c5874-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="c5874-208">Celý adresář a jeho podadresáře se stanou veřejně přístupnými.</span><span class="sxs-lookup"><span data-stu-id="c5874-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="c5874-209">Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, jako je například `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="c5874-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="c5874-210">Oddělte tyto soubory od zobrazení MVC, Razor stránek, konfiguračních souborů atd.</span><span class="sxs-lookup"><span data-stu-id="c5874-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="c5874-211">Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="c5874-212">Například Windows rozlišuje malá a velká písmena, ale macOS a Linux ne.</span><span class="sxs-lookup"><span data-stu-id="c5874-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="c5874-213">ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory.</span><span class="sxs-lookup"><span data-stu-id="c5874-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="c5874-214">Obslužná rutina statických souborů služby IIS se nepoužívá a nemá možnost zpracovávat požadavky.</span><span class="sxs-lookup"><span data-stu-id="c5874-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="c5874-215">Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:</span><span class="sxs-lookup"><span data-stu-id="c5874-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="c5874-216">Přejděte do funkce **moduly** .</span><span class="sxs-lookup"><span data-stu-id="c5874-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="c5874-217">V seznamu vyberte **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="c5874-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="c5874-218">Na bočním panelu **Akce** klikněte na **Odebrat** .</span><span class="sxs-lookup"><span data-stu-id="c5874-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="c5874-219">Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory.</span><span class="sxs-lookup"><span data-stu-id="c5874-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="c5874-220">K tomu dojde například v případě, že soubor *web.config* není nasazen.</span><span class="sxs-lookup"><span data-stu-id="c5874-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="c5874-221">Umístěte soubory kódu, včetně *přípon cs* a *. cshtml*, mimo [webový kořenový adresář](xref:fundamentals/index#web-root)projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c5874-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c5874-222">Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru.</span><span class="sxs-lookup"><span data-stu-id="c5874-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="c5874-223">Tím zabráníte úniku kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="c5874-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c5874-224">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c5874-224">Additional resources</span></span>

* [<span data-ttu-id="c5874-225">Middleware</span><span class="sxs-lookup"><span data-stu-id="c5874-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c5874-226">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5874-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c5874-227">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="c5874-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="c5874-228">Statické soubory, jako jsou HTML, CSS, obrázky a JavaScript, jsou prostředky, které ASP.NET Core aplikace slouží přímo klientům.</span><span class="sxs-lookup"><span data-stu-id="c5874-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="c5874-229">K povolení obsluhy těchto souborů je potřeba některá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c5874-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="c5874-230">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c5874-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="c5874-231">Obsluhovat statické soubory</span><span class="sxs-lookup"><span data-stu-id="c5874-231">Serve static files</span></span>

<span data-ttu-id="c5874-232">Statické soubory jsou uloženy v [kořenovém adresáři webu](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="c5874-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="c5874-233">Výchozí adresář je *{root obsahu}/wwwroot*, ale dá se změnit prostřednictvím <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="c5874-233">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="c5874-234">Další informace najdete v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) a ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) .</span><span class="sxs-lookup"><span data-stu-id="c5874-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="c5874-235">Webový hostitel aplikace musí být informován o kořenovém adresáři obsahu.</span><span class="sxs-lookup"><span data-stu-id="c5874-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="c5874-236">`WebHost.CreateDefaultBuilder`Metoda nastaví kořen obsahu na aktuální adresář:</span><span class="sxs-lookup"><span data-stu-id="c5874-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="c5874-237">Statické soubory jsou přístupné prostřednictvím cesty relativní k [webovému kořenovému adresáři](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="c5874-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c5874-238">Například šablona projektu **webové aplikace** obsahuje několik složek v rámci `wwwroot` složky:</span><span class="sxs-lookup"><span data-stu-id="c5874-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="c5874-239">Formát identifikátoru URI pro přístup k souboru v podsložce *obrázky* je \*http:// \<server_address> /images/ \<image_file_name> \*.</span><span class="sxs-lookup"><span data-stu-id="c5874-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="c5874-240">Příklad: *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="c5874-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="c5874-241">Pokud cílíte .NET Framework, přidejte do projektu balíček [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="c5874-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="c5874-242">Při cílení na .NET Core obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) tento balíček.</span><span class="sxs-lookup"><span data-stu-id="c5874-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="c5874-243">Nakonfigurujte [middleware](xref:fundamentals/middleware/index), který umožňuje obsluhu statických souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="c5874-244">Obsluhovat soubory uvnitř webového kořenového adresáře</span><span class="sxs-lookup"><span data-stu-id="c5874-244">Serve files inside of web root</span></span>

<span data-ttu-id="c5874-245">Vyvolat <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodu v rámci `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c5874-245">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="c5874-246">`UseStaticFiles`Přetížení metody bez parametrů označí soubory ve [webovém kořenovém adresáři](xref:fundamentals/index#web-root) jako servé.</span><span class="sxs-lookup"><span data-stu-id="c5874-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="c5874-247">Následující značky odkazují na *wwwroot/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="c5874-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="c5874-248">V předchozím kódu znak tildy `~/` ukazuje na [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="c5874-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="c5874-249">Obsluhovat soubory mimo web root</span><span class="sxs-lookup"><span data-stu-id="c5874-249">Serve files outside of web root</span></span>

<span data-ttu-id="c5874-250">Vezměte v úvahu hierarchii adresářů, ve které se statické soubory budou obsluhovat mimo [webový kořenový adresář](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="c5874-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="c5874-251">Požadavek může získat přístup k souboru *banner1. SVG* nakonfigurováním middleware statického souboru následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c5874-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="c5874-252">V předchozím kódu se hierarchie adresáře *MyStaticFiles* zveřejňuje veřejně prostřednictvím segmentu URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="c5874-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="c5874-253">Požadavek na *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsluhuje soubor *banner1. SVG* .</span><span class="sxs-lookup"><span data-stu-id="c5874-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="c5874-254">Následující značky odkazují na *MyStaticFiles/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="c5874-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="c5874-255">Nastavit hlavičky HTTP odpovědi</span><span class="sxs-lookup"><span data-stu-id="c5874-255">Set HTTP response headers</span></span>

<span data-ttu-id="c5874-256"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Objekt lze použít k nastavení hlaviček odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5874-256">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="c5874-257">Kromě konfigurace statického souboru obsluha z [webového kořenového adresáře](xref:fundamentals/index#web-root)nastaví následující kód `Cache-Control` hlavičku:</span><span class="sxs-lookup"><span data-stu-id="c5874-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c5874-258"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>Metoda existuje v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="c5874-258">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="c5874-259">Soubory byly ve vývojovém prostředí veřejně uložené do mezipaměti po dobu 10 minut (600 sekund):</span><span class="sxs-lookup"><span data-stu-id="c5874-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Hlavičky odpovědi ukazující hlavičku Cache-Control se přidaly.](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="c5874-261">Autorizace statického souboru</span><span class="sxs-lookup"><span data-stu-id="c5874-261">Static file authorization</span></span>

<span data-ttu-id="c5874-262">Middleware statických souborů neposkytuje kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="c5874-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="c5874-263">Všechny soubory, které obsluhuje, včetně názvů *wwwroot*, jsou veřejně přístupné.</span><span class="sxs-lookup"><span data-stu-id="c5874-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="c5874-264">Pro obsluhu souborů na základě autorizace:</span><span class="sxs-lookup"><span data-stu-id="c5874-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="c5874-265">Uložte je mimo *wwwroot* a jakýkoliv adresář přístupný pro middleware statických souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="c5874-266">Obsluhuje je pomocí metody akce, na které se autorizace používá.</span><span class="sxs-lookup"><span data-stu-id="c5874-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="c5874-267">Vrátit <xref:Microsoft.AspNetCore.Mvc.FileResult> objekt:</span><span class="sxs-lookup"><span data-stu-id="c5874-267">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="c5874-268">Povolit procházení adresářů</span><span class="sxs-lookup"><span data-stu-id="c5874-268">Enable directory browsing</span></span>

<span data-ttu-id="c5874-269">Procházení adresářů umožňuje uživatelům vaší webové aplikace zobrazit v zadaném adresáři výpis adresářů a soubory.</span><span class="sxs-lookup"><span data-stu-id="c5874-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="c5874-270">Procházení adresářů je ve výchozím nastavení zakázáno z důvodů zabezpečení (viz téma [informace](#sc)).</span><span class="sxs-lookup"><span data-stu-id="c5874-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="c5874-271">Povolit procházení adresářů vyvoláním <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> metody v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c5874-271">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="c5874-272">Přidejte požadované služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metody z `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c5874-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="c5874-273">Předchozí kód umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*s odkazy na jednotlivé soubory a složky:</span><span class="sxs-lookup"><span data-stu-id="c5874-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![procházení adresářů](static-files/_static/dir-browse.png)

<span data-ttu-id="c5874-275">Při povolování procházení si přečtěte [informace](#considerations) o bezpečnostních rizicích.</span><span class="sxs-lookup"><span data-stu-id="c5874-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="c5874-276">Všimněte si dvou `UseStaticFiles` volání v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="c5874-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="c5874-277">První volání povoluje obsluhu statických souborů ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c5874-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="c5874-278">Druhé volání umožňuje procházení adresářů složky *wwwroot/imagí* pomocí adresy URL *http:// \<server_address> /MyImages*:</span><span class="sxs-lookup"><span data-stu-id="c5874-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="c5874-279">Slouží jako výchozí dokument.</span><span class="sxs-lookup"><span data-stu-id="c5874-279">Serve a default document</span></span>

<span data-ttu-id="c5874-280">Nastavení výchozí domovské stránky poskytuje návštěvníkům logický výchozí bod při návštěvě vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="c5874-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="c5874-281">Chcete-li obsloužit výchozí stránku bez toho, aby uživatel plně kvalifikován identifikátor URI, zavolejte <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> metodu z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c5874-281">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="c5874-282">`UseDefaultFiles`musí být volána předtím, než `UseStaticFiles` bude sloužit jako výchozí soubor.</span><span class="sxs-lookup"><span data-stu-id="c5874-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="c5874-283">`UseDefaultFiles`je přepis adresy URL, který soubor ve skutečnosti neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="c5874-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="c5874-284">Povolte middleware statických souborů prostřednictvím nástroje `UseStaticFiles` za účelem obsluhy souboru.</span><span class="sxs-lookup"><span data-stu-id="c5874-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="c5874-285">S `UseDefaultFiles` , požadavky na hledání složky pro:</span><span class="sxs-lookup"><span data-stu-id="c5874-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="c5874-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="c5874-286">*default.htm*</span></span>
* <span data-ttu-id="c5874-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="c5874-287">*default.html*</span></span>
* <span data-ttu-id="c5874-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="c5874-288">*index.htm*</span></span>
* <span data-ttu-id="c5874-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="c5874-289">*index.html*</span></span>

<span data-ttu-id="c5874-290">První soubor, který se nachází v seznamu, se obsluhuje, jako by se jednalo o plně kvalifikovaný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="c5874-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="c5874-291">Adresa URL prohlížeče bude dál odpovídat požadovanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c5874-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="c5874-292">Následující kód změní výchozí název souboru na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="c5874-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="c5874-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="c5874-293">UseFileServer</span></span>

<span data-ttu-id="c5874-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>kombinuje funkce `UseStaticFiles` , `UseDefaultFiles` a volitelně `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="c5874-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="c5874-295">Následující kód povoluje obsluhu statických souborů a výchozího souboru.</span><span class="sxs-lookup"><span data-stu-id="c5874-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="c5874-296">Procházení adresářů není povoleno.</span><span class="sxs-lookup"><span data-stu-id="c5874-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="c5874-297">Následující kód je sestaven po přetěžování bez parametrů povolením procházení adresářů:</span><span class="sxs-lookup"><span data-stu-id="c5874-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="c5874-298">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="c5874-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="c5874-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="c5874-299">**wwwroot**</span></span>
  * <span data-ttu-id="c5874-300">**funkcí**</span><span class="sxs-lookup"><span data-stu-id="c5874-300">**css**</span></span>
  * <span data-ttu-id="c5874-301">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="c5874-301">**images**</span></span>
  * <span data-ttu-id="c5874-302">**js**</span><span class="sxs-lookup"><span data-stu-id="c5874-302">**js**</span></span>
* <span data-ttu-id="c5874-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="c5874-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="c5874-304">**fotografií**</span><span class="sxs-lookup"><span data-stu-id="c5874-304">**images**</span></span>
    * <span data-ttu-id="c5874-305">*banner1. SVG*</span><span class="sxs-lookup"><span data-stu-id="c5874-305">*banner1.svg*</span></span>
  * <span data-ttu-id="c5874-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="c5874-306">*default.html*</span></span>

<span data-ttu-id="c5874-307">Následující kód povoluje statické soubory, výchozí soubory a procházení adresářů pro `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="c5874-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="c5874-308">`AddDirectoryBrowser`musí být volána, pokud `EnableDirectoryBrowsing` je hodnota vlastnosti `true` :</span><span class="sxs-lookup"><span data-stu-id="c5874-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="c5874-309">Pomocí hierarchie souborů a předchozího kódu adresy URL překládat následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c5874-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="c5874-310">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="c5874-310">URI</span></span>            |                             <span data-ttu-id="c5874-311">Odpověď</span><span class="sxs-lookup"><span data-stu-id="c5874-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="c5874-312">*http:// \<server_address> /StaticFiles/images/banner1.SVG*</span><span class="sxs-lookup"><span data-stu-id="c5874-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="c5874-313">MyStaticFiles/images/banner1. SVG</span><span class="sxs-lookup"><span data-stu-id="c5874-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="c5874-314">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="c5874-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="c5874-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="c5874-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="c5874-316">Pokud v adresáři *MyStaticFiles* neexistuje žádný výchozí soubor s názvem, *http:// \<server_address> /StaticFiles* vrátí výpis adresáře s odkazy kliknutím:</span><span class="sxs-lookup"><span data-stu-id="c5874-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Seznam statických souborů](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="c5874-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>a <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> provede přesměrování na straně klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez koncového lomítka) na `http://{SERVER ADDRESS}/StaticFiles/` (s koncovým lomítkem).</span><span class="sxs-lookup"><span data-stu-id="c5874-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="c5874-319">Relativní adresy URL v adresáři *StaticFiles* jsou neplatné bez koncového lomítka.</span><span class="sxs-lookup"><span data-stu-id="c5874-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="c5874-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="c5874-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="c5874-321"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Třída obsahuje `Mappings` vlastnost, která slouží jako mapování přípon souborů na typy obsahu MIME.</span><span class="sxs-lookup"><span data-stu-id="c5874-321">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="c5874-322">V následující ukázce jsou pro známé typy MIME zaregistrovány několik přípon souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="c5874-323">Přípona *. RTF* je nahrazena a *. mp4* je odebráno.</span><span class="sxs-lookup"><span data-stu-id="c5874-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="c5874-324">Viz [typy obsahu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="c5874-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="c5874-325">Nestandardní typy obsahu</span><span class="sxs-lookup"><span data-stu-id="c5874-325">Non-standard content types</span></span>

<span data-ttu-id="c5874-326">Middleware statických souborů rozumí téměř 400 známým typům obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="c5874-327">Pokud uživatel požádá o soubor s neznámým typem souboru, middleware statických souborů předá požadavek dalšímu middlewaru v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c5874-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="c5874-328">Pokud žádost nezpracovává žádné middleware, je vrácena odpověď 404, která *nebyla nalezena* .</span><span class="sxs-lookup"><span data-stu-id="c5874-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="c5874-329">Pokud je povoleno procházení adresářů, zobrazí se v seznamu adresářů odkaz na tento soubor.</span><span class="sxs-lookup"><span data-stu-id="c5874-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="c5874-330">Následující kód povoluje obsluhu neznámých typů a vykresluje neznámý soubor jako obrázek:</span><span class="sxs-lookup"><span data-stu-id="c5874-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="c5874-331">V předchozím kódu se jako obrázek vrátí požadavek na soubor s neznámým typem obsahu.</span><span class="sxs-lookup"><span data-stu-id="c5874-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="c5874-332">Povolení <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="c5874-332">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="c5874-333">Ve výchozím nastavení je zakázané a její použití se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="c5874-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="c5874-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) poskytuje bezpečnější alternativu pro obsluhu souborů s nestandardními rozšířeními.</span><span class="sxs-lookup"><span data-stu-id="c5874-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="c5874-335">Obsluhovat soubory z více míst</span><span class="sxs-lookup"><span data-stu-id="c5874-335">Serve files from multiple locations</span></span>

<span data-ttu-id="c5874-336">`UseStaticFiles`a `UseFileServer` výchozím nastavením je poskytovatel souborů odkazující na *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="c5874-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="c5874-337">Můžete poskytnout další instance `UseStaticFiles` a `UseFileServer` s dalšími poskytovateli souborů pro obsluhu souborů z jiných umístění.</span><span class="sxs-lookup"><span data-stu-id="c5874-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="c5874-338">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="c5874-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="c5874-339">Požadavky</span><span class="sxs-lookup"><span data-stu-id="c5874-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="c5874-340">`UseDirectoryBrowser`a `UseStaticFiles` můžou neúniky tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="c5874-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="c5874-341">Zakázání procházení adresářů v produkčním prostředí se důrazně doporučuje.</span><span class="sxs-lookup"><span data-stu-id="c5874-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="c5874-342">Pečlivě zkontrolujte, které adresáře jsou povoleny prostřednictvím `UseStaticFiles` nebo `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="c5874-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="c5874-343">Celý adresář a jeho podadresáře se stanou veřejně přístupnými.</span><span class="sxs-lookup"><span data-stu-id="c5874-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="c5874-344">Ukládejte soubory vhodné pro poskytování veřejnosti ve vyhrazeném adresáři, například \* \<content_root> /wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="c5874-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="c5874-345">Oddělte tyto soubory od zobrazení MVC, Razor stránky (jenom 2. x), konfigurační soubory atd.</span><span class="sxs-lookup"><span data-stu-id="c5874-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="c5874-346">Adresy URL obsahu vystaveného nástroji `UseDirectoryBrowser` a `UseStaticFiles` podléhají omezením malých a velkých písmen a znakům základního systému souborů.</span><span class="sxs-lookup"><span data-stu-id="c5874-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="c5874-347">Například Windows rozlišuje malá a velká písmena &mdash; MacOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="c5874-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="c5874-348">ASP.NET Core aplikace hostované ve službě IIS používají [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k přeposílání všech požadavků do aplikace, včetně požadavků na statické soubory.</span><span class="sxs-lookup"><span data-stu-id="c5874-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="c5874-349">Obslužná rutina statického souboru služby IIS se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="c5874-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="c5874-350">Nemá možnost zpracovávat požadavky předtím, než je modul zpracován.</span><span class="sxs-lookup"><span data-stu-id="c5874-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="c5874-351">Provedením následujících kroků ve Správci služby IIS odeberte obslužnou rutinu statického souboru služby IIS na úrovni serveru nebo webu:</span><span class="sxs-lookup"><span data-stu-id="c5874-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="c5874-352">Přejděte do funkce **moduly** .</span><span class="sxs-lookup"><span data-stu-id="c5874-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="c5874-353">V seznamu vyberte **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="c5874-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="c5874-354">Na bočním panelu **Akce** klikněte na **Odebrat** .</span><span class="sxs-lookup"><span data-stu-id="c5874-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="c5874-355">Pokud je povolena obslužná rutina statických souborů služby IIS **a** modul ASP.NET Core je nesprávně nakonfigurován, jsou obsluhovány statické soubory.</span><span class="sxs-lookup"><span data-stu-id="c5874-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="c5874-356">K tomu dojde například v případě, že soubor *web.config* není nasazen.</span><span class="sxs-lookup"><span data-stu-id="c5874-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="c5874-357">Umístěte soubory kódu (včetně *. cs* a *. cshtml*) mimo [kořenový adresář webu](xref:fundamentals/index#web-root)projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c5874-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c5874-358">Vytvoří se logické oddělení mezi obsahem aplikace na straně klienta a kódem serveru.</span><span class="sxs-lookup"><span data-stu-id="c5874-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="c5874-359">Tím zabráníte úniku kódu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="c5874-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c5874-360">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c5874-360">Additional resources</span></span>

* [<span data-ttu-id="c5874-361">Middleware</span><span class="sxs-lookup"><span data-stu-id="c5874-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c5874-362">Úvod do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5874-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
