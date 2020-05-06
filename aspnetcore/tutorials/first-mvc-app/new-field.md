---
title: Přidání nového pole do ASP.NET Core aplikace MVC
author: rick-anderson
description: Naučte se, jak pomocí Migrace Entity Framework Code First přidat nové pole do modelu a migrovat tuto změnu do databáze.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 0cc708734726e5779951d63ac10d1b750b1cc2ac
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777069"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>Přidání nového pole do ASP.NET Core aplikace MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:

* Přidejte do modelu nové pole.
* Migrujte nové pole do databáze.

Když se Code First EF používá k automatickému vytvoření databáze, Code First:

* Přidá tabulku do databáze pro sledování schématu databáze.
* Ověřuje, zda je databáze synchronizována s třídami modelů, ze kterých byla vygenerována. Pokud nejsou synchronizovány, EF vyvolá výjimku. Díky tomu je snazší najít nekonzistentní problémy s databází či kódem.

## <a name="add-a-rating-property-to-the-movie-model"></a>Přidat vlastnost hodnocení do modelu videa

Přidat `Rating` vlastnost do *modelů/filmu. cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Sestavení aplikace

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Příkaz ⌘ + B

------

Vzhledem k tomu, že jste do `Movie` třídy přidali nové pole, je nutné aktualizovat seznam vazeb, aby byla tato nová vlastnost zahrnutá. V *MoviesController.cs*aktualizujte `[Bind]` atribut pro metody `Create` a `Edit` akce tak, aby zahrnovaly `Rating` vlastnost:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Aktualizujte šablony zobrazení, aby bylo možné zobrazit, vytvořit a upravit novou `Rating` vlastnost v zobrazení prohlížeče.

Upravte soubor */views/Movies/index.cshtml* a přidejte `Rating` pole:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Aktualizujte */views/Movies/Create.cshtml* s `Rating` polem.

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio/Visual Studio pro Mac](#tab/visual-studio+visual-studio-mac)

Můžete zkopírovat a vložit předchozí "skupinu formulářů" a nechat intelliSense, aby vám aktualizovala pole. Technologie IntelliSense spolupracuje s [pomocníky značek](xref:mvc/views/tag-helpers/intro).

![Vývojář zadal písmeno R pro hodnotu atributu ASP-for v druhém prvku popisku zobrazení. V kontextové nabídce technologie IntelliSense se objevila pole k dispozici, včetně hodnocení, které je automaticky zvýrazněno v seznamu. Když vývojář klikne na pole nebo stiskne klávesu ENTER na klávesnici, hodnota se nastaví na hodnocení.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Aktualizujte zbývající šablony.

Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec. Tato změna se zobrazuje níže, ale tuto změnu budete chtít udělat pro každou z nich `new Movie`.

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole. Pokud je spuštěno nyní, je vyvolána `SqlException` následující:

`SqlException: Invalid column name 'Rating'.`

K této chybě dochází, protože aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů existující databáze. (V tabulce databáze `Rating` není žádný sloupec.)

K řešení této chyby je potřeba několik přístupů:

1. Entity Framework automaticky vyřadit a znovu vytvořit databázi na základě nového schématu třídy modelu. Tento přístup je velmi výhodný v rané fázi vývoje, když provádíte aktivní vývoj na testovací databázi. umožňuje rychlou vývoj modelu a schématu databáze dohromady. Nevýhodou, ale je to, že ztratíte stávající data v databázi, takže nechcete tento přístup použít v provozní databázi. Použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem pro vývoj aplikace. To je dobrý přístup pro prvotní vývoj a při použití SQLite.

2. Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu. Výhodou tohoto přístupu je, že zachováte data. Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.

3. K aktualizaci schématu databáze použijte Migrace Code First.

Pro tento kurz se používá Migrace Code First.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.

  ![PMC – nabídka](adding-model/_static/pmc.png)

Do PMC zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` Příkaz instruuje rámec migrace, aby kontroloval aktuální `Movie` model s aktuálním `Movie` databázovým schématem a vytvořil potřebný kód pro migraci databáze do nového modelu.

Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace. Je užitečné použít pro migrační soubor smysluplný název.

Pokud jsou všechny záznamy v databázi odstraněny, metoda Initialize vytvoří databázi a zahrne `Rating` pole.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Odstraňte databázi a pomocí migrace znovu vytvořte databázi. Chcete-li odstranit databázi, odstraňte soubor databáze (*MvcMovie. DB*). Pak spusťte `ef database update` příkaz:

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

Spusťte aplikaci a ověřte, že je možné vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole. Aktualizujte aplikaci:

* Přidejte `Rating` pole do šablon zobrazení `Edit`, `Details`a `Delete` .
* Aktualizujte vazbu v metodě Upravit akci pro `MoviesController`.

> [!div class="step-by-step"]
> [Předchozí](search.md)
> [Další](validation.md)
