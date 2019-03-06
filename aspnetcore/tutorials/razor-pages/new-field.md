---
title: Přidat nové pole do stránky v ASP.NET Core Razor
author: rick-anderson
description: Ukazuje, jak přidat nové pole do stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: f8661a48ddd6fc616c141435edc603117b4925fb
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345885"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Přidat nové pole do stránky v ASP.NET Core Razor

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

V této části [Entity Framework](/ef/core/get-started/aspnetcore/new-db) se používá k migrace Code First:

* Přidání nového pole do modelu.
* Migrace novou změnu schématu pole do databáze.

Při použití automaticky vytvořit databázi, Code First EF Code First:

* Přidá do databáze, kterou chcete sledovat, jestli je schéma databáze synchronizované s tříd modelu, které byly vygenerovány z tabulky.
* Pokud nejsou synchronizované s databáze třídy modelu, EF vyvolá výjimku.

Automatické ověření schématu a model synchronizované usnadňuje vyhledání potíží nekonzistentní databáze nebo kódu.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti do hodnocení filmů modelu

Otevřít *Models/Movie.cs* a přidejte `Rating` vlastnost:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavení aplikace.

Upravit *Pages/Movies/Index.cshtml*a přidejte `Rating` pole:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

Aktualizace na následujících stránkách:

* Přidat `Rating` pole na stránkách Delete a podrobnosti.
* Aktualizace [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s `Rating` pole.
* Přidat `Rating` pole na stránce Upravit.

Aplikace nebude fungovat, dokud databáze je aktualizováno, aby zahrnovalo nové pole. Je-li spustit, vyvolá aplikaci `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Tato chyba je způsobena se liší od schématu tabulky Movie databáze třídy modelu aktualizované video. (Neexistuje žádný `Rating` sloupec v tabulce databáze.)

Řešení chyby několika způsoby:

1. Máte rozhraní Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nové schéma třídy modelu. Tento přístup je vhodný v rané fázi vývojového cyklu; umožňuje rychlý rozvoj schématu modelu a databáze společně. Nevýhodou je, dojít ke ztrátě existujících dat v databázi. Nepoužívejte tento postup u provozní databáze. Vyřazení databáze na změny schématu a pomocí inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace.

2. Explicitně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu. Výhodou tohoto přístupu je, že zachováte vaše data. Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.

3. Pomocí migrace Code First aktualizovat schéma databáze.

V tomto kurzu pomocí migrace Code First.

Aktualizace `SeedData` třídy tak, že poskytuje hodnoty pro nový sloupec. Ukázka změnu je uveden níže, ale budete chtít tuto změnu pro každou `new Movie` bloku.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Zobrazit [dokončit soubor SeedData.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Sestavte řešení.

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Přidejte migraci pro pole hodnocení

Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet > Konzola správce balíčků**.
V konzole PMC zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` Příkaz říká rozhraní framework:

* Porovnání `Movie` modelů s `Movie` schématu databáze.
* Vytvoření kódu pro migraci schématu databáze na nový model.

Název "Hodnocení" je volitelný a slouží k pojmenování souboru migrace. Je vhodné použít smysluplný název souboru migrace.

`Update-Database` Příkaz říká rozhraní framework k použití změn schématu do databáze.

<a name="ssox"></a>

Při odstranění všech záznamů v databázi, bude inicializátoru naplnit databáze a zahrnout `Rating` pole. To lze provést pomocí odstranit odkazy v prohlížeči nebo z [Průzkumník objektů systému Sql Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Další možností je odstranit databázi a znovu vytvořit databázi pomocí migrace. Pokud chcete odstranit databázi v SSOX:

* Vyberte databázi v SSOX.
* Klikněte pravým tlačítkem na databázi a vyberte *odstranit*.
* Zkontrolujte **ukončete stávající připojení**.
* Vyberte **OK**.
* V [PMC](xref:tutorials/razor-pages/new-field#pmc), aktualizovat databázi:

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

<!-- copy/paste this tab to the next. Not worth an include  -->

Spuštěním následujících příkazů rozhraní příkazového řádku .NET Core:

```console
dotnet ef migrations add Rating
dotnet ef database update
```

`ef migrations add` Příkaz říká rozhraní framework:

* Porovnání `Movie` modelů s `Movie` schématu databáze.
* Vytvoření kódu pro migraci schématu databáze na nový model.

Název "Hodnocení" je volitelný a slouží k pojmenování souboru migrace. Je vhodné použít smysluplný název souboru migrace.

`ef database update` Příkaz říká rozhraní framework k použití změn schématu do databáze.

Při odstranění všech záznamů v databázi, bude inicializátoru naplnit databáze a zahrnout `Rating` pole. Můžete to provést s odstranit odkazy v prohlížeči nebo pomocí nástroje SQLite.

Další možností je odstranit databázi a znovu vytvořit databázi pomocí migrace. Pokud chcete odstranit databázi, odstraňte soubor databáze (*MvcMovie.db*). Spusťte `ef database update` příkaz: 

```console
dotnet ef database update
```

> [!NOTE]
> EF Core SQLite zprostředkovatel nepodporuje mnoho operací změny schématu. Například přidáním sloupce se podporuje, ale odebráním sloupce se nepodporuje. Pokud chcete přidat migrace odebrat sloupce, `ef migrations add` úspěšný příkaz ale `ef database update` příkaz selže. Ručně napsáním kódu migrace provést znovu vytvořit tabulku, můžete alternativně vyřešit některá omezení. Tabulka opětovné sestavení zahrnuje přejmenování existující tabulky, vytvářet nové tabulky, kopírování dat do nové tabulky a vyřazení staré tabulky. Další informace naleznete v následujících materiálech:
> * [Omezení poskytovatele pro databázi SQLite EF Core](/ef/core/providers/sqlite/limitations)
> * [Přizpůsobení migrace kódu](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Předvyplnění dat](/ef/core/modeling/data-seeding)

---  
<!-- End of VS tabs -->

Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole. Pokud databáze není nasazený, nastavte zarážky `SeedData.Initialize` metody.

## <a name="additional-resources"></a>Další zdroje

* [Verzi tohoto kurzu na webu YouTube](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Předchozí: Přidání vyhledávací funkce](xref:tutorials/razor-pages/search)
> [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)
