---
title: Nahrání souborů v ASP.NET Core
author: ardalis
description: Jak používat vazby modelu a streamování pro nahrávání souborů v ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 831f0c84f0ff062e9e24ccbf4ca81b7143c66e66
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468550"
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="5a02d-103">Nahrání souborů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a02d-103">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="5a02d-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5a02d-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5a02d-105">ASP.NET MVC akce podporují nahrávání jeden nebo více souborů pomocí jednoduchého modelu vazba pro zmenšení souborů nebo datových proudů pro větší soubory.</span><span class="sxs-lookup"><span data-stu-id="5a02d-105">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="5a02d-106">Zobrazit nebo stáhnout ukázky z Githubu</span><span class="sxs-lookup"><span data-stu-id="5a02d-106">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="5a02d-107">Nahrávání malé soubory s vazby modelu</span><span class="sxs-lookup"><span data-stu-id="5a02d-107">Uploading small files with model binding</span></span>

<span data-ttu-id="5a02d-108">Nahrát malých souborů, můžete použít formuláře HTML vícedílné nebo vytvořit požadavek POST pomocí jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5a02d-108">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="5a02d-109">Formulář příklad pomocí syntaxe Razor, která podporuje více nahraných souborů, je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="5a02d-109">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple>
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload">
        </div>
    </div>
</form>
```

<span data-ttu-id="5a02d-110">Aby bylo možné podporovat nahrávání souborů, musíte zadat formuláře HTML `enctype` z `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="5a02d-110">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="5a02d-111">`files` Elementu input výše uvedené podporuje odeslání více souborů.</span><span class="sxs-lookup"><span data-stu-id="5a02d-111">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="5a02d-112">Vynechat, nechte `multiple` atribut u tohoto elementu input Povolit jenom jeden soubor k nahrání.</span><span class="sxs-lookup"><span data-stu-id="5a02d-112">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="5a02d-113">Výše uvedené značky se vykreslí v prohlížeči jako:</span><span class="sxs-lookup"><span data-stu-id="5a02d-113">The above markup renders in a browser as:</span></span>

![Formulář pro nahrání souboru](file-uploads/_static/upload-form.png)

<span data-ttu-id="5a02d-115">Jednotlivé soubory, nahrají se na server je přístupný prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5a02d-115">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> `IFormFile` <span data-ttu-id="5a02d-116">má následující strukturu:</span><span class="sxs-lookup"><span data-stu-id="5a02d-116">has the following structure:</span></span>

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> <span data-ttu-id="5a02d-117">Nemusíte spoléhat na nebo vztah důvěryhodnosti `FileName` vlastnost bez ověření.</span><span class="sxs-lookup"><span data-stu-id="5a02d-117">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="5a02d-118">`FileName` Vlastnost by měla být použita pouze pro účely zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5a02d-118">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="5a02d-119">Při nahrávání souborů pomocí vazby modelu a `IFormFile` rozhraní, metoda akce může přijímat buď jeden `IFormFile` nebo `IEnumerable<IFormFile>` (nebo `List<IFormFile>`) představující několik souborů.</span><span class="sxs-lookup"><span data-stu-id="5a02d-119">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="5a02d-120">Následující příklad prochází jeden nebo více nahraných souborů, uloží do místního systému souborů a vrátí celkový počet a velikost souborů.</span><span class="sxs-lookup"><span data-stu-id="5a02d-120">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

<span data-ttu-id="5a02d-121">Soubory nahrané pomocí `IFormFile` techniku jsou ukládány do vyrovnávací paměti v paměti nebo na disku na webovém serveru před zpracováním.</span><span class="sxs-lookup"><span data-stu-id="5a02d-121">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="5a02d-122">Uvnitř metody akce `IFormFile` obsah je dostupný jako datový proud.</span><span class="sxs-lookup"><span data-stu-id="5a02d-122">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="5a02d-123">Kromě místního systému souborů, souborů můžete Streamovat do [úložiště objektů Blob v Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) nebo [Entity Framework](/ef/core/index).</span><span class="sxs-lookup"><span data-stu-id="5a02d-123">In addition to the local file system, files can be streamed to [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) or [Entity Framework](/ef/core/index).</span></span>

<span data-ttu-id="5a02d-124">Chcete-li uložit data binárního souboru v databázi pomocí Entity Frameworku, definovat vlastnost typu `byte[]` u entity:</span><span class="sxs-lookup"><span data-stu-id="5a02d-124">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="5a02d-125">Zadejte vlastnost viewmodel typu `IFormFile`:</span><span class="sxs-lookup"><span data-stu-id="5a02d-125">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> `IFormFile` <span data-ttu-id="5a02d-126">můžete použít přímo jako parametru metody akce nebo jako vlastnost viewmodel, jak je znázorněno výše.</span><span class="sxs-lookup"><span data-stu-id="5a02d-126">can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="5a02d-127">Kopírovat `IFormFile` do datového proudu a uložte ho do bajtového pole:</span><span class="sxs-lookup"><span data-stu-id="5a02d-127">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser 
        {
            UserName = model.Email,
            Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted

    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> <span data-ttu-id="5a02d-128">Buďte opatrní při ukládání binárních dat v relačních databázích, jak může nepříznivě ovlivnit výkon.</span><span class="sxs-lookup"><span data-stu-id="5a02d-128">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="5a02d-129">Nahrávání velkých souborů pomocí streamování</span><span class="sxs-lookup"><span data-stu-id="5a02d-129">Uploading large files with streaming</span></span>

<span data-ttu-id="5a02d-130">Pokud velikost nebo četnost nahrávání souborů způsobuje problémy prostředků pro aplikace, zvažte streamování samotné nahrávání souborů a místo ukládání do vyrovnávací paměti v celém rozsahu, stejně jako výše uvedené přístup vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5a02d-130">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="5a02d-131">Při používání `IFormFile` a vazby modelu je mnohem jednodušší řešení streamování vyžaduje několik kroků při implementaci správně.</span><span class="sxs-lookup"><span data-stu-id="5a02d-131">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="5a02d-132">Libovolný soubor ve vyrovnávací paměti nad 64KB se přesune z paměti RAM do dočasného souboru na disku na serveru.</span><span class="sxs-lookup"><span data-stu-id="5a02d-132">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="5a02d-133">Prostředky (disk RAM) používané nahrávání souborů, závisí na počtu a velikosti souběžná nahrávání souborů.</span><span class="sxs-lookup"><span data-stu-id="5a02d-133">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="5a02d-134">Streamování není tolik o výkonu, jde o škálování.</span><span class="sxs-lookup"><span data-stu-id="5a02d-134">Streaming isn't so much about perf, it's about scale.</span></span> <span data-ttu-id="5a02d-135">Pokud se pokusíte uložit do vyrovnávací paměti nahrávání příliš mnoho, dojde k chybě serveru při spuštění z paměti nebo místa na disku.</span><span class="sxs-lookup"><span data-stu-id="5a02d-135">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="5a02d-136">Následující příklad ukazuje použití jazyka JavaScript/Angular pro streamování na akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="5a02d-136">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="5a02d-137">V souboru antiforgery token je generován pomocí vlastního atributu filtru a předané v hlavičkách protokolu HTTP místo v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="5a02d-137">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="5a02d-138">Protože metoda akce zpracovává odesílaná data přímo, vazby modelu zakázal jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="5a02d-138">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="5a02d-139">V akci, obsahu formuláře se načítají pomocí `MultipartReader`, která čte jednotlivých `MultipartSection`, zpracování souboru nebo ukládání obsahu podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="5a02d-139">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="5a02d-140">Po přečtení všech oddílů akci provádí vlastní vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5a02d-140">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="5a02d-141">Úvodní akce načtení formuláře a uloží antiforgery token do souboru cookie (prostřednictvím `GenerateAntiforgeryTokenCookieForAjax` atribut):</span><span class="sxs-lookup"><span data-stu-id="5a02d-141">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="5a02d-142">Atribut používá integrované ASP.NET Core [Antiforgery](xref:security/anti-request-forgery) podpora nastavení souborů cookie s token požadavku:</span><span class="sxs-lookup"><span data-stu-id="5a02d-142">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

<span data-ttu-id="5a02d-143">Angular automaticky předá antiforgery token v hlavičce požadavku s názvem `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="5a02d-143">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="5a02d-144">Aplikace ASP.NET Core MVC je nakonfigurovaná k odkazování na toto záhlaví v jeho konfiguraci v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5a02d-144">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

<span data-ttu-id="5a02d-145">`DisableFormValueModelBinding` Atribut je uvedeno níže, slouží k zakázání vazby modelu pro `Upload` metody akce.</span><span class="sxs-lookup"><span data-stu-id="5a02d-145">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

<span data-ttu-id="5a02d-146">Protože se zakázanou vazbou modelu, `Upload` nepřijímá parametry metody akce.</span><span class="sxs-lookup"><span data-stu-id="5a02d-146">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="5a02d-147">Funguje to přímo `Request` vlastnost `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="5a02d-147">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="5a02d-148">A `MultipartReader` slouží k načtení jednotlivých oddílů.</span><span class="sxs-lookup"><span data-stu-id="5a02d-148">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="5a02d-149">Soubor je uložen s názvem souboru GUID a klíč/hodnota data jsou uložena v `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="5a02d-149">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="5a02d-150">Jakmile se všechny oddíly mají číst, obsah `KeyValueAccumulator` slouží k vytvoření vazby dat formuláře na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="5a02d-150">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="5a02d-151">Kompletní `Upload` metody jsou uvedené níže:</span><span class="sxs-lookup"><span data-stu-id="5a02d-151">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a><span data-ttu-id="5a02d-152">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="5a02d-152">Troubleshooting</span></span>

<span data-ttu-id="5a02d-153">V následující tabulce jsou některé běžné problémy vzniklé při práci s nahrávání souborů a jejich možná řešení.</span><span class="sxs-lookup"><span data-stu-id="5a02d-153">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="5a02d-154">Nebyl nalezen došlo k neočekávané chybě služby IIS</span><span class="sxs-lookup"><span data-stu-id="5a02d-154">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="5a02d-155">Následující chyba udává nahrávání souboru překračuje serveru nakonfigurovaná `maxAllowedContentLength`:</span><span class="sxs-lookup"><span data-stu-id="5a02d-155">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="5a02d-156">Ve výchozím nastavení je `30000000`, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="5a02d-156">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="5a02d-157">Hodnotu lze přizpůsobit úpravou *web.config*:</span><span class="sxs-lookup"><span data-stu-id="5a02d-157">The value can be customized by editing *web.config*:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="5a02d-158">Toto nastavení platí jenom pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="5a02d-158">This setting only applies to IIS.</span></span> <span data-ttu-id="5a02d-159">Při hostování na Kestrel chování nedojde ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="5a02d-159">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="5a02d-160">Další informace najdete v tématu [omezení počtu požadavků \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="5a02d-160">For more information, see [Request Limits \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="5a02d-161">Výjimka nulového odkazu s IFormFile</span><span class="sxs-lookup"><span data-stu-id="5a02d-161">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="5a02d-162">Pokud je řadič přijímá nahrát soubory pomocí `IFormFile` můžete zjistit, že je vždy hodnota null, potvrďte, že je určení formuláře HTML, ale `enctype` hodnotu `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="5a02d-162">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="5a02d-163">Pokud tento atribut není nastaven na `<form>` elementu, nedojde, nahrávání souborů a jakoukoli mez `IFormFile` argumenty bude mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5a02d-163">If this attribute isn't set on the `<form>` element, the file upload won't occur and any bound `IFormFile` arguments will be null.</span></span>
