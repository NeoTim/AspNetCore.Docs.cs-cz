---
title: Nahrávání souborů v ASP.NET jádru
author: rick-anderson
description: Jak používat vazby modelu a streamování k nahrávání souborů v ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661741"
---
# <a name="upload-files-in-aspnet-core"></a>Nahrávání souborů v ASP.NET jádru

[Steve Smith](https://ardalis.com/) a [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Buďte opatrní, pokud uživatelům poskytujete možnost nahrávat soubory na server. Útočníci se mohou pokusit o:

* Spusťte útoky [odmítnutí služby.](/windows-hardware/drivers/ifs/denial-of-service)
* Nahrajte viry nebo malware.
* Kompromitujte sítě a servery jinými způsoby.

Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:

* Nahrávejte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe na nesystémovou jednotku. Vyhrazené umístění usnadňuje ukládání bezpečnostních omezení pro nahrané soubory. Zakažte oprávnění ke spuštění v umístění pro nahrávání souboru.&dagger;
* **Nezachovávejte** nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;
* Použijte bezpečný název souboru určený aplikací. Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodný název souboru nahraného souboru. &dagger; HTML kódovat nedůvěryhodný název souboru při jeho zobrazení. Například protokolování názvu souboru nebo zobrazení v uživatelském režimu (Razor automaticky kóduje výstup).
* Povolit pouze schválené přípony souborů pro specifikaci návrhu aplikace.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Ověřte, zda jsou na serveru prováděny kontroly na straně klienta. &dagger; Kontroly na straně klienta lze snadno obejít.
* Zkontrolujte velikost nahraného souboru. Nastavte maximální limit velikosti, abyste zabránili velkým nahráváním.&dagger;
* Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, zkontrolujte před nahráním souboru název souboru v databázi nebo fyzickém úložišti.
* **Před uložením souboru spusťte nahraný obsah skener virů a malwaru.**

&dagger;Ukázková aplikace ukazuje přístup, který splňuje kritéria.

> [!WARNING]
> Nahrání škodlivého kódu do systému je často prvním krokem k provedení kódu, který může:
>
> * Zcela získat kontrolu nad systémem.
> * Přetížení systému s výsledkem, že systém havaruje.
> * Ohrožení uživatelských nebo systémových dat.
> * Aplikujte graffiti na veřejné ui.
>
> Informace o snížení oblasti útoku při přijímání souborů od uživatelů naleznete v následujících zdrojích:
>
> * [Neomezené nahrávání souborů](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů zavedeny příslušné ovládací prvky](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [Ověření.](#validation)

## <a name="storage-scenarios"></a>Scénáře úložiště

Mezi běžné možnosti úložiště souborů patří:

* databáze

  * U malých nahrávek souborů je databáze často rychlejší než možnosti fyzického úložiště (systém souborů nebo sdílená síť).
  * Databáze je často pohodlnější než možnosti fyzického úložiště, protože načítání záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek avatara).
  * Databáze je potenciálně levnější než použití služby úložiště dat.

* Fyzické úložiště (systém souborů nebo sdílená síťová složka)

  * Pro nahrávání velkých souborů:
    * Omezení databáze může omezit velikost nahrávání.
    * Fyzické úložiště je často méně ekonomické než úložiště v databázi.
  * Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.
  * Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště. **Nikdy neudělte oprávnění k spuštění.**

* Služba úložiště dat (například [Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)

  * Služby obvykle nabízejí lepší škálovatelnost a odolnost proti místním řešením, která obvykle podléhají jedinému bodu selhání.
  * Služby jsou potenciálně nižší náklady ve scénářích velké infrastruktury úložiště.

  Další informace naleznete [v tématu Úvodní příručka: Vytvoření objektu blob v úložišti objektů pomocí rozhraní .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scénáře nahrávání souborů

Dva obecné přístupy pro nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.

**Vyrovnávací paměti**

Celý soubor je číst <xref:Microsoft.AspNetCore.Http.IFormFile>do , což je c# reprezentace souboru slouží ke zpracování nebo uložení souboru.

Prostředky (disk, paměť) používané nahráváním souborů závisí na počtu a velikosti souběžných nahrávek souborů. Pokud se aplikace pokusí ukládat do vyrovnávací paměti příliš mnoho nahraných, dojde k chybě webu, když dojde místo v paměti nebo na disku. Pokud velikost nebo četnost nahrávání souborů vyčerpává prostředky aplikací, použijte streamování.

> [!NOTE]
> Každý jeden soubor ve vyrovnávací paměti přesahující 64 kB je přesunut z paměti do dočasného souboru na disku.

Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:

* [Fyzické skladování](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [databáze](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streamování**

Soubor je přijat z vícedílného požadavku a přímo zpracován nebo uložen aplikací. Streamování výrazně nezvyšuje výkon. Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.

Streamování velkých souborů je pokryto v sekci [Nahrát velké soubory s datovými proudy.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do fyzického úložiště

Chcete-li nahrát malé soubory, použijte vícedílný formulář nebo vytvořte požadavek POST pomocí JavaScriptu.

Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru *(Pages/BufferedSingleFileUploadPhysical.cshtml* v ukázkové aplikaci):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Následující příklad je obdobou předchozípříklad s tím rozdílem, že:

* JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)se používá k odeslání dat formuláře.
* Není tu žádné potvrzení.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Chcete-li provést formulář POST v jazyce JavaScript pro klienty, kteří [nepodporují načíst rozhraní API](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:

* Použijte Fetch Polyfill (například [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Použijte `XMLHttpRequest`. Příklad:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Aby bylo možné podporovat nahrávání souborů, musí formuláře`enctype`HTML `multipart/form-data`určit typ kódování ( ) aplikace .

Pro `files` vstupní prvek pro podporu nahrávání `multiple` více souborů `<input>` poskytují atribut na prvek:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Jednotlivé soubory nahrané na server lze přistupovat prostřednictvím [model vazby](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>. Ukázková aplikace ukazuje více souborů ve vyrovnávací paměti pro scénáře databáze a fyzického úložiště.

<a name="filename"></a>

> [!WARNING]
> **Nepoužívejte** `FileName` vlastnost jiné <xref:Microsoft.AspNetCore.Http.IFormFile> než pro zobrazení a protokolování. Při zobrazení nebo protokolování html zakóduje název souboru. Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest. Žádosti by měly:
>
> * Odeberte cestu z uživatelem zadaného názvu souboru.
> * Uložte název souboru kódovaný html, který je odstraněn z cesty, pro uživatelské ui nebo protokolování.
> * Vygenerujte nový náhodný název souboru pro úložiště.
>
> Následující kód odebere cestu z názvu souboru:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Dosud poskytnuté příklady neberou v úvahu bezpečnostní aspekty. Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

Při nahrávání souborů pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>vazby modelu a , může metoda akce přijmout:

* Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Všechny následující kolekce, které představují několik souborů:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Vazba odpovídá souborům formuláře podle názvu. Například hodnota `name` HTML `<input type="file" name="formFile">` v aplikaci musí odpovídat`FormFile`vazbě parametru/vlastnosti jazyka C# ( ). Další informace naleznete v [tématu Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.

Následující příklad:

* Smyčky přes jeden nebo více nahraných souborů.
* Použije [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátit úplnou cestu pro soubor, včetně názvu souboru. 
* Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.
* Vrátí celkový počet a velikost odeslaných souborů.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Slouží `Path.GetRandomFileName` ke generování názvu souboru bez cesty. V následujícím příkladu je cesta získána z konfigurace:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Cesta předaná <xref:System.IO.FileStream> *musí* obsahovat název souboru. Pokud není k dispozici název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.

Soubory nahrané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou před zpracováním uloženy do vyrovnávací paměti nebo na disku na serveru. Uvnitř metody akce <xref:Microsoft.AspNetCore.Http.IFormFile> je obsah přístupný <xref:System.IO.Stream>jako . Kromě místního systému souborů lze soubory uložit do sdílené síťové složky nebo do služby úložiště souborů, jako je [například úložiště objektů blob Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Další příklad, který prochází přes více souborů pro nahrávání a používá bezpečné názvy souborů, najdete *v tématu Stránky/BufferedMultipleFileUploadPhysical.cshtml.csv* ukázkové aplikaci.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá <xref:System.IO.IOException> pokud více než 65 535 soubory jsou vytvořeny bez odstranění předchozídočasné soubory. Limit 65 535 souborů je limit pro server. Další informace o tomto limitu pro operační systém Windows naleznete v poznámkách v následujících tématech:
>
> * [Funkce GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do databáze

Chcete-li uložit binární data souborů do <xref:System.Byte> databáze pomocí entity [Framework](/ef/core/index), definujte vlastnost pole na entitě:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Zadejte vlastnost modelu stránky pro <xref:Microsoft.AspNetCore.Http.IFormFile>třídu, která obsahuje :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu. Předchozí příklad používá vlastnost vázaného modelu.

Používá `FileUpload` se ve formuláři Razor Pages:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Pokud je formulář posted na server, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte jej jako bajtové pole v databázi. V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Předchozí příklad je podobný scénáři, který byl ukázán v ukázkové aplikaci:

* *Stránky/BufferedSingleFileUploadDb.cshtml*
* *Stránky/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Při ukládání binárních dat do relačních databází buďte opatrní, protože to může nepříznivě ovlivnit výkon.
>
> Nespoléhejte na `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření ani ji nedůvěřujte. Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.
>
> Uvedené příklady neberou v úvahu aspekty zabezpečení. Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

### <a name="upload-large-files-with-streaming"></a>Nahrávání velkých souborů pomocí streamování

Následující příklad ukazuje, jak používat JavaScript k streamování souboru do akce řadiče. Token antiforgery souboru je generován pomocí vlastního atributu filtru a předán klientovi http záhlaví namísto v těle požadavku. Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem. V rámci akce je obsah formuláře přečten `MultipartReader`pomocí , `MultipartSection`který čte jednotlivé osoby , zpracovává soubor nebo ukládá obsah podle potřeby. Po čtení vícedílných oddílů akce provede vlastní vazbu modelu.

Počáteční odpověď na stránce načte formulář a uloží token antiforgery do souboru cookie (prostřednictvím atributu). `GenerateAntiforgeryTokenCookieAttribute` Atribut používá ASP.NET integrované [antiforgery podpory](xref:security/anti-request-forgery) Core nastavit cookie s tokenem požadavku:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Slouží `DisableFormValueModelBindingAttribute` k zakázání vazby modelu:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

V ukázkové `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` aplikaci a jsou použity jako `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` filtry na modely aplikací stránky a v `Startup.ConfigureServices` použití [Razor Pages konvence](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

Vzhledem k tomu, že vazba modelu nečte formulář, parametry, které jsou vázány z formuláře, se nevážou (dotaz, trasa a záhlaví nadále fungují). Metoda akce pracuje přímo `Request` s vlastností. A `MultipartReader` se používá ke čtení jednotlivých oddílů. Data klíč/hodnota jsou `KeyValueAccumulator`uložena v . Po čtení vícedílných oddílů se `KeyValueAccumulator` obsah těchto oddílů použije k vytvoření sazby dat formuláře s typem modelu.

Kompletní `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utility/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Kompletní `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Ve vzorové aplikaci jsou `FileHelpers.ProcessStreamedFile`kontroly ověření zpracovávány .

## <a name="validation"></a>Ověřování

Ukázková `FileHelpers` aplikace třídy ukazuje několik kontrol <xref:Microsoft.AspNetCore.Http.IFormFile> pro nahrávání souborů do vyrovnávací paměti a datových proudů. Zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> nahrávání souborů ve vyrovnávací paměti v `ProcessFormFile` ukázkové aplikaci naleznete v metodě v souboru *Utilities/FileHelpers.cs.* Zpracování streamovaných souborů naleznete `ProcessStreamedFile` v metodě ve stejném souboru.

> [!WARNING]
> Metody zpracování ověření demonstrované v ukázkové aplikaci neprohledává obsah nahraných souborů. Ve většině produkčních scénářů se v souboru používá rozhraní API pro skener virů a malwaru před zpřístupněním souboru uživatelům nebo jiným systémům.
>
> Přestože ukázka tématu obsahuje funkční příklad technik `FileHelpers` ověření, neimplementujte třídu v produkční aplikaci, pokud:
>
> * Plně pochopit implementaci.
> * Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.
>
> **Nikdy bez rozdílu implementovat bezpečnostní kód v aplikaci bez řešení těchto požadavků.**

### <a name="content-validation"></a>Ověření obsahu

**U nahraného obsahu použijte rozhraní API pro vyhledávání virů a malwaru třetí strany.**

Prohledávání souborů je náročné na serverové prostředky ve scénářích s velkým objemem. Pokud je výkon zpracování požadavků snížen v důsledku prohledávání souborů, zvažte přečtení úlohy skenování na [službu na pozadí](xref:fundamentals/host/hosted-services), případně službu spuštěnou na serveru jiném než server aplikace. Nahrané soubory jsou obvykle uchovávány v karanténě, dokud je nezkontroluje počítačový virus. Když soubor projde, soubor je přesunut do normálního umístění úložiště souborů. Tyto kroky jsou obvykle prováděny ve spojení se záznamem databáze, který označuje stav skenování souboru. Pomocí takového přístupu se aplikace a aplikační server stále zaměřují na odpovědi na požadavky.

### <a name="file-extension-validation"></a>Ověření přípony souboru

Přípona nahraného souboru by měla být zkontrolována podle seznamu povolených rozšíření. Příklad:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Ověření podpisu souboru

Podpis souboru je určen několika prvními bajty na začátku souboru. Tyto bajty lze použít k označení, pokud přípona odpovídá obsahu souboru. Ukázková aplikace kontroluje podpisy souborů pro několik běžných typů souborů. V následujícím příkladu je podpis souboru pro obraz JPEG zkontrolován proti souboru:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Další podpisy souborů naleznete v [databázi podpisů souborů](https://www.filesignatures.net/) a oficiálníspecifikace souborů.

### <a name="file-name-security"></a>Zabezpečení názvu souboru

Nikdy nepoužívejte název souboru dodaného klientem pro uložení souboru do fyzického úložiště. Vytvořte bezpečný název souboru pomocí [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) k vytvoření úplné cesty (včetně názvu souboru) pro dočasné úložiště.

Holicí strojek automaticky kóduje hodnoty vlastností pro zobrazení. Použití následujícího kódu je bezpečné:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Mimo Razor vždy <xref:System.Net.WebUtility.HtmlEncode*> soubor název obsahu z požadavku uživatele.

Mnoho implementací musí obsahovat kontrolu, zda soubor existuje; v opačném případě je soubor přepsán souborem se stejným názvem. Zadejte další logiku, která vyhovuje specifikacím vaší aplikace.

### <a name="size-validation"></a>Ověření velikosti

Omezte velikost nahraných souborů.

V ukázkové aplikaci je velikost souboru omezena na 2 MB (označeno v bajtech). Limit je dodáván prostřednictvím [konfigurace](xref:fundamentals/configuration/index) ze souboru *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

Injektuje se `FileSizeLimit` do `PageModel` tříd:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Pokud velikost souboru překročí limit, soubor je odmítnut:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Shodovat hodnotu atributu názvu s názvem parametru metody POST

Ve formulářích bez břitvy, které POST `FormData` tvoří data nebo používají javascriptpřímo, název zadaný v elementu formuláře nebo `FormData` musí odpovídat názvu parametru v akci řadiče.

V následujícím příkladu:

* Při použití `<input>` prvku `name` je atribut nastaven `battlePlans`na hodnotu :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Při `FormData` použití v Jazyce JavaScript je `battlePlans`název nastaven na hodnotu :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Použijte odpovídající název pro parametr metody C# (`battlePlans`):

* Pro metodu obslužné rutiny stránky Razor Pages s názvem `Upload`:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pro metodu akce řadiče MVC POST:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfigurace serveru a aplikace

### <a name="multipart-body-length-limit"></a>Limit délky vícedílné části těla

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit pro délku každého vícedílného těla. Oddíly formuláře, které <xref:System.IO.InvalidDataException> překračují tento limit, při analýzě vyhovují. Výchozí hodnota je 134 217 728 (128 MB). Přizpůsobení limitu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocí `Startup.ConfigureServices`nastavení v :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.

V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

V aplikaci Razor Pages nebo MVC použijte filtr na model stránky nebo metodu akce:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maximální velikost těla požadavku kestrelu

Pro aplikace hostované kestrel, výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobit limit pomocí [maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) kestrel server možnost:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>se používá k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.

V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

V aplikaci Razor stránky nebo MVC použít filtr na třídu obslužné rutiny stránky nebo metody akce:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

Lze `RequestSizeLimitAttribute` použít také pomocí [`@attribute`](xref:mvc/views/razor#attribute) směrnice Razor:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Ostatní limity kestrelu

Pro aplikace hostované společností Kestrel se mohou vztahovat i na další limity kestrelu:

* [Maximální počet připojení klientů](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Rychlost dat žádostí a odpovědí](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Omezení délky obsahu služby IIS

Výchozí limit požadavku`maxAllowedContentLength`( ) je 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobení limitu v souboru *web.config:*

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Toto nastavení platí pouze pro službu IIS. Chování nedochází ve výchozím nastavení při hostování na Kestrel. Další informace naleznete v [tématu Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Omezení v ASP.NET základního modulu nebo přítomnost modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB. Další informace naleznete [v tématu Nelze nahrát soubor větší než 2 GB velikosti (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Řešení potíží

Níže jsou uvedeny některé běžné problémy při práci s nahráváním souborů a jejich možná řešení.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Při nasazení na server služby IIS nebyla nalezena chyba

Následující chyba označuje, že nahraný soubor přesahuje nakonfigurovanou délku obsahu na serveru:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Další informace o zvýšení limitu naleznete v části [Omezení délky obsahu služby IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Chyba připojení

Chyba připojení a připojení k serveru resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost těla požadavku Kestrel. Další informace naleznete v části [Maximální velikost těla požadavku Kestrel.](#kestrel-maximum-request-body-size) Kestrel klienta omezení připojení může také vyžadovat úpravu.

### <a name="null-reference-exception-with-iformfile"></a>Výjimka nulového odkazu se souborem IFormFile

Pokud řadič přijímá nahrané soubory <xref:Microsoft.AspNetCore.Http.IFormFile> pomocí, `null`ale hodnota je , zkontrolujte, zda formulář HTML určuje `enctype` hodnotu . `multipart/form-data` Pokud tento atribut není nastaven `<form>` na prvek, nahrávání souboru nedojde <xref:Microsoft.AspNetCore.Http.IFormFile> a `null`všechny vázané argumenty jsou . Také zkontrolujte, zda [se pojmenování při nahrávání ve formulářových datech shoduje s pojmenováním aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Stream byl příliš dlouhý

Příklady v tomto tématu spoléhají na <xref:System.IO.MemoryStream> uložení obsahu nahraného souboru. Limit velikosti `MemoryStream` a `int.MaxValue`je . Pokud scénář nahrávání souborů aplikace vyžaduje držení obsahu souboru většího než 50 MB, `MemoryStream` použijte alternativní přístup, který nespoléhá na jeden pro držení obsahu nahraného souboru.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Buďte opatrní, pokud uživatelům poskytujete možnost nahrávat soubory na server. Útočníci se mohou pokusit o:

* Spusťte útoky [odmítnutí služby.](/windows-hardware/drivers/ifs/denial-of-service)
* Nahrajte viry nebo malware.
* Kompromitujte sítě a servery jinými způsoby.

Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:

* Nahrávejte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe na nesystémovou jednotku. Vyhrazené umístění usnadňuje ukládání bezpečnostních omezení pro nahrané soubory. Zakažte oprávnění ke spuštění v umístění pro nahrávání souboru.&dagger;
* **Nezachovávejte** nahrané soubory ve stejném adresářovém stromu jako aplikace.&dagger;
* Použijte bezpečný název souboru určený aplikací. Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodný název souboru nahraného souboru. &dagger; HTML kódovat nedůvěryhodný název souboru při jeho zobrazení. Například protokolování názvu souboru nebo zobrazení v uživatelském režimu (Razor automaticky kóduje výstup).
* Povolit pouze schválené přípony souborů pro specifikaci návrhu aplikace.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Ověřte, zda jsou na serveru prováděny kontroly na straně klienta. &dagger; Kontroly na straně klienta lze snadno obejít.
* Zkontrolujte velikost nahraného souboru. Nastavte maximální limit velikosti, abyste zabránili velkým nahráváním.&dagger;
* Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, zkontrolujte před nahráním souboru název souboru v databázi nebo fyzickém úložišti.
* **Před uložením souboru spusťte nahraný obsah skener virů a malwaru.**

&dagger;Ukázková aplikace ukazuje přístup, který splňuje kritéria.

> [!WARNING]
> Nahrání škodlivého kódu do systému je často prvním krokem k provedení kódu, který může:
>
> * Zcela získat kontrolu nad systémem.
> * Přetížení systému s výsledkem, že systém havaruje.
> * Ohrožení uživatelských nebo systémových dat.
> * Aplikujte graffiti na veřejné ui.
>
> Informace o snížení oblasti útoku při přijímání souborů od uživatelů naleznete v následujících zdrojích:
>
> * [Neomezené nahrávání souborů](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů zavedeny příslušné ovládací prvky](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [Ověření.](#validation)

## <a name="storage-scenarios"></a>Scénáře úložiště

Mezi běžné možnosti úložiště souborů patří:

* databáze

  * U malých nahrávek souborů je databáze často rychlejší než možnosti fyzického úložiště (systém souborů nebo sdílená síť).
  * Databáze je často pohodlnější než možnosti fyzického úložiště, protože načítání záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek avatara).
  * Databáze je potenciálně levnější než použití služby úložiště dat.

* Fyzické úložiště (systém souborů nebo sdílená síťová složka)

  * Pro nahrávání velkých souborů:
    * Omezení databáze může omezit velikost nahrávání.
    * Fyzické úložiště je často méně ekonomické než úložiště v databázi.
  * Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.
  * Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště. **Nikdy neudělte oprávnění k spuštění.**

* Služba úložiště dat (například [Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)

  * Služby obvykle nabízejí lepší škálovatelnost a odolnost proti místním řešením, která obvykle podléhají jedinému bodu selhání.
  * Služby jsou potenciálně nižší náklady ve scénářích velké infrastruktury úložiště.

  Další informace naleznete [v tématu Úvodní příručka: Vytvoření objektu blob v úložišti objektů pomocí rozhraní .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet). Toto téma <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , ale lze <xref:System.IO.FileStream> uložit do úložiště objektů <xref:System.IO.Stream>blob při práci s .

## <a name="file-upload-scenarios"></a>Scénáře nahrávání souborů

Dva obecné přístupy pro nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.

**Vyrovnávací paměti**

Celý soubor je číst <xref:Microsoft.AspNetCore.Http.IFormFile>do , což je c# reprezentace souboru slouží ke zpracování nebo uložení souboru.

Prostředky (disk, paměť) používané nahráváním souborů závisí na počtu a velikosti souběžných nahrávek souborů. Pokud se aplikace pokusí ukládat do vyrovnávací paměti příliš mnoho nahraných, dojde k chybě webu, když dojde místo v paměti nebo na disku. Pokud velikost nebo četnost nahrávání souborů vyčerpává prostředky aplikací, použijte streamování.

> [!NOTE]
> Každý jeden soubor ve vyrovnávací paměti přesahující 64 kB je přesunut z paměti do dočasného souboru na disku.

Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:

* [Fyzické skladování](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [databáze](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streamování**

Soubor je přijat z vícedílného požadavku a přímo zpracován nebo uložen aplikací. Streamování výrazně nezvyšuje výkon. Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.

Streamování velkých souborů je pokryto v sekci [Nahrát velké soubory s datovými proudy.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do fyzického úložiště

Chcete-li nahrát malé soubory, použijte vícedílný formulář nebo vytvořte požadavek POST pomocí JavaScriptu.

Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru *(Pages/BufferedSingleFileUploadPhysical.cshtml* v ukázkové aplikaci):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Následující příklad je obdobou předchozípříklad s tím rozdílem, že:

* JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)se používá k odeslání dat formuláře.
* Není tu žádné potvrzení.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Chcete-li provést formulář POST v jazyce JavaScript pro klienty, kteří [nepodporují načíst rozhraní API](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:

* Použijte Fetch Polyfill (například [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Použijte `XMLHttpRequest`. Příklad:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Aby bylo možné podporovat nahrávání souborů, musí formuláře`enctype`HTML `multipart/form-data`určit typ kódování ( ) aplikace .

Pro `files` vstupní prvek pro podporu nahrávání `multiple` více souborů `<input>` poskytují atribut na prvek:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Jednotlivé soubory nahrané na server lze přistupovat prostřednictvím [model vazby](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>. Ukázková aplikace ukazuje více souborů ve vyrovnávací paměti pro scénáře databáze a fyzického úložiště.

<a name="filename2"></a>

> [!WARNING]
> **Nepoužívejte** `FileName` vlastnost jiné <xref:Microsoft.AspNetCore.Http.IFormFile> než pro zobrazení a protokolování. Při zobrazení nebo protokolování html zakóduje název souboru. Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest. Žádosti by měly:
>
> * Odeberte cestu z uživatelem zadaného názvu souboru.
> * Uložte název souboru kódovaný html, který je odstraněn z cesty, pro uživatelské ui nebo protokolování.
> * Vygenerujte nový náhodný název souboru pro úložiště.
>
> Následující kód odebere cestu z názvu souboru:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Dosud poskytnuté příklady neberou v úvahu bezpečnostní aspekty. Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

Při nahrávání souborů pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>vazby modelu a , může metoda akce přijmout:

* Jeden <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Všechny následující kolekce, které představují několik souborů:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Vazba odpovídá souborům formuláře podle názvu. Například hodnota `name` HTML `<input type="file" name="formFile">` v aplikaci musí odpovídat`FormFile`vazbě parametru/vlastnosti jazyka C# ( ). Další informace naleznete v [tématu Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.

Následující příklad:

* Smyčky přes jeden nebo více nahraných souborů.
* Použije [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátit úplnou cestu pro soubor, včetně názvu souboru. 
* Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.
* Vrátí celkový počet a velikost odeslaných souborů.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Slouží `Path.GetRandomFileName` ke generování názvu souboru bez cesty. V následujícím příkladu je cesta získána z konfigurace:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Cesta předaná <xref:System.IO.FileStream> *musí* obsahovat název souboru. Pokud není k dispozici název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.

Soubory nahrané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou před zpracováním uloženy do vyrovnávací paměti nebo na disku na serveru. Uvnitř metody akce <xref:Microsoft.AspNetCore.Http.IFormFile> je obsah přístupný <xref:System.IO.Stream>jako . Kromě místního systému souborů lze soubory uložit do sdílené síťové složky nebo do služby úložiště souborů, jako je [například úložiště objektů blob Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Další příklad, který prochází přes více souborů pro nahrávání a používá bezpečné názvy souborů, najdete *v tématu Stránky/BufferedMultipleFileUploadPhysical.cshtml.csv* ukázkové aplikaci.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá <xref:System.IO.IOException> pokud více než 65 535 soubory jsou vytvořeny bez odstranění předchozídočasné soubory. Limit 65 535 souborů je limit pro server. Další informace o tomto limitu pro operační systém Windows naleznete v poznámkách v následujících tématech:
>
> * [Funkce GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Nahrání malých souborů s vazbou modelu ve vyrovnávací paměti do databáze

Chcete-li uložit binární data souborů do <xref:System.Byte> databáze pomocí entity [Framework](/ef/core/index), definujte vlastnost pole na entitě:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Zadejte vlastnost modelu stránky pro <xref:Microsoft.AspNetCore.Http.IFormFile>třídu, která obsahuje :

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu. Předchozí příklad používá vlastnost vázaného modelu.

Používá `FileUpload` se ve formuláři Razor Pages:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Pokud je formulář posted na server, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte jej jako bajtové pole v databázi. V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Předchozí příklad je podobný scénáři, který byl ukázán v ukázkové aplikaci:

* *Stránky/BufferedSingleFileUploadDb.cshtml*
* *Stránky/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Při ukládání binárních dat do relačních databází buďte opatrní, protože to může nepříznivě ovlivnit výkon.
>
> Nespoléhejte na `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření ani ji nedůvěřujte. Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.
>
> Uvedené příklady neberou v úvahu aspekty zabezpečení. Další informace jsou uvedeny v následujících částech a [v ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

### <a name="upload-large-files-with-streaming"></a>Nahrávání velkých souborů pomocí streamování

Následující příklad ukazuje, jak používat JavaScript k streamování souboru do akce řadiče. Token antiforgery souboru je generován pomocí vlastního atributu filtru a předán klientovi http záhlaví namísto v těle požadavku. Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem. V rámci akce je obsah formuláře přečten `MultipartReader`pomocí , `MultipartSection`který čte jednotlivé osoby , zpracovává soubor nebo ukládá obsah podle potřeby. Po čtení vícedílných oddílů akce provede vlastní vazbu modelu.

Počáteční odpověď na stránce načte formulář a uloží token antiforgery do souboru cookie (prostřednictvím atributu). `GenerateAntiforgeryTokenCookieAttribute` Atribut používá ASP.NET integrované [antiforgery podpory](xref:security/anti-request-forgery) Core nastavit cookie s tokenem požadavku:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Slouží `DisableFormValueModelBindingAttribute` k zakázání vazby modelu:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

V ukázkové `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` aplikaci a jsou použity jako `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` filtry na modely aplikací stránky a v `Startup.ConfigureServices` použití [Razor Pages konvence](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Vzhledem k tomu, že vazba modelu nečte formulář, parametry, které jsou vázány z formuláře, se nevážou (dotaz, trasa a záhlaví nadále fungují). Metoda akce pracuje přímo `Request` s vlastností. A `MultipartReader` se používá ke čtení jednotlivých oddílů. Data klíč/hodnota jsou `KeyValueAccumulator`uložena v . Po čtení vícedílných oddílů se `KeyValueAccumulator` obsah těchto oddílů použije k vytvoření sazby dat formuláře s typem modelu.

Kompletní `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utility/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Kompletní `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Ve vzorové aplikaci jsou `FileHelpers.ProcessStreamedFile`kontroly ověření zpracovávány .

## <a name="validation"></a>Ověřování

Ukázková `FileHelpers` aplikace třídy ukazuje několik kontrol <xref:Microsoft.AspNetCore.Http.IFormFile> pro nahrávání souborů do vyrovnávací paměti a datových proudů. Zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> nahrávání souborů ve vyrovnávací paměti v `ProcessFormFile` ukázkové aplikaci naleznete v metodě v souboru *Utilities/FileHelpers.cs.* Zpracování streamovaných souborů naleznete `ProcessStreamedFile` v metodě ve stejném souboru.

> [!WARNING]
> Metody zpracování ověření demonstrované v ukázkové aplikaci neprohledává obsah nahraných souborů. Ve většině produkčních scénářů se v souboru používá rozhraní API pro skener virů a malwaru před zpřístupněním souboru uživatelům nebo jiným systémům.
>
> Přestože ukázka tématu obsahuje funkční příklad technik `FileHelpers` ověření, neimplementujte třídu v produkční aplikaci, pokud:
>
> * Plně pochopit implementaci.
> * Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.
>
> **Nikdy bez rozdílu implementovat bezpečnostní kód v aplikaci bez řešení těchto požadavků.**

### <a name="content-validation"></a>Ověření obsahu

**U nahraného obsahu použijte rozhraní API pro vyhledávání virů a malwaru třetí strany.**

Prohledávání souborů je náročné na serverové prostředky ve scénářích s velkým objemem. Pokud je výkon zpracování požadavků snížen v důsledku prohledávání souborů, zvažte přečtení úlohy skenování na [službu na pozadí](xref:fundamentals/host/hosted-services), případně službu spuštěnou na serveru jiném než server aplikace. Nahrané soubory jsou obvykle uchovávány v karanténě, dokud je nezkontroluje počítačový virus. Když soubor projde, soubor je přesunut do normálního umístění úložiště souborů. Tyto kroky jsou obvykle prováděny ve spojení se záznamem databáze, který označuje stav skenování souboru. Pomocí takového přístupu se aplikace a aplikační server stále zaměřují na odpovědi na požadavky.

### <a name="file-extension-validation"></a>Ověření přípony souboru

Přípona nahraného souboru by měla být zkontrolována podle seznamu povolených rozšíření. Příklad:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Ověření podpisu souboru

Podpis souboru je určen několika prvními bajty na začátku souboru. Tyto bajty lze použít k označení, pokud přípona odpovídá obsahu souboru. Ukázková aplikace kontroluje podpisy souborů pro několik běžných typů souborů. V následujícím příkladu je podpis souboru pro obraz JPEG zkontrolován proti souboru:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Další podpisy souborů naleznete v [databázi podpisů souborů](https://www.filesignatures.net/) a oficiálníspecifikace souborů.

### <a name="file-name-security"></a>Zabezpečení názvu souboru

Nikdy nepoužívejte název souboru dodaného klientem pro uložení souboru do fyzického úložiště. Vytvořte bezpečný název souboru pomocí [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) k vytvoření úplné cesty (včetně názvu souboru) pro dočasné úložiště.

Holicí strojek automaticky kóduje hodnoty vlastností pro zobrazení. Použití následujícího kódu je bezpečné:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Mimo Razor vždy <xref:System.Net.WebUtility.HtmlEncode*> soubor název obsahu z požadavku uživatele.

Mnoho implementací musí obsahovat kontrolu, zda soubor existuje; v opačném případě je soubor přepsán souborem se stejným názvem. Zadejte další logiku, která vyhovuje specifikacím vaší aplikace.

### <a name="size-validation"></a>Ověření velikosti

Omezte velikost nahraných souborů.

V ukázkové aplikaci je velikost souboru omezena na 2 MB (označeno v bajtech). Limit je dodáván prostřednictvím [konfigurace](xref:fundamentals/configuration/index) ze souboru *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

Injektuje se `FileSizeLimit` do `PageModel` tříd:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Pokud velikost souboru překročí limit, soubor je odmítnut:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Shodovat hodnotu atributu názvu s názvem parametru metody POST

Ve formulářích bez břitvy, které POST `FormData` tvoří data nebo používají javascriptpřímo, název zadaný v elementu formuláře nebo `FormData` musí odpovídat názvu parametru v akci řadiče.

V následujícím příkladu:

* Při použití `<input>` prvku `name` je atribut nastaven `battlePlans`na hodnotu :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Při `FormData` použití v Jazyce JavaScript je `battlePlans`název nastaven na hodnotu :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Použijte odpovídající název pro parametr metody C# (`battlePlans`):

* Pro metodu obslužné rutiny stránky Razor Pages s názvem `Upload`:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pro metodu akce řadiče MVC POST:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfigurace serveru a aplikace

### <a name="multipart-body-length-limit"></a>Limit délky vícedílné části těla

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>nastaví limit pro délku každého vícedílného těla. Oddíly formuláře, které <xref:System.IO.InvalidDataException> překračují tento limit, při analýzě vyhovují. Výchozí hodnota je 134 217 728 (128 MB). Přizpůsobení limitu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocí `Startup.ConfigureServices`nastavení v :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.

V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

V aplikaci Razor Pages nebo MVC použijte filtr na model stránky nebo metodu akce:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maximální velikost těla požadavku kestrelu

Pro aplikace hostované kestrel, výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobit limit pomocí [maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) kestrel server možnost:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>se používá k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.

V aplikaci Razor Pages použijte filtr `Startup.ConfigureServices`s [konvencí](xref:razor-pages/razor-pages-conventions) v aplikaci :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

V aplikaci Razor stránky nebo MVC použít filtr na třídu obslužné rutiny stránky nebo metody akce:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Ostatní limity kestrelu

Pro aplikace hostované společností Kestrel se mohou vztahovat i na další limity kestrelu:

* [Maximální počet připojení klientů](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Rychlost dat žádostí a odpovědí](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Omezení délky obsahu služby IIS

Výchozí limit požadavku`maxAllowedContentLength`( ) je 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobení limitu v souboru *web.config:*

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Toto nastavení platí pouze pro službu IIS. Chování nedochází ve výchozím nastavení při hostování na Kestrel. Další informace naleznete v [tématu Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Omezení v ASP.NET základního modulu nebo přítomnost modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB. Další informace naleznete [v tématu Nelze nahrát soubor větší než 2 GB velikosti (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Řešení potíží

Níže jsou uvedeny některé běžné problémy při práci s nahráváním souborů a jejich možná řešení.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Při nasazení na server služby IIS nebyla nalezena chyba

Následující chyba označuje, že nahraný soubor přesahuje nakonfigurovanou délku obsahu na serveru:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Další informace o zvýšení limitu naleznete v části [Omezení délky obsahu služby IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Chyba připojení

Chyba připojení a připojení k serveru resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost těla požadavku Kestrel. Další informace naleznete v části [Maximální velikost těla požadavku Kestrel.](#kestrel-maximum-request-body-size) Kestrel klienta omezení připojení může také vyžadovat úpravu.

### <a name="null-reference-exception-with-iformfile"></a>Výjimka nulového odkazu se souborem IFormFile

Pokud řadič přijímá nahrané soubory <xref:Microsoft.AspNetCore.Http.IFormFile> pomocí, `null`ale hodnota je , zkontrolujte, zda formulář HTML určuje `enctype` hodnotu . `multipart/form-data` Pokud tento atribut není nastaven `<form>` na prvek, nahrávání souboru nedojde <xref:Microsoft.AspNetCore.Http.IFormFile> a `null`všechny vázané argumenty jsou . Také zkontrolujte, zda [se pojmenování při nahrávání ve formulářových datech shoduje s pojmenováním aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Stream byl příliš dlouhý

Příklady v tomto tématu spoléhají na <xref:System.IO.MemoryStream> uložení obsahu nahraného souboru. Limit velikosti `MemoryStream` a `int.MaxValue`je . Pokud scénář nahrávání souborů aplikace vyžaduje držení obsahu souboru většího než 50 MB, `MemoryStream` použijte alternativní přístup, který nespoléhá na jeden pro držení obsahu nahraného souboru.

::: moniker-end


## <a name="additional-resources"></a>Další zdroje

* [Neomezené nahrávání souborů](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Zabezpečení Azure: Rámec zabezpečení: Ověření vstupu | Skutečnosti snižující závažnost rizika](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Vzory návrhu Azure Cloud: Vzor klíče zajištitel](/azure/architecture/patterns/valet-key)
