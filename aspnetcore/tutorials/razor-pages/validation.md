---
title: Přidání ověření na stránku ASP.NET core razor
author: rick-anderson
description: Zjistěte, jak přidat ověření na stránku Razor v ASP.NET jádra.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666017"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Přidání ověření na stránku ASP.NET core razor

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části je do `Movie` modelu přidána logika ověření. Ověřovací pravidla jsou vynucena pokaždé, když uživatel vytvoří nebo uvede film.

## <a name="validation"></a>Ověřování

Klíčovým principem vývoje softwaru se nazývá [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) **("D**on't **R**epeat **Y**ourself"). Razor Pages podporuje vývoj, kde je jednou zadána funkčnost a odráží se v celé aplikaci. DRY může pomoci:

* Snižte množství kódu v aplikaci.
* Aby kód méně náchylné k chybám a snadněji testovat a udržovat.

Podpora validace poskytovaná razor pages a entity framework je dobrým příkladem principu DRY. Ověřovací pravidla jsou deklarativně zadána na jednom místě (ve třídě modelu) a pravidla jsou vynucena všude v aplikaci.

## <a name="add-validation-rules-to-the-movie-model"></a>Přidání ověřovacích pravidel do filmového modelu

Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributů, které jsou deklarativně použity na třídu nebo vlastnost. DataAnnotations také obsahuje atributy `DataType` formátování, jako je to, které pomáhají s formátováním a neposkytují žádné ověření.

Aktualizujte `Movie` třídu, abyste využili `Required`výhod `StringLength` `RegularExpression`předdefinovaných atributů , a `Range` validation.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověření určují chování, které chcete vynutit u vlastností modelu, na které se použijí:

* `Required` Atributy `MinimumLength` a označují, že vlastnost musí mít hodnotu; ale nic nebrání uživateli v zadání prázdného místa k uspokojení tohoto ověření.
* Atribut `RegularExpression` se používá k omezení, jaké znaky lze zadat. V předchozím kódu "Žánr":

  * Musí používat pouze písmena.
  * První písmeno musí být velkými písmeny. Prázdné znaky, čísla a speciální znaky nejsou povoleny.

* "Hodnocení": `RegularExpression`

  * Vyžaduje, aby první znak byl velké písmeno.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platný pro hodnocení, ale selže pro "Žánr".

* Atribut `Range` omezuje hodnotu v konkrétním rozsahu.
* Atribut `StringLength` umožňuje nastavit maximální délku vlastnosti řetězce a volitelně její minimální délku.
* Typy `decimal`hodnot (například `float` `DateTime`, `int`, ) jsou ze své `[Required]` podstaty povinné a nepotřebují atribut.

Díky tomu, že se ověřovací pravidla automaticky vynucují pomocí ASP.NET Core, vaše aplikace bude robustnější. Také zajišťuje, že nemůžete zapomenout na ověření něco a nechtěně nechat špatná data do databáze.

### <a name="validation-error-ui-in-razor-pages"></a>U rozhraní chyby ověření na stránkách Břitva

Spusťte aplikaci a přejděte na Stránky/filmy.

Vyberte odkaz **Vytvořit nový.** Vyplňte formulář s některými neplatnými hodnotami. Když ověření na straně klienta jQuery zjistí chybu, zobrazí se chybová zpráva.

![Formulář zobrazení filmu s více chybami ověření na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Všimněte si, jak formulář automaticky vykreslil chybovou zprávu ověření v každém poli obsahující neplatnou hodnotu. Chyby jsou vynuceny jak na straně klienta (pomocí JavaScriptu a jQuery), tak na straně serveru (pokud má uživatel JavaScript zakázán).

Významnou výhodou je, že na stránkách Vytvořit nebo Upravit nebyly nutné **žádné** změny kódu. Jakmile dataAnnotations byly použity pro model, bylo povoleno ověřovací ui. Stránky razor vytvořené v tomto kurzu automaticky zvedl ověřovací pravidla (pomocí `Movie` ověřovací atributy na vlastnosti třídy modelu). Test ověření pomocí upravit stránku, stejné ověření se použije.

Data formuláře nejsou zaúčtována na server, dokud neexistují žádné chyby ověření na straně klienta. Ověřte, zda data formuláře nejsou zaúčtována jedním nebo více z následujících přístupů:

* Vložte bod přerušení `OnPostAsync` do metody. Odešlete formulář (vyberte **Vytvořit** nebo **Uložit**). Bod zlomu není nikdy zasažen.
* Použijte [nástroj šumavka](https://www.telerik.com/fiddler).
* Pomocí nástrojů pro vývojáře prohlížeče můžete sledovat síťový provoz.

### <a name="server-side-validation"></a>Ověření na straně serveru

Pokud je JavaScript v prohlížeči zakázán, odeslání formuláře s chybami se odešle na server.

Volitelné ověření na straně serveru:

* Zakažte JavaScript v prohlížeči. JavaScript můžete zakázat pomocí vývojářských nástrojů prohlížeče. Pokud nemůžete v prohlížeči zakázat JavaScript, zkuste jiný prohlížeč.
* Nastavte bod přerušení `OnPostAsync` v metodě stránky Vytvořit nebo upravit.
* Odešlete formulář s neplatnými údaji.
* Ověřte, zda je stav modelu neplatný:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
Případně můžete [zakázat ověřování na straně klienta na serveru](xref:mvc/models/validation#disable-client-side-validation).

Následující kód ukazuje část *Create.cshtml* stránky šetrné dříve v kurzu. Stránky Vytvořit a upravit jej používají k zobrazení počátečního formuláře a k opětovnému zobrazení formuláře v případě chyby.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

Pomocník [vstupníznačky](xref:mvc/views/working-with-forms) používá [atributy DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta. Pomocná [pomoc s ověřovacíznačkou](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazuje chyby ověření. Další informace naleznete v [tématu Ověření.](xref:mvc/models/validation)

Na stránkách Vytvořit a upravit nejsou žádná ověřovací pravidla. Ověřovací pravidla a chybové řetězce jsou `Movie` určeny pouze ve třídě. Tato ověřovací pravidla se automaticky použijí `Movie` na stránky Razor, které modelu upravují.

Když se logika ověření musí změnit, provádí se pouze v modelu. Ověření se používá konzistentně v celé aplikaci (ověřovací logika je definována na jednom místě). Ověření na jednom místě pomáhá udržovat kód čistý a usnadňuje údržbu a aktualizaci.

## <a name="using-datatype-attributes"></a>Použití atributů datového typu

Prozkoumejte `Movie` třídu. Obor `System.ComponentModel.DataAnnotations` názvů poskytuje kromě předdefinované sady atributů ověření také atributy formátování. Pro vlastnosti `ReleaseDate` a `Price` je použit atribut `DataType`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atributy `DataType` poskytují pouze rady pro modul zobrazení pro formátování dat (a `<a>` dodává atributy, jako jsou adresy URL a `<a href="mailto:EmailAddress.com">` e-mail). Pomocí `RegularExpression` atributu ověřte formát dat. Atribut `DataType` se používá k určení datového typu, který je konkrétnější než vnitřní typ databáze. `DataType`atributy nejsou atributy ověření. V ukázkové aplikaci se zobrazí pouze datum bez času.

`DataType` Výčet poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa a další. Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ. Odkaz lze `mailto:` například vytvořit `DataType.EmailAddress`pro aplikaci . Volič data může být k `DataType.Date` dispozici v prohlížečích, které podporují HTML5. Atributy `DataType` vyzařují atributy HTML 5 `data-` (vyslovuje se pomlčka dat), které prohlížeče HTML 5 spotřebovávají. Atributy `DataType` **neposkytují** žádné ověření.

`DataType.Date`neurčuje formát zobrazeného data. Ve výchozím nastavení je datové pole zobrazeno podle výchozích formátů založených na souborech serveru `CultureInfo`.

Anotace `[Column(TypeName = "decimal(18, 2)")]` dat je vyžadována, aby `Price` bylo jádro entity správně mapováno na měnu v databázi. Další informace naleznete [v tématu Datové typy](/ef/core/modeling/relational/data-types).

Atribut `DisplayFormat` se používá k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Toto `ApplyFormatInEditMode` nastavení určuje, že formátování má být použito při zobrazení hodnoty pro úpravy. Toto chování možná nebudete chtít u některých polí. Například v hodnotách měny pravděpodobně nechcete symbol měny v uměle upravit.

Atribut `DisplayFormat` lze použít samostatně, ale je obecně vhodné použít `DataType` atribut. Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce a poskytuje následující výhody, které nezískáte s DisplayFormat:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolměny odpovídající národnímu prostředí, odkazy na e-mailatd.)
* Ve výchozím nastavení prohlížeč vykreslí data ve správném formátu na základě národního prostředí.
* Atribut `DataType` může povolit ASP.NET core framework zvolit správnou šablonu pole pro vykreslení dat. If `DisplayFormat` používá sám používá šablonu řetězce.

Poznámka: jQuery ověření nefunguje `Range` s `DateTime`atributem a . Například následující kód vždy zobrazí chybu ověření na straně klienta, i když je datum v zadaném rozsahu:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Obecně není vhodné kompilovat tvrdá data ve vašich `Range` modelech, takže použití atributu a `DateTime` je odrazováno.

Následující kód ukazuje kombinování atributů na jednom řádku:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) ukazuje pokročilé EF Core operace s Razor Pages.

### <a name="apply-migrations"></a>Použití migrace

DataAnnotations aplikované na třídu změnit schéma. Například dataAnnotations aplikované `Title` na pole:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Omezuje znaky na 60.
* Neumožňuje hodnotu. `null`

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Tabulka `Movie` má aktuálně následující schéma:

```sql
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

Předchozí změny schématu nezpůsobí EF vyvolat výjimku. Vytvořte však migraci, aby schéma bylo konzistentní s modelem.

V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.
Do pmc zadejte následující příkazy:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database`spustí `Up` metody třídy. `New_DataAnnotations` Zkontrolujte `Up` metodu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

Aktualizovaná `Movie` tabulka obsahuje následující schéma:

```sql
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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Migrace nejsou vyžadovány pro SQLite.

---

### <a name="publish-to-azure"></a>Publikování aplikací do Azure

Informace o nasazení do Azure najdete [v tématu Kurz: Vytvoření aplikace ASP.NET Core v Azure s databází SQL .](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

Děkujeme za dokončení tohoto úvodu do Razor Stránek. [Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikající sledování tohoto kurzu.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Verze tohoto kurzu pro YouTube](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Předchozí: Přidání nového pole](xref:tutorials/razor-pages/new-field)
