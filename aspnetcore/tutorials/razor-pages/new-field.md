---
title: Část 7 – přidání nového pole na Razor stránku v ASP.NET Core
author: rick-anderson
description: Část 7 série kurzů na Razor stránkách.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 07350e127e2e529b5ad168c3e26c1ed33cd9b99f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407990"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Část 7 – přidání nového pole na Razor stránku v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:

* Přidejte do modelu nové pole.
* Migrujte novou změnu schématu pole do databáze.

Při použití Code First EF k automatickému vytvoření databáze Code First:

* Přidá `__EFMigrationsHistory` do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.
* Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.

Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti hodnocení do modelu videa

Otevřete soubor *Models/video. cs* a přidejte `Rating` vlastnost:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavení aplikace

Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

Aktualizujte následující stránky:

* Přidejte `Rating` pole na stránky odstranit a podrobnosti.
* Aktualizace [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) s `Rating` polem
* Přidejte `Rating` pole do stránky pro úpravy.

Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole. Spuštění aplikace bez aktualizace databáze vyvolá `SqlException` :

`SqlException: Invalid column name 'Rating'.`

`SqlException`Výjimka je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů v databázi. ( `Rating` V tabulce databáze není žádný sloupec.)

K řešení této chyby je potřeba několik přístupů:

1. Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu. Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady. Nevýhodou je, že ztratíte stávající data v databázi. Nepoužívejte tento přístup k provozní databázi. Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.

2. Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu. Výhodou tohoto přístupu je, že zachováte data. Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.

3. K aktualizaci schématu databáze použijte Migrace Code First.

Pro tento kurz použijte Migrace Code First.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Podívejte se na [dokončený soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).

Sestavte řešení.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Přidání migrace pro pole hodnocení

V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.
Do PMC zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration`Příkaz instruuje rozhraní:

* Porovnejte `Movie` model se `Movie` schématem databáze.
* Vytvořte kód pro migraci schématu databáze do nového modelu.

Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace. Je užitečné použít pro migrační soubor smysluplný název.

`Update-Database`Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi a zachovaly stávající data.

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Vyřazení a opětovné vytvoření databáze

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte složku migrace.  K opětovnému vytvoření databáze použijte následující příkazy.

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole. Pokud databáze není osazena, nastavte v metodě bod přerušení `SeedData.Initialize` .

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Předchozí: Přidání vyhledávání](xref:tutorials/razor-pages/search) 
>  [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)

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

Sestavení aplikace

Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Aktualizujte následující stránky:

* Přidejte `Rating` pole na stránky odstranit a podrobnosti.
* Aktualizace [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s `Rating` polem
* Přidejte `Rating` pole do stránky pro úpravy.

Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole. Pokud nyní spustí aplikace, vyvolá `SqlException` :

`SqlException: Invalid column name 'Rating'.`

Tato chyba je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů databáze. ( `Rating` V tabulce databáze není žádný sloupec.)

K řešení této chyby je potřeba několik přístupů:

1. Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu. Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady. Nevýhodou je, že ztratíte stávající data v databázi. Nepoužívejte tento přístup k provozní databázi. Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.

2. Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu. Výhodou tohoto přístupu je, že zachováte data. Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.

3. K aktualizaci schématu databáze použijte Migrace Code First.

Pro tento kurz použijte Migrace Code First.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Podívejte se na [dokončený soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Sestavte řešení.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Přidání migrace pro pole hodnocení

V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.
Do PMC zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration`Příkaz instruuje rozhraní:

* Porovnejte `Movie` model se `Movie` schématem databáze.
* Vytvořte kód pro migraci schématu databáze do nového modelu.

Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace. Je užitečné použít pro migrační soubor smysluplný název.

`Update-Database`Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi.

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Vyřazení a opětovné vytvoření databáze

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte databázi a pomocí migrace znovu vytvořte databázi. Chcete-li odstranit databázi, odstraňte soubor databáze (*MvcMovie. DB*). Pak spusťte `ef database update` příkaz:

```dotnetcli
dotnet ef database update
```

---

Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole. Pokud databáze není osazena, nastavte v metodě bod přerušení `SeedData.Initialize` .

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Předchozí: Přidání vyhledávání](xref:tutorials/razor-pages/search) 
>  [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)

::: moniker-end
