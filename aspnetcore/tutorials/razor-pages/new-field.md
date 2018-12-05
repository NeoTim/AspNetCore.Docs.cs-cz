---
title: Přidat nové pole do stránky v ASP.NET Core Razor
author: rick-anderson
description: Ukazuje, jak přidat nové pole do stránky Razor pomocí Entity Framework Core
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: e280bc9553113982a1f1a77eabab32575c905237
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862288"
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

<a name="ssox"></a>

Při odstranění všech záznamů v databázi, bude inicializátoru naplnit databáze a zahrnout `Rating` pole. To lze provést pomocí odstranit odkazy v prohlížeči nebo z [Průzkumník objektů systému Sql Server](xref:tutorials/razor-pages/sql#ssox) (SSOX). Pokud chcete odstranit databázi z SSOX:

* Vyberte databázi v SSOX.
* Klikněte pravým tlačítkem na databázi a vyberte *odstranit*.
* Zkontrolujte **ukončete stávající připojení**.
* Vyberte **OK**.
* V [PMC](xref:tutorials/razor-pages/new-field#pmc), aktualizovat databázi:

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- copy/paste this tab to the next. Not worth an include  --> SQLite nepodporuje migrace.

* Odstranění databáze nebo změňte název databáze *appsettings.json* souboru.
* Odstranit *migrace* složku (a všechny soubory ve složce).

Spuštěním následujících příkazů rozhraní příkazového řádku .NET Core:

```console
dotnet ef migrations add Rating
dotnet ef database update
```

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

SQLite nepodporuje migrace.

* Odstranění databáze nebo změňte název databáze *appsettings.json* souboru.
* Odstranit *migrace* složku (a všechny soubory ve složce).

Spuštěním následujících příkazů rozhraní příkazového řádku .NET Core:

```console
dotnet ef migrations add Rating
dotnet ef database update
```

---  
<!-- End of VS tabs -->

Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole. Pokud databáze není nasazený, nastavte zarážky `SeedData.Initialize` metody.

> [!div class="step-by-step"]
> [Předchozí: Přidání vyhledávací funkce](xref:tutorials/razor-pages/search)
> [Další: Přidání ověření](xref:tutorials/razor-pages/validation)
