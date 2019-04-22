---
title: 'Kurz: Pomocí funkce migrace – ASP.NET MVC s EF Core'
description: V tomto kurzu začnete používat funkci migrace EF Core ke správě změn datových modelů v aplikaci ASP.NET Core MVC.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 3d2ae12bf8eda4f7997008758d4d29434a8371a7
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012601"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Kurz: Pomocí funkce migrace – ASP.NET MVC s EF Core

V tomto kurzu začnete používat funkci migrace EF Core ke správě změn datových modelů. V dalších kurzech přidáte další migrace po provedení změny datového modelu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Další informace o migraci
> * Změňte připojovací řetězec
> * Vytvoření počáteční migraci
> * Prozkoumejte nahoru a dolů metody
> * Další informace o modelu snímků dat
> * Použití migrace

## <a name="prerequisites"></a>Požadavky

* [Řazení, filtrování a stránkování](sort-filter-page.md)

## <a name="about-migrations"></a>Informace o migraci

Při vývoji nových aplikací, datového modelu mění často a pokaždé, když změny modelu, získá synchronizován s databází. Tyto kurzy spustil(a) konfigurace technologie Entity Framework pro vytvoření databáze, pokud neexistuje. Pokaždé, když změníte datový model – přidat, odebrat, nebo změňte tříd entit nebo změnit vaší třídy DbContext – potom můžete odstranit databázi a EF vytvoří nový, který odpovídá modelu a nasazení se nasazuje s testovací data.

Tato metoda zachování databáze synchronizované s datovým modelem funguje dobře, dokud nasadit aplikaci do produkčního prostředí. Když je aplikace spuštěna v produkčním prostředí je obvykle ukládá data, která chcete zachovat, a nechcete ztratit všechno, co pokaždé, když provedete změnu např. přidejte nový sloupec. Funkce migrace EF Core tento problém řeší tím, že EF aktualizovat schéma databáze místo vytvoření nové databáze.

Chcete-li pracovat s migrací, můžete použít **Konzola správce balíčků** (PMC) nebo rozhraní příkazového řádku (CLI).  Tyto kurzy vám ukážou, jak používat příkazy rozhraní příkazového řádku. Informace o konzole PMC je na [konci tohoto kurzu](#pmc).

## <a name="change-the-connection-string"></a>Změňte připojovací řetězec

V *appsettings.json* změňte název databáze v připojovacím řetězci ContosoUniversity2 nebo jiný název, který jste ještě nepoužívali v počítači, který používáte.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Tato změna nastaví projekt tak, aby první migrací vytvoří novou databázi. To není nutné, abyste mohli začít s migrací, ale zobrazí se vám později důvod, proč je vhodné.

> [!NOTE]
> Jako alternativu k změně názvu databáze je odstranit databázi. Použití **Průzkumník objektů systému SQL Server** (SSOX) nebo `database drop` příkazu rozhraní příkazového řádku:
>
> ```console
> dotnet ef database drop
> ```
>
> Následující část vysvětluje, jak spouštět příkazy rozhraní příkazového řádku.

## <a name="create-an-initial-migration"></a>Vytvoření počáteční migraci

Uložte změny a sestavte projekt. Pak otevřete okno příkazového řádku a přejděte do složky projektu. Tady je rychlý způsob, jak to udělat:

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a zvolte **otevřít složku v Průzkumníku souborů** v místní nabídce.

  ![Otevřít v Průzkumníku souborů položky nabídky](migrations/_static/open-in-file-explorer.png)

* Do panelu Adresa zadejte "cmd" a stiskněte klávesu Enter.

  ![Otevřete příkazové okno](migrations/_static/open-command-window.png)

V příkazovém okně zadejte následující příkaz:

```console
dotnet ef migrations add InitialCreate
```

Zobrazí se výstup podobný tomuto v příkazovém okně:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> Pokud se zobrazí chybová zpráva *žádná spustitelný soubor se nenašel odpovídající příkaz "dotnet-ef"*, naleznete v tématu [tento příspěvek na blogu](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) pomoc při řešení potíží.

Pokud se zobrazí chybová zpráva "*nelze získat přístup k souboru... ContosoUniversity.dll protože je používán jiným procesem.* ", vyhledejte službu IIS Express ikonu na hlavním panelu systému Windows a pravým tlačítkem myši a potom klikněte na tlačítko **ContosoUniversity > zastavení webu**.

## <a name="examine-up-and-down-methods"></a>Prozkoumejte nahoru a dolů metody

Při spouštění `migrations add` příkazu EF vygeneruje kód, který se vytvoří databáze od začátku. Tento kód je v *migrace* složku, v souboru s názvem  *\<časové razítko > _InitialCreate.cs*. `Up` Metodu `InitialCreate` třída vytvoří databázové tabulky, které odpovídají sady entit datového modelu a `Down` metoda odstraní, jak je znázorněno v následujícím příkladu.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Migrace volání `Up` metody k implementaci změn datových modelů pro migraci. Po zadání příkazu vrácení zpět aktualizace, volání migrace `Down` metody.

Tento kód je pro počáteční migraci, která byla vytvořena, když jste zadali `migrations add InitialCreate` příkazu. Název parametru migrace ("InitialCreate" v příkladu) se používá pro název souboru a může být cokoliv, co chcete. Doporučujeme vybrat slovo nebo slovní spojení, které shrnuje, co se provádí v migraci. Můžete například pojmenovat pozdější migraci "AddDepartmentTable".

Pokud jste vytvořili počáteční migraci databáze již existuje, vygeneruje kód pro vytvoření databáze, ale nemusí spustit, protože databáze již odpovídá datového modelu. Když nasadíte aplikaci do jiného prostředí, kde databáze neexistuje ale tento kód spustí k vytvoření databáze, proto je vhodné se nejdřív otestovat. To je důvod, proč jste změnili název databáze v připojovacím řetězci dříve – tak, aby migrace můžete vytvořit nový úplně od začátku.

## <a name="the-data-model-snapshot"></a>Snímek dat modelu

Migrace vytvoří *snímku* aktuální schéma databáze v *Migrations/SchoolContextModelSnapshot.cs*. Při přidání migrace EF Určuje, co se změnilo porovnáním datový model, který soubor snímku.

Při odstranění migrace, použijte [migrace ef dotnet odebrat](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) příkazu. `dotnet ef migrations remove` Odstraní migraci a zajistí, že je správně obnovit snímek.

Zobrazit [migrace EF Core v prostředí Team](/ef/core/managing-schemas/migrations/teams) Další informace o tom, jak použít soubor snímku.

## <a name="apply-the-migration"></a>Použití migrace

V příkazovém řádku zadejte následující příkaz k vytvoření databáze a tabulky v ní.

```console
dotnet ef database update
```

Výstup z příkazu je podobný `migrations add` příkazu, s tím rozdílem, že pro SQL příkazy, které nastavení databáze naleznete v protokolech. Většina protokolů jsou vynechány v následujícím ukázkovém výstupu. Pokud nechcete zobrazit tato úroveň podrobností ve zprávách protokolu, můžete změnit úroveň protokolu v *appsettings. Development.JSON* souboru. Další informace naleznete v tématu <xref:fundamentals/logging/index>.

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

Použití **Průzkumník objektů systému SQL Server** ke kontrole databáze, jako jste to udělali v prvním kurzu.  Můžete si všimnout, přidání \_ \_EFMigrationsHistory tabulku, která uchovává informace o migraci, které se použily k databázi. Zobrazení dat v této tabulce, zobrazí jeden řádek pro první migraci. (Poslední protokolu v předchozím příkladu výstupu rozhraní příkazového řádku se zobrazí, která vytváří tento řádek příkazu INSERT.)

Spuštění aplikace pro ověření, že všechno funguje stále stejná jako předtím.

![Studenti indexová stránka](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Porovnání rozhraní příkazového řádku a PMC

EF nástroje pro správu migrace je k dispozici z příkazů rozhraní příkazového řádku .NET Core nebo z rutin prostředí PowerShell v sadě Visual Studio **Konzola správce balíčků** okno (PMC). Tento kurz ukazuje, jak používat rozhraní příkazového řádku, ale pokud dáváte přednost, můžete použít konzolu PMC.

EF příkazů pro příkazy PMC jsou v [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) balíčku. Tento balíček je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), takže není nutné přidat odkaz na balíček, pokud vaše aplikace obsahuje odkaz na balíček pro `Microsoft.AspNetCore.App`.

**Důležité:** Tato akce není stejného balíčku, jako je instalace rozhraní příkazového řádku tak, že upravíte *.csproj* souboru. Název tohoto objektu končí `Tools`, na rozdíl od názvu balíčku rozhraní příkazového řádku, které končí na `Tools.DotNet`.

Další informace o příkazech rozhraní příkazového řádku najdete v tématu [rozhraní příkazového řádku .NET Core](/ef/core/miscellaneous/cli/dotnet).

Další informace o příkazech PMC najdete v tématu [Konzola správce balíčků (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Další krok

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Dozvěděli jste se o migraci
> * Dozvěděli jste se o migraci balíčků NuGet
> * Změnit připojovací řetězec
> * Vytvoří počáteční migraci
> * Prozkoumat nahoru a dolů metody
> * Dozvěděli jste se o snímek dat modelu
> * Použít na migraci

Přejděte k dalšímu kurzu, kde hledat na pokročilejší témata o rozšiřování datového modelu. Na cestě můžete vytvářet a použít další migrace.

> [!div class="nextstepaction"]
> [Vytvořit a použít další migrace](complex-data-model.md)
