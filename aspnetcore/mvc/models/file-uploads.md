---
title: Nahrání souborů v ASP.NET Core
author: guardrex
description: Jak používat vazbu modelu a streamování k nahrávání souborů v ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/30/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 46cdb517742431a7f2a93155564832d586233e68
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925121"
---
# <a name="upload-files-in-aspnet-core"></a>Nahrání souborů v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/)a [Rutger](https://github.com/rutix)

ASP.NET Core podporuje nahrávání jednoho nebo více souborů pomocí vazby modelu ve vyrovnávací paměti pro menší soubory a streamování bez vyrovnávací paměti pro větší soubory.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Aspekty zabezpečení

Pokud chcete uživatelům poskytnout možnost nahrávat soubory na server, buďte opatrní. Útočníci se můžou pokusit:

* Vykoná útok [DOS (Denial of Service](/windows-hardware/drivers/ifs/denial-of-service) ).
* Nahrání virů nebo malwaru
* Narušit sítě a servery jinými způsoby.

Bezpečnostní kroky, které snižují pravděpodobnost úspěšného útoku, jsou:

* Nahrajte soubory do vyhrazené oblasti pro nahrání souborů v systému, nejlépe pro nesystémovou jednotku. Použití vyhrazeného umístění usnadňuje omezení zabezpečení pro nahrané soubory. Zakažte oprávnění EXECUTE pro umístění pro nahrání souboru. &dagger;
* Neuchovávat nahrané soubory ve stejném adresářovém stromu jako aplikace. &dagger;
* Použijte název bezpečného souboru určený aplikací. Nepoužívejte název souboru poskytnutý uživatelem nebo nedůvěryhodný název souboru nahraného souboru. &dagger; Chcete-li zobrazit nedůvěryhodný název souboru v uživatelském rozhraní nebo v hlášení zprávy, zakódováním HTML hodnotu.
* Povoluje pouze konkrétní sadu schválených přípon souborů. &dagger;
* Chcete-li zabránit uživateli v nahrávání maskovaného souboru, ověřte podpis formátu souboru. &dagger; Například nepovolíte uživateli odeslat soubor *. exe* s příponou *. txt* .
* Ověřte, zda jsou na serveru provedeny také kontroly na straně klienta. &dagger; Je snadné pro obejití kontroly na straně klienta.
* Ověřte velikost nahraného souboru a zabraňte nahrávání, které jsou větší, než se očekávalo. &dagger;
* Pokud by soubory neměly být přepsány nahraným souborem se stejným názvem, před nahráním souboru ověřte název souboru proti databázi nebo fyzickému úložišti.
* **Před uložením souboru spusťte v nahraném obsahu skener virů nebo malwaru.**

Ukázková aplikace &dagger;The ukazuje přístup, který splňuje kritéria.

> [!WARNING]
> Nahrává se škodlivý kód do systému je často prvním krokem při provádění kódu, který můžete:
>
> * Zcela získá kontrolu nad systémem.
> * Přetížit systém s výsledkem, že dojde k chybě systému.
> * Ohrozit data uživatele nebo systému.
> * Použijte graffiti pro veřejné uživatelské rozhraní.
>
> Informace o omezení možností útoku, při přijetí soubory od uživatelů najdete v následujících zdrojích:
>
> * [Nahrání souboru bez omezení](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * @no__t – zabezpečení 0Azure: Zajistěte, aby byly při přijímání souborů od uživatelů zavedeny příslušné ovládací prvky @ no__t-0.

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

  Další informace najdete v tématu [rychlý Start: Pomocí .NET vytvořte objekt BLOB v úložišti objektů @ no__t-0. Téma ukazuje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> lze použít k uložení <xref:System.IO.FileStream> do úložiště objektů BLOB při práci s <xref:System.IO.Stream>.

## <a name="file-upload-scenarios"></a>Scénáře nahrávání souborů

Dva obecné přístupy k nahrávání souborů jsou ukládání do vyrovnávací paměti a streamování.

**Do vyrovnávací paměti**

Celý soubor se přečte do <xref:Microsoft.AspNetCore.Http.IFormFile>, což je C# reprezentace souboru, který se používá ke zpracování nebo uložení souboru.

Prostředky (disk, paměť) používané při nahrávání souborů závisí na počtu a velikosti souběžných nahrávání souborů. Pokud se aplikace pokusí do vyrovnávací paměti příliš mnoho nahrávání, dojde k selhání lokality, když dojde k vynechání paměti nebo místa na disku. Pokud velikost nebo frekvence nahrávání souborů vyčerpá prostředky aplikace, použijte streamování.

> [!NOTE]
> Z paměti do dočasného souboru na disku se přesune libovolný soubor s vyrovnávací pamětí větší než 64 KB.

Ukládání malých souborů do vyrovnávací paměti je popsáno v následujících částech tohoto tématu:

* [Fyzické úložiště](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streamování**

Soubor se přijímá z požadavku na více částí a přímo se zpracovává nebo ukládá v aplikaci. Streamování nijak významně nezvyšuje výkon. Streamování snižuje nároky na paměť nebo místo na disku při nahrávání souborů.

Streamování velkých souborů je zahrnuté v části [nahrávání velkých souborů pomocí streamování](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Nahrávání malých souborů s vazbou modelu ve vyrovnávací paměti na fyzické úložiště

Pro nahrání malých souborů použijte formulář s více částmi nebo sestavte požadavek POST pomocí JavaScriptu.

Následující příklad ukazuje použití formuláře Razor Pages k nahrání jednoho souboru (*Pages/BufferedSingleFileUploadPhysical. cshtml* do ukázkové aplikace):

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

Aby bylo možné podporovat nahrávání souborů, musí formuláře HTML určovat typ kódování (`enctype`) `multipart/form-data`.

V případě, že vstupní prvek `files` podporuje nahrávání více souborů, zadejte atribut `multiple` pro element `<input>`:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Jednotlivé soubory nahrané na server jsou k dispozici prostřednictvím [vazby modelu](xref:mvc/models/model-binding) pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>. Ukázková aplikace ukazuje více ukládání souborů do vyrovnávací paměti pro scénáře databáze a fyzických úložišť.

> [!WARNING]
> Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření. Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování hodnoty HTML.
>
> Zde uvedené příklady neberou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

Při nahrávání souborů pomocí vazeb modelů a <xref:Microsoft.AspNetCore.Http.IFormFile> může metoda Action přijmout:

* Jeden @no__t – 0.
* Kterákoli z následujících kolekcí, které reprezentují několik souborů:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Seznam](xref:System.Collections.Generic.List`1)\< @ no__t-2 @ no__t-3

> [!NOTE]
> Vazba odpovídá souborům formuláře podle názvu. Například hodnota HTML `name` v `<input type="file" name="formFile">` se musí shodovat s C# parametrem nebo vazbou vlastnosti (`FormFile`). Další informace naleznete v části [název atributu matched na název parametru metody post](#match-name-attribute-value-to-parameter-name-of-post-method) .

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

    return Ok(new { count = files.Count, size, filePath });
}
```

K vygenerování názvu souboru bez cesty použijte `Path.GetRandomFileName`. V následujícím příkladu je cesta získána z konfigurace:

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

Cesta předaná <xref:System.IO.FileStream> *musí* zahrnovat název souboru. Pokud není zadán název souboru, je při spuštění vyvolána hodnota <xref:System.UnauthorizedAccessException>.

Soubory odeslané pomocí techniky <xref:Microsoft.AspNetCore.Http.IFormFile> jsou uloženy do vyrovnávací paměti nebo na disku na serveru před zpracováním. V rámci metody Action je obsah <xref:Microsoft.AspNetCore.Http.IFormFile> přístupný jako <xref:System.IO.Stream>. Kromě místního systému souborů je možné soubory ukládat do síťové sdílené složky nebo do služby úložiště souborů, jako je [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Další příklad, který projde několik souborů pro nahrání a používá bezpečné názvy souborů, najdete v ukázkové aplikaci v části *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* .

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) vyvolá výjimku <xref:System.IO.IOException>, pokud jsou vytvořeny více než 65 535 souborů bez odstranění předchozích dočasných souborů. Limit 65 535 souborů je omezen na server. Další informace o tomto limitu pro operační systém Windows najdete v následujících tématech:
>
> * [GetTempFileNameA – funkce](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Nahrávání malých souborů s vazbou modelu s vyrovnávací pamětí do databáze

Chcete-li uložit data binárního souboru do databáze pomocí [Entity Framework](/ef/core/index), definujte v entitě vlastnost pole <xref:System.Byte>:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Zadejte vlastnost modelu stránky pro třídu, která obsahuje <xref:Microsoft.AspNetCore.Http.IFormFile>:

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> lze použít přímo jako parametr metody akce nebo jako vlastnost vázaného modelu. Předchozí příklad používá vlastnost vázaného modelu.

Ve formuláři Razor Pages se používá `FileUpload`:

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

Když je formulář publikovaný na serveru, zkopírujte <xref:Microsoft.AspNetCore.Http.IFormFile> do datového proudu a uložte ho jako pole bajtů v databázi. V následujícím příkladu `_dbContext` ukládá kontext databáze aplikace:

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
> Nespoléhá se ani na nedůvěryhodnou vlastnost `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez ověření. Vlastnost `FileName` by měla být použita pouze pro účely zobrazení a pouze po kódování HTML.
>
> Uvedené příklady nevezmou ohled na zabezpečení. Další informace jsou k dispozici v následujících částech a [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Důležité informace o zabezpečení](#security-considerations)
> * [Ověřování](#validation)

### <a name="upload-large-files-with-streaming"></a>Nahrávání velkých souborů pomocí streamování

Následující příklad ukazuje, jak použít JavaScript ke streamování souboru do akce kontroleru. Token proti padělání souboru se generuje pomocí vlastního atributu filtru a předává se do hlaviček protokolu HTTP klienta místo v textu žádosti. Vzhledem k tomu, že metoda akce zpracovává nahraná data přímo, vazba modelu formuláře je zakázána jiným vlastním filtrem. V rámci akce je obsah formuláře čten pomocí `MultipartReader`, který čte každou jednotlivou `MultipartSection`, zpracovává soubor nebo ukládá obsah podle potřeby. Po načtení oddílů s více částmi provede akce vlastní vazbu modelu.

Počáteční odpověď stránky načte formulář a uloží token proti padělání do souboru cookie (prostřednictvím atributu `GenerateAntiforgeryTokenCookieAttribute`). Atribut používá ASP.NET Core integrovanou [podporu proti padělání](xref:security/anti-request-forgery) pro nastavení souboru cookie s tokenem žádosti:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

@No__t-0 se používá k zakázání vazby modelu:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

V ukázkové aplikaci `GenerateAntiforgeryTokenCookieAttribute` a `DisableFormValueModelBindingAttribute` se aplikují jako filtry pro modely stránky aplikace `/StreamedSingleFileUploadDb` a `/StreamedSingleFileUploadPhysical` v `Startup.ConfigureServices` pomocí [konvencí Razor Pages](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Vzhledem k tomu, že vazba modelu nepřečte formulář, parametry, které jsou svázané z formuláře, se nezobrazují (budou pokračovat v práci s dotazem, trasou a hlavičkou). Metoda Action funguje přímo s vlastností `Request`. Pro čtení jednotlivých oddílů se používá `MultipartReader`. Data klíč/hodnota jsou uložena v `KeyValueAccumulator`. Po načtení oddílů s více částmi se obsah `KeyValueAccumulator` použije pro svázání dat formuláře s typem modelu.

Kompletní metoda `StreamingController.UploadDatabase` pro streamování do databáze s EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

Úplná metoda `StreamingController.UploadPhysical` pro streamování do fyzického umístění:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

V ukázkové aplikaci jsou kontroly ověřování zpracovávány `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Ověřování

Třída `FileHelpers` ukázkové aplikace ukazuje několik kontrol uložených do vyrovnávací paměti <xref:Microsoft.AspNetCore.Http.IFormFile> a streamovaná nahrávání souborů. Pro zpracování @no__t ukládání souborů s vyrovnávací pamětí v ukázkové aplikaci si přečtěte část `ProcessFormFile` v souboru *Utilities/Helper. cs* . Pro zpracování streamované soubory si Projděte metodu `ProcessStreamedFile` ve stejném souboru.

> [!WARNING]
> Metody zpracování ověřování, které jsou znázorněné v ukázkové aplikaci, nekontrolují obsah nahraných souborů. Ve většině produkčních scénářů se v souboru používá rozhraní API pro skenování virů nebo malwaru, než je soubor dostupný uživatelům nebo jiným systémům.
>
> I když ukázka tématu poskytuje pracovní příklad technik ověřování, Neimplementujte třídu `FileHelpers` v produkční aplikaci, pokud:
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

Razor automaticky kóduje hodnoty vlastností pro zobrazení. Následující kód je bezpečné použít:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Mimo Razor, vždy <xref:System.Net.WebUtility.HtmlEncode*> obsah názvu souboru z požadavku uživatele.

Mnoho implementací musí zahrnovat kontrolu, že soubor existuje. v opačném případě je soubor přepsán souborem se stejným názvem. Poskytněte další logiku pro splnění specifikací vaší aplikace.

### <a name="size-validation"></a>Ověřování velikosti

Omezte velikost nahraných souborů.

V ukázkové aplikaci je velikost souboru omezená na 2 MB (uvedené v bajtech). Limit je zadán prostřednictvím [Konfigurace](xref:fundamentals/configuration/index) ze souboru *appSettings. JSON* :

```json
{
  "FileSizeLimit": 2097152
}
```

@No__t-0 je vložen do tříd `PageModel`:

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

V nestandardních formulářích, které odesílají data formuláře nebo přímo využívají @no__t JavaScriptu, se název zadaný v prvku formuláře nebo `FormData` musí shodovat s názvem parametru v akci kontroleru.

V následujícím příkladu:

* Při použití elementu `<input>` je atribut `name` nastaven na hodnotu `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Při použití `FormData` v jazyce JavaScript je název nastaven na hodnotu `battlePlans`:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Pro parametr C# metody použijte stejný název (`battlePlans`):

* Pro Razor Pages metodu obslužné rutiny stránky s názvem `Upload`:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Pro metodu akce po kontroléru MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfigurace serveru a aplikace

### <a name="multipart-body-length-limit"></a>Omezení délky těla částí

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> nastaví limit délky jednotlivých částí části. Oddíly formuláře, které překračují toto omezení, vyvolávají při analýze <xref:System.IO.InvalidDataException>. Výchozí hodnota je 134 217 728 (128 MB). Upravte limit pomocí nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> v `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> slouží k nastavení <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pro jednu stránku nebo akci.

V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:

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

V aplikaci Razor Pages nebo aplikaci MVC použijte filtr na model stránky nebo metodu akce:

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

::: moniker range=">= aspnetcore-3.0"

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> slouží k nastavení [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) pro jednu stránku nebo akci.

V aplikaci Razor Pages použijte filtr s [konvencí](xref:razor-pages/razor-pages-conventions) v `Startup.ConfigureServices`:

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

V aplikaci se stránkami Razor nebo v aplikaci MVC použijte filtr na třídu obslužné rutiny stránky nebo na metodu akce:

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

### <a name="iis-content-length-limit"></a>Omezení délky obsahu služby IIS

Výchozí limit počtu požadavků (`maxAllowedContentLength`) je 30 000 000 bajtů, což je přibližně 28.6 MB. Upravte limit v souboru *Web. config* :

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

Toto nastavení platí pouze pro službu IIS. K tomuto chování nedochází ve výchozím nastavení při hostování v Kestrel. Další informace najdete v tématu [omezení požadavků \<requestLimits >](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Omezení v modulu ASP.NET Core nebo přítomnosti modulu filtrování požadavků služby IIS mohou omezit nahrávání na 2 nebo 4 GB. Další informace najdete v tématu [nelze odeslat soubor o velikosti větší než 2 GB (ASPNET/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Řešení potíží

Níže jsou uvedeny některé běžné problémy, které se vyskytly při práci s nahráváním souborů a jejich možnými řešeními.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Při nasazení na server služby IIS se nenašla chyba.

Následující chyba znamená, že nahraný soubor překračuje délku nakonfigurovaného obsahu serveru:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Další informace o zvýšení limitu najdete v části [omezení délky obsahu služby IIS](#iis-content-length-limit) .

### <a name="connection-failure"></a>Chyba připojení

Chyba připojení a připojení k serveru pro resetování pravděpodobně znamená, že nahraný soubor překračuje maximální velikost textu požadavku Kestrel. Další informace najdete v části [Kestrel maximální velikost textu požadavku](#kestrel-maximum-request-body-size) . Omezení připojení klientů Kestrel mohou také vyžadovat úpravu.

### <a name="null-reference-exception-with-iformfile"></a>Výjimka odkazu s hodnotou null s IFormFile

Pokud kontroler přijímá odeslané soubory pomocí <xref:Microsoft.AspNetCore.Http.IFormFile>, ale hodnota je `null`, potvrďte, že formulář HTML určuje hodnotu `enctype` `multipart/form-data`. Pokud tento atribut není nastaven u elementu `<form>`, odeslání souboru neproběhne a všechny vázané argumenty <xref:Microsoft.AspNetCore.Http.IFormFile> jsou `null`. Ujistěte se také, že [nahrávání názvů v datech formuláře odpovídá pojmenování aplikace](#match-name-attribute-value-to-parameter-name-of-post-method).

## <a name="additional-resources"></a>Další zdroje

* [Nahrání souboru bez omezení](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* @no__t – zabezpečení 0Azure: Rámec zabezpečení: Ověřování vstupu | Zmírnění rizika @ no__t-0
* @no__t – 0Azure vzory návrhu cloudu: Osobního Key vzor @ no__t-0
