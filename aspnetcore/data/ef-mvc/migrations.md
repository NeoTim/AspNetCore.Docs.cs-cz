---
title: 'Kurz: použití funkcí migrace – ASP.NET MVC s EF Core'
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v aplikaci ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/migrations
ms.openlocfilehash: b8701687d97f5fe940e2f39fca9c3f98052660be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773520"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Kurz: použití funkcí migrace – ASP.NET MVC s EF Core

V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu. V pozdějších kurzech přidáte při změně datového modelu další migrace.

V tomto kurzu jste:

> [!div class="checklist"]
> * Další informace o migraci
> * Změna připojovacího řetězce
> * Vytvoření prvotní migrace
> * Projděte si metody směrem nahoru a dolů
> * Další informace o snímku datového modelu
> * Použití migrace

## <a name="prerequisites"></a>Požadavky

* [Řazení, filtrování a stránkování](sort-filter-page.md)

## <a name="about-migrations"></a>O migracích

Při vývoji nové aplikace se datový model často mění a pokaždé, když se model změní, se nesynchronizuje s databází. Tyto kurzy jste začali konfigurací Entity Framework vytvořit databázi, pokud neexistuje. Pak pokaždé, když změníte datový model – můžete přidat, odebrat nebo změnit třídy entit nebo změnit třídu DbContext – můžete odstranit databázi a EF vytvoří novou, která odpovídá modelu, a vyhodnotit jeho semena s testovacími daty.

Tato metoda uchování databáze v synchronizaci s datovým modelem funguje dobře, dokud aplikaci nenainstalujete do produkčního prostředí. Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která chcete zachovat, a nechcete přijít o všechny pokaždé, když uděláte změnu, jako je přidání nového sloupce. Funkce migrace EF Core vyřeší tento problém tím, že umožňuje EF aktualizovat schéma databáze místo vytvoření nové databáze.

Pro práci s migrací můžete použít **konzolu Správce balíčků** (PMC) nebo rozhraní příkazového řádku (CLI).  Tyto kurzy ukazují, jak používat příkazy rozhraní příkazového řádku. Informace o PMC jsou na [konci tohoto kurzu](#pmc).

## <a name="change-the-connection-string"></a>Změna připojovacího řetězce

V souboru *appSettings. JSON* změňte název databáze v připojovacím řetězci na ContosoUniversity2 nebo jiný název, který jste nepoužili v počítači, který používáte.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Tato změna nastaví projekt tak, že první migrace vytvoří novou databázi. Tento postup není potřebný k tomu, aby bylo možné začít s migracemi, ale později se dozvíte, proč je to dobrý nápad.

> [!NOTE]
> Jako alternativu ke změně názvu databáze můžete databázi odstranit. Použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo příkaz `database drop` CLI:
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> V následující části je vysvětleno, jak spustit příkazy rozhraní příkazového řádku.

## <a name="create-an-initial-migration"></a>Vytvoření prvotní migrace

Uložte změny a sestavte projekt. Pak otevřete příkazové okno a přejděte do složky projektu. Tady je rychlý způsob, jak to udělat:

* V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a v místní nabídce vyberte možnost **Otevřít složku v Průzkumníku souborů** .

  ![Otevřít v Průzkumníkovi souborů – položka nabídky](migrations/_static/open-in-file-explorer.png)

* Do adresního řádku zadejte "cmd" a stiskněte klávesu ENTER.

  ![Otevřít příkazové okno](migrations/_static/open-command-window.png)

V příkazovém okně zadejte následující příkaz:

```dotnetcli
dotnet ef migrations add InitialCreate
```

V příkazovém okně se zobrazí výstup podobný následujícímu:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> Pokud se zobrazí chybová zpráva *bez nalezeného spustitelného souboru, který odpovídá příkazu "dotnet-EF"*, přečtěte si [Tento Blogový příspěvek](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) , kde najdete pomoc při řešení potíží

Pokud se zobrazí chybová zpráva "*nelze získat přístup k souboru... ContosoUniversity. dll, protože ho používá jiný proces.*", najděte ikonu IIS Express v hlavním panelu systému Windows a klikněte na ni pravým tlačítkem myši a potom klikněte na **ContosoUniversity > zastavit web**.

## <a name="examine-up-and-down-methods"></a>Projděte si metody směrem nahoru a dolů

Když jste provedli `migrations add` příkaz, EF vygeneroval kód, který vytvoří databázi zcela od začátku. Tento kód se nachází ve složce *migrace* v souboru s názvem * \<timestamp>_InitialCreate. cs*. Metoda třídy vytvoří tabulky databáze, které odpovídají sadám entit datového modelu, a `Down` metoda je odstraní, jak je znázorněno v následujícím příkladu. `InitialCreate` `Up`

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Migrace volá `Up` metodu, která implementuje změny datového modelu pro migraci. Když zadáte příkaz pro vrácení aktualizace, migrace volá `Down` metodu.

Tento kód je určen pro počáteční migraci, která byla vytvořena při zadání `migrations add InitialCreate` příkazu. Parametr názvu migrace (v příkladu "InitialCreate") se používá pro název souboru a může být libovolný, který chcete. Nejlepší je zvolit slovo nebo frázi, která shrnuje, co se v migraci provádí. Můžete třeba pojmenovat pozdější migraci "AddDepartmentTable".

Pokud jste vytvořili počáteční migraci i v případě, že databáze již existuje, je vytvořen kód pro vytvoření databáze, ale nemusí být spuštěn, protože databáze již odpovídá datovému modelu. Když nasadíte aplikaci do jiného prostředí, kde databáze ještě neexistuje, tento kód se spustí, aby se vytvořila vaše databáze, takže je dobré ho nejdřív otestovat. To je důvod, proč jste změnili název databáze v připojovacím řetězci dříve, takže migrace mohou vytvořit nové od začátku.

## <a name="the-data-model-snapshot"></a>Snímek datového modelu

Migrace vytvoří *snímek* aktuálního schématu databáze v části *migrations/SchoolContextModelSnapshot. cs*. Když přidáte migraci, EF určí, co se změnilo, porovnáním datového modelu se souborem snímku.

K odebrání migrace použijte příkaz [dotnet EF migrations Remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) . `dotnet ef migrations remove`odstraní migraci a zajistí správné resetování snímku. Pokud `dotnet ef migrations remove` se nezdaří, `dotnet ef migrations remove -v` použijte k získání dalších informací o selhání.

Další informace o tom, jak se soubor snímku používá, najdete [v tématu EF Core migrace v týmových prostředích](/ef/core/managing-schemas/migrations/teams) .

## <a name="apply-the-migration"></a>Použití migrace

V okně příkazového řádku zadejte následující příkaz, který v něm vytvoří databázi a tabulky.

```dotnetcli
dotnet ef database update
```

Výstup příkazu je podobný `migrations add` příkazu, s tím rozdílem, že se zobrazí protokoly pro příkazy SQL, které nastaví databázi. Většina protokolů je vynechána v následujícím ukázkovém výstupu. Pokud nechcete, aby se tato úroveň podrobností zobrazovala v protokolových zprávách, můžete změnit úroveň protokolu v souboru *appSettings. Soubor Development. JSON* . Další informace naleznete v tématu <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

Pomocí **Průzkumník objektů systému SQL Server** můžete zkontrolovat databázi jako v prvním kurzu.  Všimnete si přidání \_ \_tabulky EFMigrationsHistory, která uchovává informace o tom, které migrace byly pro databázi aplikovány. Zobrazit data v této tabulce a pro první migraci se zobrazí jeden řádek. (Poslední protokol v předchozím příkladu výstupu rozhraní příkazového řádku ukazuje příkaz INSERT, který tento řádek vytvoří.)

Spusťte aplikaci, abyste ověřili, že všechno pořád funguje stejně jako předtím.

![Stránka indexu studentů](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Porovnání CLI a PMC

Nástroje EF pro správu migrací jsou dostupné z .NET Core CLI příkazy nebo z rutin prostředí PowerShell v okně **konzoly Správce balíčků** sady Visual Studio (PMC). V tomto kurzu se dozvíte, jak používat rozhraní příkazového řádku, ale pokud chcete, můžete použít PMC.

Příkazy EF pro příkazy PMC jsou v balíčku [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) . Tento balíček je zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), takže nemusíte přidávat odkaz na balíček, pokud vaše aplikace obsahuje odkaz na balíček pro `Microsoft.AspNetCore.App`.

**Důležité informace:** Nejedná se o stejný balíček jako ten, který nainstalujete pro rozhraní příkazového řádku úpravou souboru *. csproj* . Název tohoto typu končí na `Tools`, na rozdíl od názvu balíčku CLI, který končí. `Tools.DotNet`

Další informace o příkazech rozhraní příkazového řádku naleznete v tématu [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

Další informace o příkazech PMC naleznete v tématu [Konzola správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Získání kódu

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Další krok

V tomto kurzu jste:

> [!div class="checklist"]
> * Seznámili jste se s migracemi
> * Seznámili jste se s balíčky migrace NuGet.
> * Změna připojovacího řetězce
> * Vytvořila se počáteční migrace.
> * Prověření a snížení metod
> * Dozvěděli jste se o snímku datového modelu.
> * Migrace se používala

Přejděte k dalšímu kurzu a začněte hledat v pokročilejších tématech o rozšiřování datového modelu. Podél toho, jak vytvoříte a použijete další migrace.

> [!div class="nextstepaction"]
> [Vytvoření a použití dalších migrací](complex-data-model.md)
