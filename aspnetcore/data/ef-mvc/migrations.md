---
title: 'Kurz: Použití funkce migrace - ASP.NET MVC s EF Core'
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v ASP.NET základní aplikace MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 8b3417205457a5ce5fa16994701a06e2a4d7d350
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665723"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Kurz: Použití funkce migrace - ASP.NET MVC s EF Core

V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu. V pozdějších kurzech přidáte další migrace při změně datového modelu.

V tomto kurzu jste:

> [!div class="checklist"]
> * Další informace o migracích
> * Změna připojovacího řetězce
> * Vytvoření počáteční migrace
> * Zkoumat metody nahoru a dolů
> * Informace o snímku datového modelu
> * Použití migrace

## <a name="prerequisites"></a>Požadavky

* [Řazení, filtrování a stránkování](sort-filter-page.md)

## <a name="about-migrations"></a>O migracích

Při vývoji nové aplikace, datový model se často mění a pokaždé, když se změní model, se synchronizuje s databází. Tyto kurzy jste spustili tak, že jste nakonfigurovali rozhraní Entity Framework k vytvoření databáze, pokud neexistuje. Potom pokaždé, když změníte datový model – přidat, odebrat nebo změnit třídy entit nebo změnit třídu DbContext -- můžete odstranit databázi a EF vytvoří nový model, který odpovídá modelu a semena s testovací data.

Tento způsob udržování databáze v synchronizaci s datovým modelem funguje dobře, dokud nenasadíte aplikaci do produkčního prostředí. Když je aplikace spuštěna v produkčním prostředí, obvykle ukládá data, která chcete zachovat, a nechcete přijít o vše při každé změně, jako je například přidání nového sloupce. Funkce EF Core Migrations tento problém řeší povolením EF k aktualizaci schématu databáze namísto vytvoření nové databáze.

Chcete-li pracovat s migracemi, můžete použít **konzolu Správce balíčků** (PMC) nebo CLI.  Tyto kurzy ukazují, jak používat příkazy příkazového příkazu. Informace o PMC je [na konci tohoto kurzu](#pmc).

## <a name="change-the-connection-string"></a>Změna připojovacího řetězce

V souboru *appsettings.json* změňte název databáze v připojovacím řetězci na ContosoUniversity2 nebo jiný název, který jste v počítači, který používáte, nepoužili.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Tato změna nastaví projekt tak, aby první migrace vytvoří novou databázi. To není nutné začít s migrací, ale uvidíte později, proč je to dobrý nápad.

> [!NOTE]
> Jako alternativu ke změně názvu databáze můžete databázi odstranit. Použijte **Průzkumník objektů SERVERU SQL** Server `database drop` (SSOX) nebo příkaz cli:
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> V následující části je vysvětleno, jak spustit příkazy příkazového příkazu.

## <a name="create-an-initial-migration"></a>Vytvoření počáteční migrace

Uložte změny a vytvořte projekt. Potom otevřete příkazové okno a přejděte do složky projektu. Zde je rychlý způsob, jak to udělat:

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a z kontextové nabídky zvolte **Otevřít složku v Průzkumníkovi souborů.**

  ![Otevřít v položce nabídky Průzkumníksouborů](migrations/_static/open-in-file-explorer.png)

* Do adresního řádku zadejte "cmd" a stiskněte Enter.

  ![Otevřít příkazové okno](migrations/_static/open-command-window.png)

Do příkazového okna zadejte následující příkaz:

```dotnetcli
dotnet ef migrations add InitialCreate
```

Výstup se zobrazí jako následující v příkazovém okně:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> Pokud se zobrazí chybová zpráva *Žádný spustitelný soubor nenašel odpovídající příkaz "dotnet-ef"*, podívejte se na [tento příspěvek blogu,](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) kde naleznete nápovědu k řešení potíží.

Pokud se zobrazí chybová zpráva "*nelze získat přístup k souboru ... Soubor ContosoUniversity.dll, protože je používán jiným procesem.*", vyhledejte ikonu IIS Express v systémové liště systému Windows a klepněte pravým tlačítkem myši na něj a klepněte na příkaz **ContosoUniversity > Stop Site**.

## <a name="examine-up-and-down-methods"></a>Zkoumat metody nahoru a dolů

Při spuštění příkazu `migrations add` EF vygeneroval kód, který vytvoří databázi od začátku. Tento kód je ve složce *Migrace* v souboru s názvem * \<timestamp>_InitialCreate.cs*. Metoda `Up` `InitialCreate` třídy vytvoří databázové tabulky, které odpovídají sady entit datového modelu a `Down` metoda je odstraní, jak je znázorněno v následujícím příkladu.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Migrace volá `Up` metodu k implementaci změn datového modelu pro migraci. Když zadáte příkaz pro vrácení aktualizace zpět, `Down` migrace volá metodu.

Tento kód je určen pro počáteční migraci, která byla vytvořena při zadání příkazu. `migrations add InitialCreate` Parametr názvu migrace ("InitialCreate" v příkladu) se používá pro název souboru a může být libovolné. Je nejlepší zvolit slovo nebo frázi, která shrnuje, co se v migraci děje. Můžete například pojmenovat pozdější migraci "AddDepartmentTable".

Pokud jste vytvořili počáteční migraci, když databáze již existuje, je generován kód vytvoření databáze, ale nemusí být spuštěn, protože databáze již odpovídá datovému modelu. Když nasadíte aplikaci do jiného prostředí, kde databáze ještě neexistuje, tento kód se spustí k vytvoření databáze, takže je vhodné ji nejprve otestovat. To je důvod, proč jste změnili název databáze v připojovacím řetězci dříve - tak, aby migrace můžete vytvořit nový od začátku.

## <a name="the-data-model-snapshot"></a>Snímek datového modelu

Migrace vytvoří *snímek* aktuálního schématu databáze v *migrace/SchoolContextModelSnapshot.cs*. Když přidáte migraci, EF určuje, co se změnilo porovnáním datového modelu se souborem snímku.

K odebrání migrace použijte příkaz [remove migrace dotnet ef.](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) `dotnet ef migrations remove`odstraní migraci a zajistí správné resetování snímku. Pokud `dotnet ef migrations remove` se `dotnet ef migrations remove -v` nezdaří, použijte k získání dalších informací o selhání.

Další informace o tom, jak se soubor snímku používá, najdete [v tématu migrace jádra EF v týmových prostředích.](/ef/core/managing-schemas/migrations/teams)

## <a name="apply-the-migration"></a>Použití migrace

V příkazovém okně zadejte následující příkaz pro vytvoření databáze a tabulek v něm.

```dotnetcli
dotnet ef database update
```

Výstup z příkazu je `migrations add` podobný příkazu, s tím rozdílem, že se zobrazí protokoly pro příkazy SQL, které nastavují databázi. Většina protokolů jsou vynechány v následujícím ukázkovém výstupu. Pokud nechcete vidět tuto úroveň podrobností ve zprávách protokolu, můžete změnit úroveň protokolu v *nastavení aplikace. Soubor Development.json.* Další informace naleznete v tématu <xref:fundamentals/logging/index>.

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

Pomocí **Průzkumníka objektů SERVERU SQL Server** zkontrolujte databázi stejně jako v prvním kurzu.  Všimněte si přidání \_ \_EFMigrationsHistory tabulka, která sleduje, které migrace byly použity do databáze. Zobrazte data v této tabulce a uvidíte jeden řádek pro první migraci. (Poslední protokol v předchozím příkladu výstupu příkazového řádku zobrazuje příkaz INSERT, který vytvoří tento řádek.)

Spusťte aplikaci a ověřte, zda vše stále funguje stejně jako dříve.

![Stránka Studentský index](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Porovnání CLI a PMC

Nástroj EF pro správu migrací je k dispozici z příkazů rozhraní PŘÍKAZOvého příkazu .NET Core nebo z rutin prostředí PowerShell v okně PMC (Visual Studio **Package Manager Console).** Tento kurz ukazuje, jak používat cli, ale můžete použít PMC, pokud dáváte přednost.

Příkazy EF pro příkazy PMC jsou v balíčku [Microsoft.EntityFrameworkCore.Tools.](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) Tento balíček je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), takže není nutné přidávat odkaz na balíček, pokud vaše aplikace obsahuje odkaz na balíček pro `Microsoft.AspNetCore.App`.

**Důležité:** Nejedná se o stejný balíček jako ten, který nainstalujete pro příkaz cli úpravou souboru *.csproj.* Název tohoto končí na `Tools`, na rozdíl od názvu `Tools.DotNet`balíčku CLI, který končí v .

Další informace o příkazech rozhraní příkazového příkazu naleznete [v tématu rozhraní příkazového příkazu .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

Další informace o příkazech PMC naleznete v [tématu Package Manager Console (Visual Studio).](/ef/core/miscellaneous/cli/powershell)

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Další krok

V tomto kurzu jste:

> [!div class="checklist"]
> * Informace o migracích
> * Informace o balíčcích migrace NuGet
> * Změna připojovacího řetězce
> * Vytvoření počáteční migrace
> * Zkoumané metody nahoru a dolů
> * Informace o snímku datového modelu
> * Použitá migrace

Přejde mekontější kurz a začněte se zabývat pokročilejšími tématy o rozšíření datového modelu. Cestou budete vytvářet a používat další migrace.

> [!div class="nextstepaction"]
> [Vytvoření a použití dalších migrací](complex-data-model.md)
