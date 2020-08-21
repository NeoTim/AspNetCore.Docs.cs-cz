---
title: Nahrání souborů v ASP.NET Core
author: rick-anderson
description: Jak používat vazbu modelu a streamování k nahrávání souborů v ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: 6ff78b26e8e2363cf6c54ebb2a392f390fb2995c
ms.sourcegitcommit: cd412a44f26cb416ceb348fc0a1ccc9a6e9ca73e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720276"
---
# <a name="upload-files-in-aspnet-core"></a>Nahrání souborů v ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Rutgerá](https://github.com/rutix) zaplavení

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní. Útočníci se můžou pokusit:

* Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).
* Nahrání virů nebo malwaru
* Narušit sítě a servery jinými způsoby.

Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:

* Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky. Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory. Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;
* Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;
* Použijte název bezpečného souboru určený aplikací. Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru. Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).
* Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.
* Ověřte velikost nahraného souboru. Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;
* Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.
* **Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**

&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.

> [!WARNING]
> Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:
>
> * Zcela získá kontrolu nad systémem.
> * Přetížit systém s výsledkem, že dojde k chybě systému.
> * Napadnout data uživatelů nebo systémových dat.
> * Použijte graffiti pro veřejné uživatelské rozhraní.
>
> Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:
>
> * [Neomezená nahrávání souboru](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .

## <a name="storage-scenarios"></a>Scénáře úložiště

Mezi běžné možnosti úložiště pro soubory patří:

* Databáze

  * U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).
  * Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).
  * Databáze je potenciálně levnější než použití služby úložiště dat.

* Fyzické úložiště (systém souborů nebo síťová sdílená složka)

  * Pro nahrávání velkých souborů:
    * Omezení databáze mohou omezit velikost nahrávání.
    * Fyzické úložiště je často méně hospodárné než úložiště v databázi.
  * Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.
  * Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště. **Nikdy neudělujte oprávnění EXECUTE.**

* Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.
  * Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.

  Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scénáře nahrávání souborů

Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.

**Do vyrovnávací paměti**

Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.

Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů. Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku. Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.

> [!NOTE]
> Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.

Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:

* [Fyzické úložiště](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Databáze](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streamování**

Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci. Streamování nijak významně nezvyšuje výkon. Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.

Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště

Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.

Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):

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

Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:

* K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).
* Neexistuje žádné ověření.

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

Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:

* Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).
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

Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .

Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> . Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.

<a name="filename"></a>

> [!WARNING]
> Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování. Při zobrazení nebo protokolování je název souboru kódován HTML. Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest. Aplikace by měly:
>
> * Odeberte cestu z názvu souboru zadaného uživatelem.
> * Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.
> * Vygenerujte nový náhodný název souboru pro úložiště.
>
> Následující kód odstraní cestu z názvu souboru:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Zde uvedené příklady neberou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:

* Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Kterákoli z následujících kolekcí, které reprezentují několik souborů:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Vazba odpovídá souborům formuláře podle názvu. Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ). Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .

Následující příklad:

* Projde jedním nebo více nahranými soubory.
* Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru. 
* Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.
* Vrátí celkový počet nahraných souborů a jejich velikost.

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

Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty. V následujícím příkladu je cesta získána z konfigurace:

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

Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru. Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.

Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti. V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> . Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .

> [!WARNING]
> [Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů. Limit 65 535 souborů je omezen na server. Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:
>
> * [GetTempFileNameA – funkce](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze

Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu. Předchozí příklad používá vlastnost vázaného modelu.

`FileUpload`Je použit ve Razor formuláři stránky:

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

Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi. V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:

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

Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.
>
> Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření. `FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.
>
> Uvedené příklady nevezmou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

### <a name="upload-large-files-with-streaming"></a>Nahrávání velkých souborů pomocí streamování

Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru. Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti. Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem. V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby. Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.

Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu). Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou). Metoda Action pracuje přímo s `Request` vlastností. `MultipartReader`Slouží ke čtení jednotlivých oddílů. Data klíč/hodnota jsou uložena v `KeyValueAccumulator` . Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.

Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Nástroje/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Ověřování

Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání. Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* . Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.

> [!WARNING]
> Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů. Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.
>
> I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:
>
> * Plně rozumíte implementaci.
> * Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.
>
> **Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**

### <a name="content-validation"></a>Ověření obsahu

**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**

Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem. Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace. Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí. Když soubor projde, soubor se přesune do normálního umístění úložiště souborů. Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru. Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.

### <a name="file-extension-validation"></a>Ověření přípony souboru

Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření. Příklad:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Ověření podpisu souboru

Podpis souboru se určuje na prvních několika bajtech na začátku souboru. Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru. Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů. V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:

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

Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.

### <a name="file-name-security"></a>Zabezpečení názvu souboru

Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště. Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.

Razor Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení. Následující kód je bezpečné použít:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.

Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem. Poskytněte další logiku pro splnění specifikací vaší aplikace.

### <a name="size-validation"></a>Ověřování velikosti

Omezte velikost nahraných souborů.

V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech). Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Třída je vložena do `PageModel` tříd:

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

Když velikost souboru překročí limit, soubor se odmítne:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Porovnává hodnotu atributu name s parametrem název metody POST

V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.

V následujícím příkladu:

* Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :

* Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pro metodu akce po kontroléru MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfigurace serveru a aplikace

### <a name="multipart-body-length-limit"></a>Omezení délky těla částí

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části. Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze. Výchozí hodnota je 134 217 728 (128 MB). Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.

V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel maximální velikost textu požadavku

Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.

V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Lze také použít pomocí [`@attribute`](xref:mvc/views/razor#attribute) Razor direktivy:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Další omezení Kestrel

Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:

* [Maximální počet připojení klientů](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Sazby za data požadavků a odpovědí](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobte limit v `web.config` souboru. V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`Nastavení platí pouze pro službu IIS. Další informace najdete v tématu [omezení `<requestLimits>` požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

## <a name="troubleshoot"></a>Řešení potíží

Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Při nasazení na server služby IIS se nenašla chyba.

Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Další informace najdete v části [IIS](#iis) .

### <a name="connection-failure"></a>Chyba připojení

Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel. Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) . Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.

### <a name="null-reference-exception-with-iformfile"></a>Výjimka odkazu s hodnotou null s IFormFile

Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` . Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` . Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Proud je příliš dlouhý.

Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru. Omezení velikosti `MemoryStream` je `int.MaxValue` . Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní. Útočníci se můžou pokusit:

* Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).
* Nahrání virů nebo malwaru
* Narušit sítě a servery jinými způsoby.

Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:

* Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky. Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory. Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;
* Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;
* Použijte název bezpečného souboru určený aplikací. Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru. Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).
* Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.
* Ověřte velikost nahraného souboru. Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;
* Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.
* **Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**

&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.

> [!WARNING]
> Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:
>
> * Zcela získá kontrolu nad systémem.
> * Přetížit systém s výsledkem, že dojde k chybě systému.
> * Napadnout data uživatelů nebo systémových dat.
> * Použijte graffiti pro veřejné uživatelské rozhraní.
>
> Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:
>
> * [Neomezená nahrávání souboru](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .

## <a name="storage-scenarios"></a>Scénáře úložiště

Mezi běžné možnosti úložiště pro soubory patří:

* Databáze

  * U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).
  * Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).
  * Databáze je potenciálně levnější než použití služby úložiště dat.

* Fyzické úložiště (systém souborů nebo síťová sdílená složka)

  * Pro nahrávání velkých souborů:
    * Omezení databáze mohou omezit velikost nahrávání.
    * Fyzické úložiště je často méně hospodárné než úložiště v databázi.
  * Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.
  * Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště. **Nikdy neudělujte oprávnění EXECUTE.**

* Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.
  * Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.

  Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scénáře nahrávání souborů

Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.

**Do vyrovnávací paměti**

Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.

Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů. Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku. Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.

> [!NOTE]
> Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.

Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:

* [Fyzické úložiště](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Databáze](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streamování**

Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci. Streamování nijak významně nezvyšuje výkon. Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.

Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště

Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.

Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):

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

Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:

* K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).
* Neexistuje žádné ověření.

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

Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:

* Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).
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

Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .

Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> . Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.

<a name="filename"></a>

> [!WARNING]
> Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování. Při zobrazení nebo protokolování je název souboru kódován HTML. Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest. Aplikace by měly:
>
> * Odeberte cestu z názvu souboru zadaného uživatelem.
> * Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.
> * Vygenerujte nový náhodný název souboru pro úložiště.
>
> Následující kód odstraní cestu z názvu souboru:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Zde uvedené příklady neberou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:

* Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Kterákoli z následujících kolekcí, které reprezentují několik souborů:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Vazba odpovídá souborům formuláře podle názvu. Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ). Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .

Následující příklad:

* Projde jedním nebo více nahranými soubory.
* Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru. 
* Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.
* Vrátí celkový počet nahraných souborů a jejich velikost.

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

Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty. V následujícím příkladu je cesta získána z konfigurace:

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

Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru. Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.

Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti. V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> . Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .

> [!WARNING]
> [Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů. Limit 65 535 souborů je omezen na server. Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:
>
> * [GetTempFileNameA – funkce](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze

Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu. Předchozí příklad používá vlastnost vázaného modelu.

`FileUpload`Je použit ve Razor formuláři stránky:

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

Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi. V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:

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

Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.
>
> Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření. `FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.
>
> Uvedené příklady nevezmou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

### <a name="upload-large-files-with-streaming"></a>Nahrávání velkých souborů pomocí streamování

Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru. Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti. Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem. V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby. Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.

Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu). Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou). Metoda Action pracuje přímo s `Request` vlastností. `MultipartReader`Slouží ke čtení jednotlivých oddílů. Data klíč/hodnota jsou uložena v `KeyValueAccumulator` . Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.

Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Nástroje/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Ověřování

Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání. Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* . Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.

> [!WARNING]
> Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů. Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.
>
> I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:
>
> * Plně rozumíte implementaci.
> * Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.
>
> **Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**

### <a name="content-validation"></a>Ověření obsahu

**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**

Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem. Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace. Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí. Když soubor projde, soubor se přesune do normálního umístění úložiště souborů. Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru. Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.

### <a name="file-extension-validation"></a>Ověření přípony souboru

Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření. Příklad:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Ověření podpisu souboru

Podpis souboru se určuje na prvních několika bajtech na začátku souboru. Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru. Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů. V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:

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

Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.

### <a name="file-name-security"></a>Zabezpečení názvu souboru

Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště. Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.

Razor Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení. Následující kód je bezpečné použít:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.

Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem. Poskytněte další logiku pro splnění specifikací vaší aplikace.

### <a name="size-validation"></a>Ověřování velikosti

Omezte velikost nahraných souborů.

V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech). Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Třída je vložena do `PageModel` tříd:

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

Když velikost souboru překročí limit, soubor se odmítne:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Porovnává hodnotu atributu name s parametrem název metody POST

V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.

V následujícím příkladu:

* Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :

* Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pro metodu akce po kontroléru MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfigurace serveru a aplikace

### <a name="multipart-body-length-limit"></a>Omezení délky těla částí

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části. Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze. Výchozí hodnota je 134 217 728 (128 MB). Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.

V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel maximální velikost textu požadavku

Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.

V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Lze také použít pomocí [`@attribute`](xref:mvc/views/razor#attribute) Razor direktivy:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Další omezení Kestrel

Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:

* [Maximální počet připojení klientů](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Sazby za data požadavků a odpovědí](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobte limit v `web.config` souboru. V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`Nastavení platí pouze pro službu IIS. Další informace najdete v tématu [omezení `<requestLimits>` požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Zvyšte maximální velikost textu požadavku pro požadavek HTTP nastavením <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> v `Startup.ConfigureServices` . V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Další informace naleznete v tématu <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Řešení potíží

Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Při nasazení na server služby IIS se nenašla chyba.

Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Další informace najdete v části [IIS](#iis) .

### <a name="connection-failure"></a>Chyba připojení

Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel. Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) . Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.

### <a name="null-reference-exception-with-iformfile"></a>Výjimka odkazu s hodnotou null s IFormFile

Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` . Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` . Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Proud je příliš dlouhý.

Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru. Omezení velikosti `MemoryStream` je `int.MaxValue` . Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní. Útočníci se můžou pokusit:

* Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).
* Nahrání virů nebo malwaru
* Narušit sítě a servery jinými způsoby.

Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:

* Nahrajte soubory do vyhrazené oblasti pro nahrávání souborů, nejlépe do nesystémové jednotky. Vyhrazené umístění usnadňuje omezení zabezpečení pro nahrané soubory. Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru.&dagger;
* Neuchovávat nahrané soubory ve stejném stromu adresářů jako aplikace. **not**&dagger;
* Použijte název bezpečného souboru určený aplikací. Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodného názvu nahraného souboru. &dagger; HTML při zobrazení kódování názvu nedůvěryhodného souboru. Například protokolování názvu souboru nebo zobrazení v uživatelském rozhraní ( Razor Automatické kódování HTML kódování).
* Povolte pro specifikaci návrhu aplikace jenom schválené přípony souborů.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Ověřte, zda jsou na serveru provedeny kontroly na straně klienta. &dagger; Kontroly na straně klienta je snadné obejít.
* Ověřte velikost nahraného souboru. Nastavte limit maximální velikosti, aby se zabránilo velkým nahrávání.&dagger;
* Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.
* **Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**

&dagger;Ukázková aplikace předvádí přístup, který splňuje kritéria.

> [!WARNING]
> Nahrávání škodlivého kódu do systému je často prvním krokem ke spuštění kódu, který může:
>
> * Zcela získá kontrolu nad systémem.
> * Přetížit systém s výsledkem, že dojde k chybě systému.
> * Napadnout data uživatelů nebo systémových dat.
> * Použijte graffiti pro veřejné uživatelské rozhraní.
>
> Informace o omezení oblasti útoku při přijímání souborů uživateli najdete v následujících zdrojích informací:
>
> * [Neomezená nahrávání souboru](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpečení Azure: Ujistěte se, že jsou při přijímání souborů od uživatelů k dismístě správné ovládací prvky.](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Další informace o implementaci bezpečnostních opatření, včetně příkladů z ukázkové aplikace, najdete v části [ověření](#validation) .

## <a name="storage-scenarios"></a>Scénáře úložiště

Mezi běžné možnosti úložiště pro soubory patří:

* Databáze

  * U malých nahrávání souborů je databáze často rychlejší než možnosti fyzického úložiště (systému souborů nebo síťového sdílení).
  * Databáze je často pohodlnější než možnosti fyzického úložiště, protože načtení záznamu databáze pro uživatelská data může současně poskytovat obsah souboru (například obrázek miniatury).
  * Databáze je potenciálně levnější než použití služby úložiště dat.

* Fyzické úložiště (systém souborů nebo síťová sdílená složka)

  * Pro nahrávání velkých souborů:
    * Omezení databáze mohou omezit velikost nahrávání.
    * Fyzické úložiště je často méně hospodárné než úložiště v databázi.
  * Fyzické úložiště je potenciálně levnější než použití služby úložiště dat.
  * Proces aplikace musí mít oprávnění ke čtení a zápisu do umístění úložiště. **Nikdy neudělujte oprávnění EXECUTE.**

* Služba úložiště dat (například [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Služby obvykle nabízejí vylepšenou škálovatelnost a odolnost proti místním řešením, které obvykle podléhají jednomu bodu selhání.
  * Služby jsou potenciálně nižší náklady ve scénářích infrastruktury velkých úložišť.

  Další informace najdete v tématu [rychlý Start: použití .NET k vytvoření objektu BLOB v úložišti objektů](/azure/storage/blobs/storage-quickstart-blobs-dotnet). Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> dá se použít k uložení do <xref:System.IO.FileStream> úložiště objektů BLOB při práci s <xref:System.IO.Stream> .

## <a name="file-upload-scenarios"></a>Scénáře nahrávání souborů

Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.

**Do vyrovnávací paměti**

Celý soubor je načten do <xref:Microsoft.AspNetCore.Http.IFormFile> , což je reprezentace souboru, který se používá ke zpracování nebo uložení souboru v jazyce C#.

Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů. Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku. Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.

> [!NOTE]
> Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.

Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:

* [Fyzické úložiště](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Databáze](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streamování**

Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci. Streamování nijak významně nezvyšuje výkon. Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.

Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště

Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.

Následující příklad ukazuje použití Razor formuláře stránky k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):

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

Následující příklad je podobný předchozímu příkladu s tím rozdílem, že:

* K odeslání dat formuláře se používá JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)).
* Neexistuje žádné ověření.

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

Chcete-li provést příspěvek formuláře v jazyce JavaScript pro klienty, kteří [nepodporují rozhraní API pro načítání](https://caniuse.com/#feat=fetch), použijte jeden z následujících přístupů:

* Použijte načtenou výplň (například [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).
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

Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování ( `enctype` ) `multipart/form-data` .

Pro `files` vstupní element, který podporuje nahrávání více souborů, poskytněte `multiple` atribut `<input>` elementu:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> . Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.

<a name="filename2"></a>

> [!WARNING]
> Nepoužívejte **not** `FileName` vlastnost <xref:Microsoft.AspNetCore.Http.IFormFile> jinou než pro zobrazení a protokolování. Při zobrazení nebo protokolování je název souboru kódován HTML. Útočník může poskytnout škodlivý název souboru, včetně úplných cest nebo relativních cest. Aplikace by měly:
>
> * Odeberte cestu z názvu souboru zadaného uživatelem.
> * Uložte název souboru s příponou PATH s kódováním HTML pro uživatelské rozhraní nebo protokolování.
> * Vygenerujte nový náhodný název souboru pro úložiště.
>
> Následující kód odstraní cestu z názvu souboru:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Zde uvedené příklady neberou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

Při nahrávání souborů pomocí vazby modelu a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:

* Jedna <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Kterákoli z následujících kolekcí, které reprezentují několik souborů:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Seznamu](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Vazba odpovídá souborům formuláře podle názvu. Například hodnota HTML v se `name` `<input type="file" name="formFile">` musí shodovat s parametrem nebo vazbou vlastnosti jazyka C# ( `FormFile` ). Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .

Následující příklad:

* Projde jedním nebo více nahranými soubory.
* Pomocí [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vrátí úplnou cestu k souboru, včetně názvu souboru. 
* Uloží soubory do místního systému souborů pomocí názvu souboru generovaného aplikací.
* Vrátí celkový počet nahraných souborů a jejich velikost.

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

Slouží `Path.GetRandomFileName` k vygenerování názvu souboru bez cesty. V následujícím příkladu je cesta získána z konfigurace:

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

Cesta předaná do <xref:System.IO.FileStream> *musí* zahrnovat název souboru. Pokud není zadán název souboru, <xref:System.UnauthorizedAccessException> je vyvolána za běhu.

Soubory odeslané pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> techniky jsou v paměti nebo na disku na serveru před zpracováním uloženy do vyrovnávací paměti. V rámci metody Action je <xref:Microsoft.AspNetCore.Http.IFormFile> obsah přístupný jako <xref:System.IO.Stream> . Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .

> [!WARNING]
> [Cesta. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku, <xref:System.IO.IOException> Pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů. Limit 65 535 souborů je omezen na server. Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:
>
> * [GetTempFileNameA – funkce](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze

Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v <xref:System.Byte> entitě vlastnost Array:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile> :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> lze ji použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu. Předchozí příklad používá vlastnost vázaného modelu.

`FileUpload`Je použit ve Razor formuláři stránky:

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

Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> ho do datového proudu a uložte ho jako pole bajtů v databázi. V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:

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

Předchozí příklad je podobný scénáři, který je znázorněný v ukázkové aplikaci:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Při ukládání binárních dat do relačních databází buďte opatrní, protože to může mít nepříznivý vliv na výkon.
>
> Nespoléhá se na nebo důvěřujete `FileName` vlastnosti <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření. `FileName`Vlastnost by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.
>
> Uvedené příklady nevezmou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

### <a name="upload-large-files-with-streaming"></a>Nahrávání velkých souborů pomocí streamování

Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru. Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti. Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem. V rámci akce je obsah formuláře čten pomocí `MultipartReader` , který čte každou jednotlivou osobu `MultipartSection` , zpracovává soubor nebo ukládá obsah podle potřeby. Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.

Počáteční odpověď stránky načte formulář a uloží token proti padělání do cookie (prostřednictvím `GenerateAntiforgeryTokenCookieAttribute` atributu). Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení cookie s tokenem požadavku:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`Slouží k zakázání vazby modelu:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

V ukázkové aplikaci a používá `GenerateAntiforgeryTokenCookieAttribute` se `DisableFormValueModelBindingAttribute` jako filtr pro modelové aplikace stránky `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` [ Razor konvencích použití stránek](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou). Metoda Action pracuje přímo s `Request` vlastností. `MultipartReader`Slouží ke čtení jednotlivých oddílů. Data klíč/hodnota jsou uložena v `KeyValueAccumulator` . Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` používá pro svázání dat formuláře s typem modelu.

Úplná `StreamingController.UploadDatabase` metoda pro streamování do databáze s EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Nástroje/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Úplná `StreamingController.UploadPhysical` metoda pro streamování do fyzického umístění:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

V ukázkové aplikaci jsou kontroly ověřování zpracovávány nástrojem `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Ověřování

Třída ukázkové aplikace `FileHelpers` ukazuje několik kontrol ukládání souborů do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a datových proudů při nahrávání. Informace o zpracování <xref:Microsoft.AspNetCore.Http.IFormFile> ukládání souborů do vyrovnávací paměti v ukázkové aplikaci naleznete v `ProcessFormFile` metodě v souboru *Utilities/Helper. cs* . Pro zpracování streamované soubory se podívejte na `ProcessStreamedFile` metodu ve stejném souboru.

> [!WARNING]
> Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů. Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.
>
> I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte `FileHelpers` třídu v produkční aplikaci, pokud:
>
> * Plně rozumíte implementaci.
> * Upravte implementaci podle potřeby pro prostředí a specifikace aplikace.
>
> **Nikdy nepoužívejte nerozlišený kód zabezpečení v aplikaci bez nutnosti řešit tyto požadavky.**

### <a name="content-validation"></a>Ověření obsahu

**Pro nahraný obsah použijte rozhraní API pro kontrolu virů a malwaru třetí strany.**

Prohledávání souborů je náročné na prostředky serveru ve scénářích s vysokým objemem. Pokud dojde ke snížení výkonu zpracování požadavků z důvodu kontroly souborů, zvažte přesměrování práce skenování na službu na [pozadí](xref:fundamentals/host/hosted-services), případně služby spuštěné na serveru, který se liší od serveru aplikace. Nahrané soubory se obvykle uchovávají v oblasti v karanténě, dokud je kontrola virů na pozadí nevrátí. Když soubor projde, soubor se přesune do normálního umístění úložiště souborů. Tyto kroky se obvykle provádí ve spojení s databázovým záznamem, který indikuje stav kontroly souboru. Při použití takového přístupu zůstane aplikace a Server App zaměřené na reakci na požadavky.

### <a name="file-extension-validation"></a>Ověření přípony souboru

Přípona nahraného souboru by měla být zaškrtnutá na seznamu povolených rozšíření. Příklad:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Ověření podpisu souboru

Podpis souboru se určuje na prvních několika bajtech na začátku souboru. Tyto bajty lze použít k určení, zda přípona odpovídá obsahu souboru. Ukázková aplikace zkontroluje podpisy souborů pro několik běžných typů souborů. V následujícím příkladu se pro tento soubor kontroluje podpis souboru obrázku JPEG:

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

Další signatury souborů získáte v dokumentaci [signatury souborů](https://www.filesignatures.net/) a oficiálních souborů.

### <a name="file-name-security"></a>Zabezpečení názvu souboru

Nikdy nepoužívejte název souboru dodaný klientem pro uložení souboru do fyzického úložiště. Vytvořte bezpečný název souboru pro soubor pomocí [cesty. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) nebo [cesty. GetTempFileName](xref:System.IO.Path.GetTempFileName*) a vytvořte úplnou cestu (včetně názvu souboru) pro dočasné úložiště.

Razor Automatické kódování HTML kóduje hodnoty vlastností pro zobrazení. Následující kód je bezpečné použít:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Mimo Razor , vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.

Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem. Poskytněte další logiku pro splnění specifikací vaší aplikace.

### <a name="size-validation"></a>Ověřování velikosti

Omezte velikost nahraných souborů.

V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech). Limit se poskytuje prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) z *appsettings.jsv* souboru:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Třída je vložena do `PageModel` tříd:

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

Když velikost souboru překročí limit, soubor se odmítne:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Porovnává hodnotu atributu name s parametrem název metody POST

V Razor neformulářích, které publikují data formuláře nebo přímo využívají JavaScript `FormData` , název zadaný v prvku formuláře nebo `FormData` musí odpovídat názvu parametru v akci kontroleru.

V následujícím příkladu:

* Při použití `<input>` elementu `name` je atribut nastaven na hodnotu `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Pro parametr metody jazyka C# () použijte stejný název `battlePlans` :

* Pro Razor metodu obslužné rutiny stránky stránky s názvem `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pro metodu akce po kontroléru MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfigurace serveru a aplikace

### <a name="multipart-body-length-limit"></a>Omezení délky těla částí

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části. Oddíly formuláře, které překračují toto omezení, vyvolávají <xref:System.IO.InvalidDataException> při analýze. Výchozí hodnota je 134 217 728 (128 MB). Upravte limit pomocí <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastavení v `Startup.ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.

V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :

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

V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel maximální velikost textu požadavku

Pro aplikace hostované v Kestrel je výchozí maximální velikost textu požadavku 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobte limit pomocí možnosti serveru [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.

V Razor aplikaci Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v nástroji `Startup.ConfigureServices` :

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

V Razor aplikaci Pages nebo aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Další omezení Kestrel

Pro aplikace hostované v Kestrel se můžou vztahovat další omezení Kestrel:

* [Maximální počet připojení klientů](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Sazby za data požadavků a odpovědí](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Výchozí limit počtu požadavků ( `maxAllowedContentLength` ) je 30 000 000 bajtů, což je přibližně 28,6 MB. Přizpůsobte limit v `web.config` souboru. V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

`maxAllowedContentLength`Nastavení platí pouze pro službu IIS. Další informace najdete v tématu [omezení `<requestLimits>` požadavků ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Zvyšte maximální velikost textu požadavku pro požadavek HTTP nastavením <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> v `Startup.ConfigureServices` . V následujícím příkladu je limit nastaven na 50 MB (52 428 800 bajtů):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Další informace naleznete v tématu <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Řešení potíží

Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Při nasazení na server služby IIS se nenašla chyba.

Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Další informace najdete v části [IIS](#iis) .

### <a name="connection-failure"></a>Chyba připojení

Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel. Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) . Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.

### <a name="null-reference-exception-with-iformfile"></a>Výjimka odkazu s hodnotou null s IFormFile

Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile> , ale hodnota je `null` , potvrďte, že formulář HTML určuje `enctype` hodnotu `multipart/form-data` . Pokud tento atribut není nastaven na `<form>` elementu, odeslání souboru neproběhne a jakékoli vázané <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty jsou `null` . Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Proud je příliš dlouhý.

Příklady v tomto tématu <xref:System.IO.MemoryStream> se spoléhají na uchovávání obsahu nahraného souboru. Omezení velikosti `MemoryStream` je `int.MaxValue` . Pokud scénář nahrávání souborů aplikace vyžaduje, aby byl obsah souboru větší než 50 MB, použijte alternativní přístup, který nespoléhá na jednu z nich `MemoryStream` pro uchovávání obsahu nahraného souboru.

::: moniker-end


## <a name="additional-resources"></a>Další zdroje

* [Vyprazdňování žádosti o připojení HTTP](xref:fundamentals/servers/kestrel#http11-request-draining)
* [Neomezená nahrávání souboru](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Zabezpečení Azure: rámec zabezpečení: ověření vstupu | Hrozeb](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Vzory návrhu cloudu Azure: vzor osobního Key](/azure/architecture/patterns/valet-key)
