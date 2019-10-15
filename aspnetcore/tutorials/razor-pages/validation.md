---
title: Přidání ověřování na stránku ASP.NET Core Razor
author: rick-anderson
description: Zjistěte, jak přidat ověřování na stránku Razor v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: c2397a535fa2c128f18d65323d0f4920af914205
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334222"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Přidání ověřování na stránku ASP.NET Core Razor

Od [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části je logika ověřování přidána do modelu `Movie`. Ověřovací pravidla se vynutily pokaždé, když uživatel vytvoří nebo upraví film.

## <a name="validation"></a>Ověřování

Key principem vývoje softwaru se nazývá [suchý](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**EPEAT **Y**ourself"). Razor Pages podporuje vývoj, ve kterém jsou funkce zadány jednou a které se projeví v celé aplikaci. Bezsuchá může pomáhat:

* Snižte množství kódu v aplikaci.
* Udělejte kód méně náchylné k chybám a usnadněte si testování a údržbu.

Podpora ověřování, kterou poskytuje Razor Pages a Entity Framework, je dobrým příkladem SUCHÉho principu. Ověřovací pravidla jsou deklarativně určena na jednom místě (ve třídě modelu) a pravidla jsou vynutila všude v aplikaci.

## <a name="add-validation-rules-to-the-movie-model"></a>Přidání ověřovacích pravidel do modelu filmů

Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributů, které se aplikují deklarativně na třídu nebo vlastnost. Tato dataanotace také obsahuje atributy formátování, jako je `DataType`, které vám pomůžou s formátováním a neposkytují žádné ověřování.

Aktualizujte třídu `Movie`, abyste mohli využívat integrované ověřovací atributy `Required`, `StringLength`, `RegularExpression` a `Range`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověřování určují chování, které chcete vyhovět pro vlastnosti modelu, na které se aplikují:

* Atributy `Required` a `MinimumLength` označují, že vlastnost musí mít hodnotu. ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.
* Atribut `RegularExpression` slouží k omezení znaků, které lze zadat. V předchozím kódu "Žánr":

  * Je nutné použít pouze písmena.
  * První písmeno musí být velkými písmeny. Mezery, číslice a speciální znaky nejsou povoleny.

* Hodnocení `RegularExpression`:

  * Vyžaduje, aby byl první znak velkým písmenem.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.

* Atribut `Range` omezuje hodnotu na v zadaném rozsahu.
* Atribut `StringLength` umožňuje nastavit maximální délku řetězcové vlastnosti a volitelně její minimální délku.
* Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou v podstatě vyžadované a nepotřebují atribut `[Required]`.

Automatické vynucení ověřovacích pravidel nástrojem ASP.NET Core pomáhá zajistit větší odolnost aplikace. Také zajišťuje, že se nebudete moci zapomenout a neúmyslně ověřit data v databázi.

### <a name="validation-error-ui-in-razor-pages"></a>Uživatelské rozhraní chyby ověřování v Razor Pages

Spusťte aplikaci a přejděte na stránky/filmy.

Vyberte odkaz **vytvořit nový** . Vyplňte formulář s neplatnými hodnotami. Když při ověřování na straně klienta zjistí chybu, zobrazí se chybová zpráva.

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Všimněte si, jak formulář automaticky vykresluje chybovou zprávu ověřování v každém poli, které obsahuje neplatnou hodnotu. Chyby se vynutily na straně klienta (pomocí JavaScriptu a jQuery) a na straně serveru (když má uživatel zakázaný JavaScript).

Významnou výhodou je, že na stránkách vytvořit nebo upravit nebyly nutné **žádné** změny kódu. Jakmile se v modelu aplikují dataanotace, uživatelské rozhraní ověřování je povolené. Razor Pages vytvořená v tomto kurzu automaticky vybrala ověřovací pravidla (pomocí atributů ověřování ve vlastnostech třídy modelu `Movie`). Ověření testu pomocí stránky pro úpravy je použito stejné ověřování.

Data formuláře se na server neúčtují, dokud nedojde k žádným chybám při ověřování na straně klienta. Ověřte, že data formuláře nejsou publikovaná jedním nebo více z následujících přístupů:

* Umístěte bod přerušení do metody `OnPostAsync`. Odešlete formulář (vyberte **vytvořit** nebo **Uložit**). Není k dispozice žádný bod přerušení.
* Použijte [Nástroj Fiddler](https://www.telerik.com/fiddler).
* Pomocí vývojářských nástrojů pro prohlížeč můžete monitorovat síťový provoz.

### <a name="server-side-validation"></a>Ověřování na straně serveru

Pokud je v prohlížeči zakázán jazyk JavaScript, odesláním formuláře s chybami bude odesláno na server.

Volitelné, testovací ověřování na straně serveru:

* Zakáže JavaScript v prohlížeči. JavaScript můžete zakázat pomocí vývojářských nástrojů v prohlížeči. Pokud nemůžete zakázat JavaScript v prohlížeči, zkuste použít jiný prohlížeč.
* Nastavte bod přerušení v metodě `OnPostAsync` stránky vytvořit nebo upravit.
* Odešle formulář s neplatnými daty.
* Ověřte, že stav modelu není platný:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

Následující kód ukazuje část vygenerovaného uživatelského rozhraní stránky *vytvořit. cshtml* dříve v tomto kurzu. Používá se na stránkách vytvořit a upravit k zobrazení počátečního formuláře a k opětovnému zobrazení formuláře v případě chyby.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné k ověření jQuery na straně klienta. [Pomocník pro ověřování značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověřování. Další informace najdete v tématu [ověření](xref:mvc/models/validation) .

Na stránkách pro vytváření a úpravy nejsou v nich žádná ověřovací pravidla. Ověřovací pravidla a řetězce chyb jsou zadány pouze ve třídě `Movie`. Tato ověřovací pravidla se automaticky aplikují na Razor Pages, která upravují model `Movie`.

Když je potřeba logiku ověřování změnit, provede se jenom v modelu. Ověřování se konzistentně používá v celé aplikaci (logika ověřování je definovaná na jednom místě). Ověřování na jednom místě usnadňuje vyčištění kódu a usnadňuje údržbu a aktualizaci.

## <a name="using-datatype-attributes"></a>Použití atributů DataType

Prověřte třídu `Movie`. Obor názvů `System.ComponentModel.DataAnnotations` poskytuje kromě předdefinované sady ověřovacích atributů i atributy formátování. Atribut `DataType` se použije u vlastností `ReleaseDate` a `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atributy `DataType` poskytují pouze pomocné parametry pro modul zobrazení k formátování dat (a poskytování atributů, jako je například `<a>` pro URL a `<a href="mailto:EmailAddress.com">` pro e-mail). K ověření formátu dat použijte atribut `RegularExpression`. Atribut `DataType` slouží k zadání datového typu, který je konkrétnější než vnitřní typ databáze. atributy `DataType` nejsou ověřovacími atributy. V ukázkové aplikaci se zobrazí pouze datum, a to bez času.

Výčet `DataType` poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další. Atribut `DataType` může aplikaci povolit také automatické poskytování funkcí specifických pro typ. Například odkaz `mailto:` lze vytvořit pro `DataType.EmailAddress`. Selektor data lze zadat pro `DataType.Date` v prohlížečích, které podporují HTML5. Atributy `DataType` emitují atributy HTML 5 `data-` (vyslovované datové přerušované), které používají prohlížeče formátu HTML 5. Atributy `DataType` **neposkytují žádné** ověřování.

`DataType.Date` neurčuje formát data, které se zobrazí. Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech na základě `CultureInfo` serveru.

Datová anotace `[Column(TypeName = "decimal(18, 2)")]` je povinná, takže Entity Framework Core může správně mapovat `Price` na měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Nastavení `ApplyFormatInEditMode` určuje, že se má formátování použít při zobrazení hodnoty pro úpravy. Pro některá pole možná nebudete chtít toto chování. Například v hodnotách měn pravděpodobně nebudete chtít symbol měny v uživatelském rozhraní úprav.

Atribut `DisplayFormat` lze použít samostatně, ale obecně je vhodné použít atribut `DataType`. Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které nezískáte pomocí DisplayFormat:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů atd.)
* Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.
* Atribut `DataType` může povolit ASP.NET Coremu rozhraní vybrat šablonu pravého pole pro vykreslení dat. @No__t-0, pokud se používá samostatně, používá šablonu řetězce.

Poznámka: ověřování jQuery nefunguje s atributem `Range` a `DateTime`. Například následující kód bude vždy zobrazovat chybu ověřování na straně klienta, i když je datum v zadaném rozsahu:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Obvykle není dobrým zvykem při kompilování pevných dat ve vašich modelech, takže použijte atribut `Range` a `DateTime` se nedoporučuje.

Následující kód ukazuje kombinování atributů na jednom řádku:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Začínáme se Razor Pages a EF Core](xref:data/ef-rp/intro) zobrazuje pokročilé EF Core operace s Razor Pages.

### <a name="apply-migrations"></a>Použít migrace

Tato dataanotace použitá pro třídu mění schéma. Například dataanotace použité pro pole `Title`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Omezí znaky na 60.
* Nepovoluje hodnotu `null`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Tabulka `Movie` v současné době má následující schéma:

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

Předchozí změny schématu nezpůsobí výjimku EF k vyvolání výjimky. Vytvořte ale migraci tak, aby schéma bylo konzistentní s modelem.

V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.
Do PMC zadejte následující příkazy:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database` spustí metody `Up` třídy `New_DataAnnotations`. Projděte si metodu `Up`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

Aktualizovaná tabulka `Movie` má následující schéma:

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Migrace nejsou pro SQLite požadovány.

---

### <a name="publish-to-azure"></a>Publikování do Azure

Informace o nasazení do Azure najdete v tématu [kurz: sestavení aplikace ASP.NET Core v Azure pomocí SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

Děkujeme za dokončení tohoto úvodu do Razor Pages. Začněte [s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikajícím postupem v tomto kurzu.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Verze YouTube tohoto kurzu](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Předchozí: Přidání nového pole](xref:tutorials/razor-pages/new-field)
