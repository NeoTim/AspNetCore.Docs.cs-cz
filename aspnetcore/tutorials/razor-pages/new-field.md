---
title: Přidat nové pole na stránku Razor v ASP.NET Core
author: rick-anderson
description: Ukazuje, jak přidat nové pole na stránku Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: a1c0622d97e0d2b0a5601e27688f4be7cbe068dc
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483301"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Přidat nové pole na stránku Razor v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:

* Přidejte do modelu nové pole.
* Migrujte novou změnu schématu pole do databáze.

Při použití Code First EF k automatickému vytvoření databáze Code First:

* Přidá do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.
* Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.

Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti hodnocení do modelu videa

Otevřete soubor *Models/video. cs* a přidejte `Rating` vlastnost:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavte aplikaci.

Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,61-63)]

Aktualizujte následující stránky:

* `Rating` Přidejte pole na stránky odstranit a podrobnosti.
* Aktualizace [Create. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) s `Rating` polem
* `Rating` Přidejte pole do stránky pro úpravy.

Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole. Pokud nyní spustí aplikace, vyvolá `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Tato chyba je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů databáze. (V tabulce databáze `Rating` není žádný sloupec.)

K řešení této chyby je potřeba několik přístupů:

1. Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu. Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady. Nevýhodou je, že ztratíte stávající data v databázi. Nepoužívejte tento přístup k provozní databázi. Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.

2. Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu. Výhodou tohoto přístupu je, že zachováte data. Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.

3. K aktualizaci schématu databáze použijte Migrace Code First.

Pro tento kurz použijte Migrace Code First.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Podívejte se na [dokončený soubor SeedData.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).

Sestavte řešení.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Přidání migrace pro pole hodnocení

V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.
V konzole PMC zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` Příkaz instruuje rozhraní:

* Porovnejte `Movie` model se schématem databáze. `Movie`
* Vytvořte kód pro migraci schématu databáze do nového modelu.

Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace. Je užitečné použít pro migrační soubor smysluplný název.

`Update-Database` Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi.

<a name="ssox"></a>

Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne `Rating` pole. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze. Odstranění databáze v SSOX:

* Vyberte databázi v SSOX.
* Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.
* Podívejte se na **Zavřít existující připojení**.
* Vyberte **OK**.
* V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Vyřazení a opětovné vytvoření databáze

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte složku migrace.  K opětovnému vytvoření databáze použijte následující příkazy.

```console
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole. Pokud databáze není osazena, nastavte v `SeedData.Initialize` metodě bod přerušení.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Předchozí Další přidání](xref:tutorials/razor-pages/search)vyhledávání
> :[ Přidání ověřování](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:

* Přidejte do modelu nové pole.
* Migrujte novou změnu schématu pole do databáze.

Při použití Code First EF k automatickému vytvoření databáze Code First:

* Přidá do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.
* Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.

Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti hodnocení do modelu videa

Otevřete soubor *Models/video. cs* a přidejte `Rating` vlastnost:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavte aplikaci.

Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Aktualizujte následující stránky:

* `Rating` Přidejte pole na stránky odstranit a podrobnosti.
* Aktualizace [Create. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s `Rating` polem
* `Rating` Přidejte pole do stránky pro úpravy.

Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole. Pokud nyní spustí aplikace, vyvolá `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Tato chyba je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů databáze. (V tabulce databáze `Rating` není žádný sloupec.)

K řešení této chyby je potřeba několik přístupů:

1. Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu. Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady. Nevýhodou je, že ztratíte stávající data v databázi. Nepoužívejte tento přístup k provozní databázi. Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.

2. Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu. Výhodou tohoto přístupu je, že zachováte data. Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.

3. K aktualizaci schématu databáze použijte Migrace Code First.

Pro tento kurz použijte Migrace Code First.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Podívejte se na [dokončený soubor SeedData.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Sestavte řešení.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Přidání migrace pro pole hodnocení

V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.
V konzole PMC zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` Příkaz instruuje rozhraní:

* Porovnejte `Movie` model se schématem databáze. `Movie`
* Vytvořte kód pro migraci schématu databáze do nového modelu.

Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace. Je užitečné použít pro migrační soubor smysluplný název.

`Update-Database` Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi.

<a name="ssox"></a>

Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne `Rating` pole. Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze. Odstranění databáze v SSOX:

* Vyberte databázi v SSOX.
* Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.
* Podívejte se na **Zavřít existující připojení**.
* Vyberte **OK**.
* V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Vyřazení a opětovné vytvoření databáze

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte databázi a pomocí migrace znovu vytvořte databázi. Chcete-li odstranit databázi, odstraňte soubor databáze (*MvcMovie. DB*). Pak spusťte `ef database update` příkaz:

```console
dotnet ef database update
```

---

Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole. Pokud databáze není osazena, nastavte v `SeedData.Initialize` metodě bod přerušení.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Předchozí Další přidání](xref:tutorials/razor-pages/search)vyhledávání
> :[ Přidání ověřování](xref:tutorials/razor-pages/validation)

::: moniker-end
