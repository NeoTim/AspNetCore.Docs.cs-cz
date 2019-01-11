---
title: Přidání nového pole do aplikace ASP.NET Core MVC
author: rick-anderson
description: Další informace o použití migrace Entity Framework Code First pro přidání nového pole do modelu a migrovat tuto změnu do databáze.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 7993b36bf9115225e082d2929bb253aba5b18310
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207366"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>Přidání nového pole do aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části [Entity Framework](/ef/core/get-started/aspnetcore/new-db) se používá k migrace Code First:

* Přidání nového pole do modelu.
* Migrace nové pole do databáze.

Když platforem EF Code First umožňuje automaticky vytvořte databázi, Code First:

* Tabulka se přidá do databáze, kterou chcete sledovat schéma databáze.
* Ověřte, že je synchronizovaný s tříd modelu, které byly vygenerovány z databáze. Pokud nejsou synchronizované, EF vyvolá výjimku. Díky tomu je snazší najít problémy s nekonzistentní databáze nebo kódu.

## <a name="add-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti hodnocení filmů modelu

Přidat `Rating` vlastnost *Models/Movie.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Vytvořte aplikaci (Ctrl + Shift + B).

Protože jsme přidali nové pole do `Movie` třídy, budete muset aktualizovat vazbu seznamu povolených, aby tuto novou vlastnost budou zahrnuty. V *MoviesController.cs*, aktualizovat `[Bind]` atribut pro obě `Create` a `Edit` metody akce, které chcete zahrnout `Rating` vlastnost:

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Aktualizace šablon zobrazení, aby bylo možné zobrazit, vytvořit a upravit novou `Rating` vlastností v okně prohlížeče.

Upravit */Views/Movies/Index.cshtml* a přidejte `Rating` pole:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Aktualizace */Views/Movies/Create.cshtml* s `Rating` pole.

<!-- VS -------------------------->
# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[Visual Studio nebo Visual Studio pro Mac](#tab/visual-studio+visual-studio-mac)

Můžete kopírovat/vložit předchozí "formuláře skupinu" a aktualizujte pole pomohou technologie intelliSense. Technologie IntelliSense funguje s [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).

![Vývojář napsal písmeno R pro hodnotu atributu ASP-pro druhý popisek prvku zobrazení. Jeho ikona místní nabídku technologie Intellisense zobrazí dostupná pole, včetně hodnocení, který je zvýrazněn v seznamu automaticky. Když vývojář klikne pole nebo stiskne klávesu Enter na klávesnici, hodnota se nastaví na hodnocení.](new-field/_static/cr.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)
<!-- This tab intentionally left blank. -->
---  
<!-- End of VS tabs -->

Aktualizace `SeedData` třídy tak, že poskytuje hodnoty pro nový sloupec. Ukázka změnu je uveden níže, ale budete chtít tuto změnu pro každou `new Movie`.

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

Aplikace nebude fungovat, dokud databáze je aktualizováno, aby zahrnovalo nové pole. Pokud se má spustit nyní následující `SqlException` je vyvolána výjimka:

`SqlException: Invalid column name 'Rating'.`

K této chybě dochází, protože se liší od schématu tabulky Movie existující databáze aktualizované třídy modelu video. (Neexistuje žádný `Rating` sloupec v tabulce databáze.)

Řešení chyby několika způsoby:

1. Máte rozhraní Entity Framework automaticky vyřadit a znovu vytvořit databázi založené na nové schéma třídy modelu. Tento přístup je velmi vhodné v rané fázi vývojového cyklu, pokud provádíte databázi testu; s aktivním vývojem umožňuje rychlý rozvoj schématu modelu a databáze společně. Nevýhodou, je však dojít ke ztrátě existujících dat v databázi, takže nechcete tuto metodu použijte u provozní databáze. Použití inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace. To je dobrý nápad pro raného vývoje a při použití SQLite.

2. Explicitně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu. Výhodou tohoto přístupu je, že zachováte vaše data. Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.

3. Pomocí migrace Code First aktualizovat schéma databáze.

V tomto kurzu se používá migrace Code First.

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet > Konzola správce balíčků**.

  ![PMC nabídky](adding-model/_static/pmc.png)

V konzole PMC zadejte následující příkazy:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` Příkaz říká rozhraní framework migrace ke kontrole aktuální `Movie` modelů s aktuálním `Movie` schématu databáze a vytvářet kód potřebné k migraci databáze na nový model.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code nebo Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Spusťte následující příkaz:

```cli
dotnet ef migrations add Rating
dotnet ef database update
```

---  
<!-- End of VS tabs -->

Název "Hodnocení" je volitelný a slouží k pojmenování souboru migrace. Je vhodné použít smysluplný název souboru migrace.

Pokud se odstraní všechny záznamy z databáze, Metoda initialize bude počáteční hodnoty databáze a uveďte `Rating` pole.

Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole. Měli byste přidat `Rating` pole `Edit`, `Details`, a `Delete` zobrazení šablon.

> [!div class="step-by-step"]
> [Předchozí](search.md)
> [další](validation.md)  
