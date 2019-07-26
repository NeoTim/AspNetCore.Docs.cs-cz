---
title: Přidání ověřování na stránku ASP.NET Core Razor
author: rick-anderson
description: Zjistěte, jak přidat ověřování na stránku Razor v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d6d45dc7154bf415c3b098299d066b6fb37cf64d
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483275"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Přidání ověřování na stránku ASP.NET Core Razor

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části je logika ověřování přidána do `Movie` modelu. Ověřovací pravidla se vynutily pokaždé, když uživatel vytvoří nebo upraví film.

## <a name="validation"></a>Ověřování

Key principem vývoje softwaru se nazývá [suchý](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**EPEAT **Y**ourself"). Razor Pages podporuje vývoj, ve kterém jsou funkce zadány jednou a které se projeví v celé aplikaci. Bezsuchá může pomáhat:

* Snižte množství kódu v aplikaci.
* Udělejte kód méně náchylné k chybám a usnadněte si testování a údržbu.

Podpora ověřování, kterou poskytuje Razor Pages a Entity Framework, je dobrým příkladem SUCHÉho principu. Ověřovací pravidla jsou deklarativně určena na jednom místě (ve třídě modelu) a pravidla jsou vynutila všude v aplikaci.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

### <a name="validation-error-ui-in-razor-pages"></a>Uživatelské rozhraní chyby ověřování v Razor Pages

Spusťte aplikaci a přejděte na stránky/filmy.

Vyberte odkaz **vytvořit nový** . Vyplňte formulář s neplatnými hodnotami. Když při ověřování na straně klienta zjistí chybu, zobrazí se chybová zpráva.

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

Všimněte si, jak formulář automaticky vykresluje chybovou zprávu ověřování v každém poli, které obsahuje neplatnou hodnotu. Chyby se vynutily na straně klienta (pomocí JavaScriptu a jQuery) a na straně serveru (když má uživatel zakázaný JavaScript).

Významnou výhodou je, že na stránkách vytvořit nebo upravit nebyly nutné **žádné** změny kódu. Jakmile se v modelu aplikují dataanotace, uživatelské rozhraní ověřování je povolené. Razor Pages vytvořená v tomto kurzu automaticky vybrala ověřovací pravidla (pomocí atributů ověřování ve vlastnostech `Movie` třídy modelu). Ověření testu pomocí stránky pro úpravy je použito stejné ověřování.

Data formuláře se na server neúčtují, dokud nedojde k žádným chybám při ověřování na straně klienta. Ověřte, že data formuláře nejsou publikovaná jedním nebo více z následujících přístupů:

* Umístěte bod přerušení do `OnPostAsync` metody. Odešlete formulář (vyberte **vytvořit** nebo **Uložit**). Není k dispozice žádný bod přerušení.
* Použijte [Nástroj Fiddler](https://www.telerik.com/fiddler).
* Pomocí vývojářských nástrojů pro prohlížeč můžete monitorovat síťový provoz.

### <a name="server-side-validation"></a>Ověřování na straně serveru

Pokud je v prohlížeči zakázán jazyk JavaScript, odesláním formuláře s chybami bude odesláno na server.

Volitelné, testovací ověřování na straně serveru:

* Zakáže JavaScript v prohlížeči. JavaScript můžete zakázat pomocí vývojářských nástrojů v prohlížeči. Pokud nemůžete zakázat JavaScript v prohlížeči, zkuste použít jiný prohlížeč.
* Nastavte bod přerušení v `OnPostAsync` metodě stránky vytvořit nebo upravit.
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

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) používá atributy DataAnnotations a vytváří atributy HTML potřebné k ověření jQuery na straně klienta. [](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) [Pomocník pro ověřování značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověřování. Další informace najdete v tématu [ověření](xref:mvc/models/validation) .

Na stránkách pro vytváření a úpravy nejsou v nich žádná ověřovací pravidla. Ověřovací pravidla a řetězce chyb jsou určeny pouze ve `Movie` třídě. Tato ověřovací pravidla se automaticky aplikují na Razor Pages, která `Movie` model upravují.

Když je potřeba logiku ověřování změnit, provede se jenom v modelu. Ověřování se konzistentně používá v celé aplikaci (logika ověřování je definovaná na jednom místě). Ověřování na jednom místě usnadňuje vyčištění kódu a usnadňuje údržbu a aktualizaci.

## <a name="using-datatype-attributes"></a>Použití atributů DataType

Prověřte `Movie` třídu. `System.ComponentModel.DataAnnotations` Obor názvů poskytuje kromě předdefinované sady ověřovacích atributů i atributy formátování. Atribut je použit `ReleaseDate` pro vlastnosti a `Price`. `DataType`

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atributy poskytují nápovědu pouze pro modul zobrazení k formátování dat (a poskytování atributů `<a>` , jako je například adresa URL a `<a href="mailto:EmailAddress.com">` e-mailu). `DataType` `RegularExpression` Použijte atribut k ověření formátu dat. `DataType` Atribut slouží k určení datového typu, který je konkrétnější než vnitřní typ databáze. `DataType`atributy nejsou ověřovány. V ukázkové aplikaci se zobrazí pouze datum, a to bez času.

`DataType` Výčet poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další. `DataType` Atribut může také povolit aplikaci automatické poskytování funkcí specifických pro typ. Můžete například `mailto:` vytvořit odkaz pro `DataType.EmailAddress`. Selektor data lze zadat pro `DataType.Date` v prohlížečích, které podporují HTML5. Atributy emitují atributy HTML 5 `data-` (vyslovované datové přerušované), které používají prohlížeče formátu HTML 5. `DataType` Atributy neposkytují žádné ověřování.  `DataType`

`DataType.Date`neurčuje formát data, které se zobrazí. Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech založených na serveru `CultureInfo`.

Datová anotace je vyžadována, aby Entity Framework Core mohl správně `Price` mapovat na měnu v databázi. `[Column(TypeName = "decimal(18, 2)")]` Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Toto `ApplyFormatInEditMode` nastavení určuje, že se má formátování použít při zobrazení hodnoty pro úpravy. Pro některá pole možná nebudete chtít toto chování. Například v hodnotách měn pravděpodobně nebudete chtít symbol měny v uživatelském rozhraní úprav.

Atribut může být použit sám sebe, ale obecně je vhodné `DataType` použít atribut. `DisplayFormat` `DataType` Atribut předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které nezískáte pomocí DisplayFormat:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů atd.)
* Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.
* `DataType` Atribut může ASP.NET Core rozhraní povolit výběr pravé šablony pole k vykreslení dat. V `DisplayFormat` případě, že se používá samostatně, používá šablonu řetězce.

Poznámka: ověřování jQuery nefunguje s `Range` atributem a. `DateTime` Například následující kód bude vždy zobrazovat chybu ověřování na straně klienta, i když je datum v zadaném rozsahu:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Obvykle není dobrým zvykem při kompilování pevných dat ve vašich modelech, takže použití `Range` atributu a `DateTime` nedoporučuje se.

Následující kód ukazuje kombinování atributů na jednom řádku:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Začínáme se Razor Pages a EF Core](xref:data/ef-rp/intro) zobrazuje pokročilé EF Core operace s Razor Pages.

### <a name="apply-migrations"></a>Použít migrace

Tato dataanotace použitá pro třídu mění schéma. Například dataanotace použité pro `Title` pole:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Omezí znaky na 60.
* Nepovoluje `null` hodnotu.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

`Movie` Tabulka teď má následující schéma:

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
V konzole PMC zadejte následující příkazy:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database``Up` spustí metody`New_DataAnnotations` třídy. Projděte `Up` si metodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

Aktualizovaná `Movie` tabulka má následující schéma:

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

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Migrace nejsou pro SQLite požadovány.

---

### <a name="publish-to-azure"></a>Publikování do Azure

Informace o nasazení do Azure najdete v tématu [kurz: Sestavte ASP.NET Core aplikaci v Azure pomocí](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)SQL Database.

Děkujeme za dokončení tohoto úvodu do Razor Pages. Začněte [s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikajícím postupem v tomto kurzu.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Verze YouTube tohoto kurzu](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Předchozí Přidání nového pole](xref:tutorials/razor-pages/new-field)
