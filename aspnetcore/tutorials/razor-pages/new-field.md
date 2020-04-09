---
title: Přidání nového pole na stránku Razor v ASP.NET jádru
author: rick-anderson
description: Ukazuje, jak přidat nové pole na stránku Razor s jádrem entity frameworku
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: d34b938dbd1b512ddb167cac0c035837889cd38f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657813"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Přidání nového pole na stránku Razor v ASP.NET jádru

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V této části [entity framework](/ef/core/get-started/aspnetcore/new-db) code first migrace se používá k:

* Přidejte do modelu nové pole.
* Migrujte změnu nového schématu pole do databáze.

Při použití EF code First k automatickému vytvoření databáze, Code First:

* Přidá `__EFMigrationsHistory` do databáze tabulku ke sledování, zda je schéma databáze synchronizováno s třídami modelu, ze kterých byla generována.
* Pokud třídy modelu nejsou synchronizovány s DB, EF vyvolá výjimku.

Automatické ověření schématu/modelu v synchronizaci usnadňuje hledání nekonzistentních problémů s databází a kódem.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti hodnocení do filmového modelu

Otevřete soubor *Models/Movie.cs* `Rating` a přidejte vlastnost:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavení aplikace

Upravte *stránky/filmy/index.cshtml*a `Rating` přidejte pole:

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

Aktualizujte následující stránky:

* Přidejte `Rating` pole na stránky Odstranit a Podrobnosti.
* Aktualizujte [create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) s polem. `Rating`
* Přidejte `Rating` pole na stránku Úpravy.

Aplikace nebude fungovat, dokud db je aktualizován tak, aby zahrnovala nové pole. Spuštění aplikace bez aktualizace databáze `SqlException`vyvolá :

`SqlException: Invalid column name 'Rating'.`

Výjimka `SqlException` je způsobena tím, že aktualizovaná třída modelu Movie se liší od schématu tabulky Movie databáze. (V databázové `Rating` tabulce není žádný sloupec.)

Existuje několik přístupů k řešení chyby:

1. Mají entity framework automaticky přetažení a znovu vytvořit databázi pomocí nového schématu třídy modelu. Tento přístup je vhodný na počátku vývojového cyklu; umožňuje rychle vyvíjet schéma modelu a databáze společně. Nevýhodou je, že ztratíte existující data v databázi. Nepoužívejte tento přístup v produkční databázi! Uvolnění DB na změny schématu a pomocí inicializátoru automaticky osiva databáze s testovací data je často produktivní způsob, jak vyvinout aplikaci.

2. Explicitně upravte schéma existující databáze tak, aby odpovídala třídám modelu. Výhodou tohoto přístupu je, že uchováváte data. Tuto změnu můžete provést ručně nebo vytvořením skriptu pro změnu databáze.

3. K aktualizaci schématu databáze použijte migrace prvního kódu.

V tomto kurzu použijte migrace Code First.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Ukázková změna je uvedena níže, ale tuto změnu `new Movie` budete chtít provést pro každý blok.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Podívejte se na [vyplněný soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).

Sestavte řešení.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Přidání migrace do pole hodnocení

V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.
Do pmc zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

Příkaz `Add-Migration` říká rozhraní:

* Porovnejte `Movie` `Movie` model se schématem DB.
* Vytvořte kód pro migraci schématu DB do nového modelu.

Název "Hodnocení" je libovolný a používá se k pojmenování migračního souboru. Je užitečné použít smysluplný název pro soubor migrace.

Příkaz `Update-Database` říká rozhraní použít změny schématu v databázi a zachovat existující data.

<a name="ssox"></a>

Pokud odstraníte všechny záznamy v DB, inicializátor zaznamená DB a zahrne `Rating` pole. Můžete to provést pomocí odstranění odkazů v prohlížeči nebo z [Průzkumníka objektů sql serveru](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Další možností je odstranění databáze a použití migrace znovu vytvořit databázi. Odstranění databáze ve SSOX:

* Vyberte databázi ve SSOX.
* Klikněte pravým tlačítkem myši na databázi a vyberte *příkaz Odstranit*.
* Zaškrtněte **políčko Zavřít existující připojení**.
* Vyberte **OK**.
* V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Přetažení a opětovné vytvoření databáze

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte složku migrace.  K opětovnému vytvoření databáze použijte následující příkazy.

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Spusťte aplikaci a ověřte, zda `Rating` můžete vytvářet/ upravovat/zobrazovat filmy pomocí pole. Pokud databáze není nasazena, nastavte bod přerušení `SeedData.Initialize` v metodě.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Předchozí: Přidání hledání](xref:tutorials/razor-pages/search)
> [další: Přidání ověření](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V této části [entity framework](/ef/core/get-started/aspnetcore/new-db) code first migrace se používá k:

* Přidejte do modelu nové pole.
* Migrujte změnu nového schématu pole do databáze.

Při použití EF code First k automatickému vytvoření databáze, Code First:

* Přidá do databáze tabulku ke sledování, zda je schéma databáze synchronizováno s třídami modelu, ze kterých byla generována.
* Pokud třídy modelu nejsou synchronizovány s DB, EF vyvolá výjimku.

Automatické ověření schématu/modelu v synchronizaci usnadňuje hledání nekonzistentních problémů s databází a kódem.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti hodnocení do filmového modelu

Otevřete soubor *Models/Movie.cs* `Rating` a přidejte vlastnost:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavení aplikace

Upravte *stránky/filmy/index.cshtml*a `Rating` přidejte pole:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Aktualizujte následující stránky:

* Přidejte `Rating` pole na stránky Odstranit a Podrobnosti.
* Aktualizujte [create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s polem. `Rating`
* Přidejte `Rating` pole na stránku Úpravy.

Aplikace nebude fungovat, dokud db je aktualizován tak, aby zahrnovala nové pole. Pokud běží nyní, aplikace `SqlException`vyvolá :

`SqlException: Invalid column name 'Rating'.`

Tato chyba je způsobena tím, že aktualizovaná třída modelu Movie se liší od schématu tabulky Movie databáze. (V databázové `Rating` tabulce není žádný sloupec.)

Existuje několik přístupů k řešení chyby:

1. Mají entity framework automaticky přetažení a znovu vytvořit databázi pomocí nového schématu třídy modelu. Tento přístup je vhodný na počátku vývojového cyklu; umožňuje rychle vyvíjet schéma modelu a databáze společně. Nevýhodou je, že ztratíte existující data v databázi. Nepoužívejte tento přístup v produkční databázi! Uvolnění DB na změny schématu a pomocí inicializátoru automaticky osiva databáze s testovací data je často produktivní způsob, jak vyvinout aplikaci.

2. Explicitně upravte schéma existující databáze tak, aby odpovídala třídám modelu. Výhodou tohoto přístupu je, že uchováváte data. Tuto změnu můžete provést ručně nebo vytvořením skriptu pro změnu databáze.

3. K aktualizaci schématu databáze použijte migrace prvního kódu.

V tomto kurzu použijte migrace Code First.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Ukázková změna je uvedena níže, ale tuto změnu `new Movie` budete chtít provést pro každý blok.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Podívejte se na [vyplněný soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Sestavte řešení.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Přidání migrace do pole hodnocení

V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.
Do pmc zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

Příkaz `Add-Migration` říká rozhraní:

* Porovnejte `Movie` `Movie` model se schématem DB.
* Vytvořte kód pro migraci schématu DB do nového modelu.

Název "Hodnocení" je libovolný a používá se k pojmenování migračního souboru. Je užitečné použít smysluplný název pro soubor migrace.

Příkaz `Update-Database` říká rozhraní pro použití změny schématu v databázi.

<a name="ssox"></a>

Pokud odstraníte všechny záznamy v DB, inicializátor zaznamená DB a zahrne `Rating` pole. Můžete to provést pomocí odstranění odkazů v prohlížeči nebo z [Průzkumníka objektů sql serveru](xref:tutorials/razor-pages/sql#ssox) (SSOX).

Další možností je odstranění databáze a použití migrace znovu vytvořit databázi. Odstranění databáze ve SSOX:

* Vyberte databázi ve SSOX.
* Klikněte pravým tlačítkem myši na databázi a vyberte *příkaz Odstranit*.
* Zaškrtněte **políčko Zavřít existující připojení**.
* Vyberte **OK**.
* V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Přetažení a opětovné vytvoření databáze

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte databázi a použijte migrace znovu vytvořit databázi. Chcete-li databázi odstranit, odstraňte databázový soubor (*MvcMovie.db*). Pak spusťte `ef database update` příkaz:

```dotnetcli
dotnet ef database update
```

---

Spusťte aplikaci a ověřte, zda `Rating` můžete vytvářet/ upravovat/zobrazovat filmy pomocí pole. Pokud databáze není nasazena, nastavte bod přerušení `SeedData.Initialize` v metodě.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Předchozí: Přidání hledání](xref:tutorials/razor-pages/search)
> [další: Přidání ověření](xref:tutorials/razor-pages/validation)

::: moniker-end
