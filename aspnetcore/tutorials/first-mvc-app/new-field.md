---
title: Přidání nového pole do aplikace Core MVC ASP.NET
author: rick-anderson
description: Zjistěte, jak pomocí migrace entity framework code first přidat nové pole do modelu a migrovat, které se změní do databáze.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: a5ea9b75cf8bb1f31cb07a2b32f361bdbfd4efa3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662902"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>Přidání nového pole do aplikace Core MVC ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části [entity framework](/ef/core/get-started/aspnetcore/new-db) code first migrace se používá k:

* Přidejte do modelu nové pole.
* Migrujte nové pole do databáze.

Při EF Code First se používá k automatickému vytvoření databáze, Kód první:

* Přidá do databáze tabulku ke sledování schématu databáze.
* Ověří, že databáze je synchronizována s třídami modelu, ze kterých byla vygenerována. Pokud nejsou synchronizovány, EF vyvolá výjimku. To usnadňuje hledání nekonzistentní chod databáze/kódu.

## <a name="add-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti hodnocení do filmového modelu

Přidat `Rating` vlastnost *modely/Movie.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavení aplikace

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Příkaz - + B

------

Vzhledem k tomu, že `Movie` jste do třídy přidali nové pole, je třeba aktualizovat bílý seznam vazeb, aby byla zahrnuta tato nová vlastnost. V *MoviesController.cs*aktualizujte `[Bind]` atribut `Create` pro `Edit` metody i `Rating` metody akce tak, aby zahrnoval vlastnost:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Aktualizujte šablony zobrazení tak, aby zobrazovala, `Rating` vytvářela a upravovala novou vlastnost v zobrazení prohlížeče.

Upravte soubor */Views/Movies/Index.cshtml* `Rating` a přidejte pole:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Aktualizujte *parametr /Views/Movies/Create.cshtml* polem. `Rating`

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio / Visual Studio pro Mac](#tab/visual-studio+visual-studio-mac)

Můžete zkopírovat/vložit předchozí "skupina formulářů" a nechat intelliSense, aby vám pomohla aktualizovat pole. Technologie IntelliSense pracuje s [pomocníky značek](xref:mvc/views/tag-helpers/intro).

![Vývojář zadal písmeno R pro hodnotu atributu asp-for v druhém prvku popisku zobrazení. Kontextová nabídka Intellisense zobrazuje dostupná pole, včetně hodnocení, která je v seznamu automaticky zvýrazněna. Když vývojář klikne na pole nebo stiskne klávesu Enter na klávesnici, bude hodnota nastavena na hodnotu Hodnocení.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Aktualizujte zbývající šablony.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Ukázková změna je uvedena níže, ale tuto změnu `new Movie`budete chtít provést pro každou z nich .

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Aplikace nebude fungovat, dokud db je aktualizován tak, aby zahrnovala nové pole. Pokud je spuštěn nyní, `SqlException` je vyvolána následující:

`SqlException: Invalid column name 'Rating'.`

K této chybě dochází, protože aktualizovaná třída modelu Movie se liší od schématu tabulky Movie existující databáze. (V databázové `Rating` tabulce není žádný sloupec.)

Existuje několik přístupů k řešení chyby:

1. Mají entity framework automaticky přetažení a znovu vytvořit databázi na základě nového schématu třídy modelu. Tento přístup je velmi výhodné na počátku vývojového cyklu, když děláte aktivní vývoj na testovací databázi; umožňuje rychle vyvíjet schéma modelu a databáze společně. Nevýhodou však je, že ztratíte existující data v databázi - takže nechcete používat tento přístup v produkční databázi! Použití inicializátoru automaticky osiva databáze s testovací data je často produktivní způsob, jak vyvinout aplikaci. To je dobrý přístup pro časný vývoj a při použití SQLite.

2. Explicitně upravte schéma existující databáze tak, aby odpovídala třídám modelu. Výhodou tohoto přístupu je, že uchováváte data. Tuto změnu můžete provést ručně nebo vytvořením skriptu pro změnu databáze.

3. K aktualizaci schématu databáze použijte migrace prvního kódu.

Pro účely tohoto kurzu se používá migrace code first.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.

  ![Nabídka PMC](adding-model/_static/pmc.png)

Do pmc zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

Příkaz `Add-Migration` sděluje rozhraní pro `Movie` migraci, `Movie` aby prozkoumalaktuální model s aktuálním schématem DB a vytvořil potřebný kód pro migraci DB do nového modelu.

Název "Hodnocení" je libovolný a používá se k pojmenování migračního souboru. Je užitečné použít smysluplný název pro soubor migrace.

Pokud jsou odstraněny všechny záznamy v DB, inicializovat metoda bude `Rating` osiva DB a zahrnout pole.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte databázi a použijte migrace znovu vytvořit databázi. Chcete-li databázi odstranit, odstraňte databázový soubor (*MvcMovie.db*). Pak spusťte `ef database update` příkaz:

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

Spusťte aplikaci a ověřte, zda `Rating` můžete vytvářet, upravovat a zobrazovat filmy pomocí pole. Aktualizace aplikace:

* Přidejte `Rating` pole `Edit`do `Details`šablony `Delete` , a zobrazte je.
* Aktualizujte vazbu v metodě `MoviesController`akce úprav .

> [!div class="step-by-step"]
> [Předchozí](search.md)
> [další](validation.md)
