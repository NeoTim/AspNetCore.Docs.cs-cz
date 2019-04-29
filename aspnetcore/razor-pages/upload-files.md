---
title: Nahrání souborů do stránky v ASP.NET Core Razor
author: guardrex
description: Zjistěte, jak k nahrání souborů do stránky Razor v ASP.NET Core s využitím třídy FileUpload.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 11/10/2018
ms.custom: mvc, seodec18
uid: razor-pages/upload-files
ms.openlocfilehash: a12653e5dc99ad731de63e151af6dbdbe38ffefc
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165219"
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a>Nahrání souborů do stránky v ASP.NET Core Razor

Podle [Luke Latham](https://github.com/guardrex)

Toto téma staví na [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) v <xref:tutorials/razor-pages/razor-pages-start>.

Toto téma ukazuje, jak použít jednoduchý model vazby k nahrání souborů, což funguje dobře pro nahrávání malých souborů. Informace o datových proudů velkých souborů, najdete v části [nahrávání velkých souborů pomocí streamování](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).

V následujících krocích se funkci odesílání souborů plán video přidá do ukázkové aplikace. Plán video představuje `Schedule` třídy. Třída zahrnuje dvě verze plánu. Jedna verze je poskytováno zákazníkům, `PublicSchedule`. Jiné verze se používá pro zaměstnance společnosti `PrivateSchedule`. Každá verze je odeslán jako samostatný soubor. Tento kurz ukazuje, jak provést dvě nahrávání souborů ze stránky s jeden příspěvek na server.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Aspekty zabezpečení

Upozornění musí být provedeny, když zároveň uživatelům poskytují možnost k nahrání souborů do serveru. Útočníci se dá provádět [útok DoS](/windows-hardware/drivers/ifs/denial-of-service) a dalších útoků na systém. Některé kroky zabezpečení, které sníží pravděpodobnost úspěšného útoku, že jsou:

* Nahrání souborů do oblasti nahrávání souboru vyhrazené systému, takže je jednodušší a stanovit bezpečnostní opatření u odeslaný obsah. Když umožňující nahrávání souborů, ujistěte se, která oprávnění ke spouštění v umístění nahrávání jsou zakázána.
* Použijte soubor bezpečný název určit aplikace, nikoli ze vstupu uživatele nebo název souboru uloženého souboru.
* Povolte pouze konkrétní sadu přípon souborů schválené.
* Ověřte, že jsou provedeny kontroly na straně klienta na serveru. Je snadné pro obejití kontroly na straně klienta.
* Zkontrolujte velikost nahrávání a zakázat nahrávání větší, než se očekávalo.
* Spusťte vyhledávání virů a malwaru na odeslaný obsah.

> [!WARNING]
> Nahrává se škodlivý kód do systému je často prvním krokem při provádění kódu, který můžete:
> * Úplné převzetí systému.
> * Přetížení systému s výsledkem, který systém zcela selže.
> * Ohrozit data uživatele nebo systému.
> * Graffiti platí pro veřejné rozhraní.

## <a name="add-a-fileupload-class"></a>Přidejte třídu FileUpload

Vytvoření stránky Razor pro zpracování pár nahrávání souborů. Přidat `FileUpload` třídy, který je vázán na stránce získat data plánu. Klikněte pravým tlačítkem myši *modely* složky. Vyberte **přidat** > **třídy**. Název třídy **FileUpload** a přidejte následující vlastnosti:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

Třída nemá vlastnost pro název plánu a vlastnost pro každý dvě verze plán. Všechny tři vlastnosti jsou povinné a název musí být dlouhý 3 až 60 znaků.

## <a name="add-a-helper-method-to-upload-files"></a>Přidejte pomocnou metodu k nahrání souborů

Aby se zabránilo duplicitě kód pro zpracování souborů odeslané plán, je třeba nejprve přidáte statickou pomocnou metodu. Vytvoření *nástroje* složky v aplikaci a přidejte *FileHelpers.cs* soubor s následujícím obsahem. Pomocná metoda `ProcessFormFile`, přebírá [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) a [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) a vrátí řetězec obsahující velikosti a obsahu souboru. Typ obsahu a délka jsou kontrolovány. Pokud soubor není úspěšný ověření, chyba je přidána do `ModelState`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a>Uložte soubor na disk

Ukázková aplikace ukládá nahraných souborů do pole databáze. Chcete-li uložit soubor na disk, použijte [FileStream](/dotnet/api/system.io.filestream). V následujícím příkladu se zkopíruje soubor ukládaná společností `FileUpload.UploadPublicSchedule` k `FileStream` v `OnPostAsync` metoda. `FileStream` Zapíše soubor na disk na `<PATH-AND-FILE-NAME>` k dispozici:

```csharp
public async Task<IActionResult> OnPostAsync()
{
    // Perform an initial check to catch FileUpload class attribute violations.
    if (!ModelState.IsValid)
    {
        return Page();
    }

    var filePath = "<PATH-AND-FILE-NAME>";

    using (var fileStream = new FileStream(filePath, FileMode.Create))
    {
        await FileUpload.UploadPublicSchedule.CopyToAsync(fileStream);
    }

    return RedirectToPage("./Index");
}
```

Pracovní proces musí mít oprávnění k zápisu do umístění určeného proměnnou `filePath`.

> [!NOTE]
> `filePath` *Musí* zahrnovat název souboru. Pokud není zadaný název souboru, [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) je vyvolána výjimka za běhu.

> [!WARNING]
> Nikdy neměli zachovat nahrané soubory ve stromové struktuře stejného adresáře jako aplikace.
>
> Vzorový kód poskytuje žádné serverové ochranu proti nahrávání škodlivých souborů. Informace o omezení možností útoku, při přijetí soubory od uživatelů najdete v následujících zdrojích:
>
> * [Nahrání souboru bez omezení](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [Zabezpečení Azure: Ujistěte se, že odpovídající ovládací prvky jsou na místě při přijetí soubory od uživatelů](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a>Uložte soubor do úložiště objektů Blob v Azure

Nahrát obsah souboru do úložiště objektů Blob v Azure, najdete v článku [Začínáme s Azure Blob Storage pomocí .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs). Téma ukazuje, jak používat [UploadFromStream](/dotnet/api/microsoft.windowsazure.storage.file.cloudfile.uploadfromstreamasync) uložte [FileStream](/dotnet/api/system.io.filestream) do úložiště objektů blob.

## <a name="add-the-schedule-class"></a>Přidat třídu plán

Klikněte pravým tlačítkem myši *modely* složky. Vyberte **přidat** > **třídy**. Název třídy **plán** a přidejte následující vlastnosti:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

Tato třída používá `Display` a `DisplayFormat` atributy, které vytvářejí popisné názvy a formátování při vykreslování dat plánu.

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a>Aktualizace RazorPagesMovieContext

Zadejte `DbSet` v `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) pro plány:

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a>Aktualizace MovieContext

Zadejte `DbSet` v `MovieContext` (*Models/MovieContext.cs*) pro plány:

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a>Přidat plán tabulku do databáze

Otevřete konzoly Správce balíčků (PMC): **Nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

![PMC nabídky](upload-files/_static/pmc.png)

V konzole PMC spusťte následující příkazy. Přidat tyto příkazy `Schedule` tabulky v databázi:

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a>Přidat stránky Razor odesílání souborů

V *stránky* složku, vytvořte *plány* složky. V *plány* složku, vytvořte stránku s názvem *Index.cshtml* pro nahrávání plánu s následujícím obsahem:

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

Každý formulář skupinou  **\<popisek >** , který zobrazí název každé vlastnosti třídy. `Display` Atributů `FileUpload` nabízí model zobrazované hodnoty popisků. Například `UploadPublicSchedule` zobrazovaného názvu vlastnosti se nastaví pomocí `[Display(Name="Public Schedule")]` a proto zobrazí "Veřejný plán" v popisku při vykreslení formuláři.

Každá skupina formuláře zahrnuje ověření  **\<span >**. Uživatelský vstup nesplňuje-li nastavit atributy vlastnosti `FileUpload` třídy nebo zda má některý `ProcessFormFile` metoda soubor ověřování selže, modelu se nepodařilo ověřit. Pokud selže ověření modelu se vykreslí užitečné ověřovací zprávu pro uživatele. Například `Title` vlastnost je opatřen poznámkou `[Required]` a `[StringLength(60, MinimumLength = 3)]`. Pokud se uživateli nepodaří zadat název, obdrží zprávu s oznámením, že je vyžadována hodnota. Pokud uživatel zadá hodnotu menší než tři znaky nebo více než 60 znaků, obdrží zprávu s oznámením, že hodnota má nesprávnou délku. Pokud soubor uvedený, který nemá žádný obsah, zobrazí se zpráva označující, že soubor je prázdný.

## <a name="add-the-page-model"></a>Přidání modelu stránky

Přidat model stránky (*Index.cshtml.cs*) k *plány* složky:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

Model stránky (`IndexModel` v *Index.cshtml.cs*) vytvoří vazbu `FileUpload` třídy:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

Seznam plánů používá model (`IList<Schedule>`) Chcete-li zobrazit plány, které jsou uloženy v databázi na stránce:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

Při načtení stránky s `OnGetAsync`, `Schedules` je vyplněný z databáze a sloužící ke generování tabulku HTML načíst plánů:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

Při odeslání formuláře k serveru, `ModelState` je zaškrtnuté políčko. Pokud není platný, `Schedule` znovu sestaví, a na stránce vykresluje se s jeden nebo více ověřovacích zpráv s informacemi o tom, proč se nezdařilo ověření stránky. Pokud je platný, `FileUpload` vlastnosti jsou používány v *OnPostAsync* k dokončení nahrávání souboru pro dvě verze plán a vytvořit nový `Schedule` objekt pro uložení data. Plán se pak uloží do databáze:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a>Propojit samotné nahrávání souborů stránky Razor

Otevřít *Pages/Shared/_Layout.cshtml* a přidat odkaz na navigačním panelu na stránce plány:

```cshtml
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li><a asp-page="/Index">Home</a></li>
        <li><a asp-page="/Schedules/Index">Schedules</a></li>
        <li><a asp-page="/About">About</a></li>
        <li><a asp-page="/Contact">Contact</a></li>
    </ul>
</div>
```

## <a name="add-a-page-to-confirm-schedule-deletion"></a>Přidat stránku pro potvrzení odstranění plánu

Když uživatel klikne na odstranění plánu, je k dispozici možnost zrušit operaci. Přidat stránku potvrzení odstranění (*Delete.cshtml*) k *plány* složky:

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

Model stránky (*Delete.cshtml.cs*) načte jeden plán identifikovaný `id` v datech trasy žádost. Přidat *Delete.cshtml.cs* do souboru *plány* složky:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

`OnPostAsync` Obsluhovala odstraňuje se plán, podle jeho `id`:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

Po úspěšném odstranění plánu, `RedirectToPage` uživatel odešle zpět plány *Index.cshtml* stránky.

## <a name="the-working-schedules-razor-page"></a>Funkční plány stránky Razor

Když se stránka načte, popisků a vstupy pro název plánu, plán veřejné a privátní plánu jsou vykreslovány pomocí tlačítka Odeslat:

![Stránka Razor plány, jak je vidět na počátečním načtení bez chyb ověření a prázdná pole](upload-files/_static/browser1.png)

Výběr **nahrát** tlačítko bez naplnění libovolné pole je v rozporu `[Required]` atributů v modelu. `ModelState` Je neplatný. Uživateli se zobrazí chybové zprávy ověření:

![Chybové zprávy ověření se zobrazí vedle každého vstupního ovládacího prvku](upload-files/_static/browser2.png)

Zadejte dvě písmena do **název** pole. Změny ověření zpráva označující, že název musí být dlouhý 3 až 60 znaků:

![Změnit název ověřovací zpráva](upload-files/_static/browser3.png)

Když se nahrají nejmíň jeden plán, **načíst plány** vykreslí část načíst plány:

![Tabulka načíst plány, zobrazuje název každého plánu, nahrát data ve standardu UTC, veřejné verze souboru velikost a velikost souboru privátní verze](upload-files/_static/browser4.png)

Uživatel může kliknout **odstranit** odkaz z něj k dosažení zobrazení potvrzení odstranění, ve kterých budou mít možnost potvrdit nebo zrušit operaci odstranění zopakovat.

## <a name="troubleshooting"></a>Poradce při potížích

Informace o odstraňování potíží `IFormFile` nahrát, najdete v článku [nahrání souborů v ASP.NET Core: Řešení potíží s](xref:mvc/models/file-uploads#troubleshooting).
