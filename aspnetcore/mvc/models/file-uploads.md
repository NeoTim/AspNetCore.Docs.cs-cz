---
title: Nahrání souborů v ASP.NET Core
author: ardalis
description: Jak používat vazby modelu a streamování pro nahrávání souborů v ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 3db2e08acc1552957f28c7015f9a75af4a57fdcd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65084863"
---
# <a name="file-uploads-in-aspnet-core"></a>Nahrání souborů v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

ASP.NET MVC akce podporují nahrávání jeden nebo více souborů pomocí jednoduchého modelu vazba pro zmenšení souborů nebo datových proudů pro větší soubory.

[Zobrazit nebo stáhnout ukázky z Githubu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a>Nahrávání malé soubory s vazby modelu

Nahrát malých souborů, můžete použít formuláře HTML vícedílné nebo vytvořit požadavek POST pomocí jazyka JavaScript. Formulář příklad pomocí syntaxe Razor, která podporuje více nahraných souborů, je zobrazena níže:

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

Aby bylo možné podporovat nahrávání souborů, musíte zadat formuláře HTML `enctype` z `multipart/form-data`. `files` Elementu input výše uvedené podporuje odeslání více souborů. Vynechat, nechte `multiple` atribut u tohoto elementu input Povolit jenom jeden soubor k nahrání. Výše uvedené značky se vykreslí v prohlížeči jako:

![Formulář pro nahrání souboru](file-uploads/_static/upload-form.png)

Jednotlivé soubory, nahrají se na server je přístupný prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) rozhraní. `IFormFile` má následující strukturu:

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
> Nemusíte spoléhat na nebo vztah důvěryhodnosti `FileName` vlastnost bez ověření. `FileName` Vlastnost by měla být použita pouze pro účely zobrazení.

Při nahrávání souborů pomocí vazby modelu a `IFormFile` rozhraní, metoda akce může přijímat buď jeden `IFormFile` nebo `IEnumerable<IFormFile>` (nebo `List<IFormFile>`) představující několik souborů. Následující příklad prochází jeden nebo více nahraných souborů, uloží do místního systému souborů a vrátí celkový počet a velikost souborů.

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

Soubory nahrané pomocí `IFormFile` techniku jsou ukládány do vyrovnávací paměti v paměti nebo na disku na webovém serveru před zpracováním. Uvnitř metody akce `IFormFile` obsah je dostupný jako datový proud. Kromě místního systému souborů, souborů můžete Streamovat do [úložiště objektů Blob v Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) nebo [Entity Framework](/ef/core/index).

Chcete-li uložit data binárního souboru v databázi pomocí Entity Frameworku, definovat vlastnost typu `byte[]` u entity:

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

Zadejte vlastnost viewmodel typu `IFormFile`:

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> `IFormFile` můžete použít přímo jako parametru metody akce nebo jako vlastnost viewmodel, jak je znázorněno výše.

Kopírovat `IFormFile` do datového proudu a uložte ho do bajtového pole:

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
> Buďte opatrní při ukládání binárních dat v relačních databázích, jak může nepříznivě ovlivnit výkon.

## <a name="uploading-large-files-with-streaming"></a>Nahrávání velkých souborů pomocí streamování

Pokud velikost nebo četnost nahrávání souborů způsobuje problémy prostředků pro aplikace, zvažte streamování samotné nahrávání souborů a místo ukládání do vyrovnávací paměti v celém rozsahu, stejně jako výše uvedené přístup vazby modelu. Při používání `IFormFile` a vazby modelu je mnohem jednodušší řešení streamování vyžaduje několik kroků při implementaci správně.

> [!NOTE]
> Libovolný soubor ve vyrovnávací paměti nad 64KB se přesune z paměti RAM do dočasného souboru na disku na serveru. Prostředky (disk RAM) používané nahrávání souborů, závisí na počtu a velikosti souběžná nahrávání souborů. Streamování není tolik o výkonu, jde o škálování. Pokud se pokusíte uložit do vyrovnávací paměti nahrávání příliš mnoho, dojde k chybě serveru při spuštění z paměti nebo místa na disku.

Následující příklad ukazuje použití jazyka JavaScript/Angular pro streamování na akce kontroleru. V souboru antiforgery token je generován pomocí vlastního atributu filtru a předané v hlavičkách protokolu HTTP místo v textu požadavku. Protože metoda akce zpracovává odesílaná data přímo, vazby modelu zakázal jiný filtr. V akci, obsahu formuláře se načítají pomocí `MultipartReader`, která čte jednotlivých `MultipartSection`, zpracování souboru nebo ukládání obsahu podle potřeby. Po přečtení všech oddílů akci provádí vlastní vazby modelu.

Úvodní akce načtení formuláře a uloží antiforgery token do souboru cookie (prostřednictvím `GenerateAntiforgeryTokenCookieForAjax` atribut):

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

Atribut používá integrované ASP.NET Core [Antiforgery](xref:security/anti-request-forgery) podpora nastavení souborů cookie s token požadavku:

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

Angular automaticky předá antiforgery token v hlavičce požadavku s názvem `X-XSRF-TOKEN`. Aplikace ASP.NET Core MVC je nakonfigurovaná k odkazování na toto záhlaví v jeho konfiguraci v *Startup.cs*:

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

`DisableFormValueModelBinding` Atribut je uvedeno níže, slouží k zakázání vazby modelu pro `Upload` metody akce.

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

Protože se zakázanou vazbou modelu, `Upload` nepřijímá parametry metody akce. Funguje to přímo `Request` vlastnost `ControllerBase`. A `MultipartReader` slouží k načtení jednotlivých oddílů. Soubor je uložen s názvem souboru GUID a klíč/hodnota data jsou uložena v `KeyValueAccumulator`. Jakmile se všechny oddíly mají číst, obsah `KeyValueAccumulator` slouží k vytvoření vazby dat formuláře na typ modelu.

Kompletní `Upload` metody jsou uvedené níže:

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a>Poradce při potížích

V následující tabulce jsou některé běžné problémy vzniklé při práci s nahrávání souborů a jejich možná řešení.

### <a name="unexpected-not-found-error-with-iis"></a>Nebyl nalezen došlo k neočekávané chybě služby IIS

Následující chyba udává nahrávání souboru překračuje serveru nakonfigurovaná `maxAllowedContentLength`:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Ve výchozím nastavení je `30000000`, což je přibližně 28.6 MB. Hodnotu lze přizpůsobit úpravou *web.config*:

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

Toto nastavení platí jenom pro službu IIS. Při hostování na Kestrel chování nedojde ve výchozím nastavení. Další informace najdete v tématu [omezení počtu požadavků \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

### <a name="null-reference-exception-with-iformfile"></a>Výjimka nulového odkazu s IFormFile

Pokud je řadič přijímá nahrát soubory pomocí `IFormFile` můžete zjistit, že je vždy hodnota null, potvrďte, že je určení formuláře HTML, ale `enctype` hodnotu `multipart/form-data`. Pokud tento atribut není nastaven na `<form>` elementu, nedojde, nahrávání souborů a jakoukoli mez `IFormFile` argumenty bude mít hodnotu null.
