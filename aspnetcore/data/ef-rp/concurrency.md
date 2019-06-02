---
title: Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8
author: rick-anderson
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: 8430f8e720870a7b541655ea8bcfe2f67c942bb3
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458424"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra), a [Jan Macek P](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

Tento kurz ukazuje, jak řešit konflikty při více uživateli aktualizovat entitu současně (ve stejnou dobu). Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Dojde ke konfliktu souběžnosti při:

* Uživatel přejde na stránku upravit pro entitu.
* Jiný uživatel aktualizuje stejné entity před první uživatel změny jsou zapsána do databáze.

Pokud zjišťování souběžnosti není povolena, když dojde k souběžných aktualizací:

* Poslední aktualizace wins. To znamená poslední aktualizace hodnoty se uloží do databáze.
* První z aktuální aktualizace budou ztraceny.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Optimistického řízení souběžnosti umožňuje konfliktů souběžnosti, která se provede, a potom reaguje správně při dělají. Například Jana návštěv stránky pro úpravu oddělení a změny rozpočet anglické oddělení od $350,000.00 0,00 USD.

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

Jan klikne **Uložit** první a zobrazí ji změnit, pokud prohlížeč zobrazí indexovou stránku.

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $. Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.

Optimistického řízení souběžnosti zahrnuje následující možnosti:

* Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi.

  Ve scénáři bude ztracena žádná data. Různé vlastnosti byly aktualizovány dva uživatelé. Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny. Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě. Tento přístup:
 
  * Nelze nedošlo ke ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost.
  * Není obecně praktické ve webové aplikaci. Vyžaduje udržování významné stavu, aby bylo možné udržovat přehled o všech načtených hodnoty a nové hodnoty. Zachování velké množství stavu může ovlivnit výkon aplikace.
  * Můžete zvýšit složitost aplikace ve srovnání s detekce souběžnosti s entitou.

* Můžete nechat John's na změnu Jana změna přepsána.

  Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a počet získaných $350,000.00 hodnotu. Tento přístup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář. (Všechny hodnoty z klienta přednost co je v úložišti.) Pokud tak učiníte nemusíte vytvářet kód pro zpracování souběžnosti, Wins, klient probíhá automaticky.

* John's na změnu může zabránit aktualizují v databázi. Obvykle by aplikace:

  * Zobrazí chybovou zprávu.
  * Zobrazit aktuální stav dat.
  * Povolit uživateli, který chcete znovu použít změny.

  Tento postup se nazývá *Store Wins* scénář. (Hodnoty úložiště dat přednost hodnoty odeslány klientem.) Implementace služby Wins Store scénář v tomto kurzu. Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění.

## <a name="handling-concurrency"></a>Ošetření souběžnosti 

Když je vlastnost nakonfigurovaný jako [tokenem souběžnosti](/ef/core/modeling/concurrency):

* EF Core ověřuje, že vlastnost byl změněn po načtení. Kontrola dochází při [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána.
* Pokud vlastnost byl změněn po načtení, [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) je vyvolána výjimka. 

Databáze a datový model musí být nakonfigurované pro podporu vyvolání `DbUpdateConcurrencyException`.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Zjišťování konfliktů souběžnosti u vlastnosti

Konflikty souběžnosti lze zjistit pomocí na úrovni vlastnost [atribut ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) atribut. Atribut lze použít na více vlastností v modelu. Další informace najdete v tématu [anotací dat – atribut ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

`[ConcurrencyCheck]` Atribut není použit v tomto kurzu.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Zjišťování konfliktů souběžnosti na řádek

K detekci konfliktů souběžnosti [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) sledování sloupec se přidá do modelu.  `rowversion` :

* SQL Server je konkrétní. Ostatní databáze neposkytují podobné funkce.
* Slouží k určení, že entita nebyl změněn od načtení z databáze. 

Databáze generuje sekvenční `rowversion` aktualizovat číslo, které se zvýší pokaždé, když na řádek. V `Update` nebo `Delete` příkazu `Where` klauzule obsahuje hodnotu načtených `rowversion`. Pokud došlo ke změně aktualizuje řádek:

* `rowversion` neodpovídá hodnotě načtených.
* `Update` Nebo `Delete` příkazů nelze nalézt řádek, protože `Where` klauzule obsahuje načetly `rowversion`.
* A `DbUpdateConcurrencyException` je vyvolána výjimka.

V EF Core, když nebyly aktualizovány žádné řádky pomocí `Update` nebo `Delete` příkaz, je vyvolána výjimka souběžnosti.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Přidání vlastnosti sledování do entity oddělení

V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

[Časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) atribut určuje, že je součástí tohoto sloupce `Where` klauzuli `Update` a `Delete` příkazy. Atribut se nazývá `Timestamp` protože předchozích verzí SQL serveru použít SQL `timestamp` datového typu než SQL `rowversion` typ nahradili jsme ho.

Rozhraní fluent API můžete také zadat vlastnosti sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Následující kód ukazuje část generovaných EF Core, když se aktualizuje název oddělení T-SQL:

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód ukazuje `WHERE` obsahující klauzuli `RowVersion`. Pokud databáze `RowVersion` není roven `RowVersion` parametr (`@p2`), jsou aktualizovány žádné řádky.

Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrací počet řádků, které jsou ovlivněny poslední příkaz. V žádné řádky jsou aktualizovány, vyvolá EF Core `DbUpdateConcurrencyException`.

Uvidíte, že v okně výstupu sady Visual Studio generuje EF Core T-SQL.

### <a name="update-the-db"></a>Aktualizace databáze

Přidávání `RowVersion` změní vlastnost databáze modelu, který vyžaduje migraci.

Sestavte projekt. V příkazovém okně zadejte následující údaje:

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

Předchozí příkazy:

* Přidá *migrace / {čas stamp}_RowVersion.cs* souboru migrace.
* Aktualizace *Migrations/SchoolContextModelSnapshot.cs* souboru. Tato aktualizace přidává následující zvýrazněný kód do `BuildModel` metody:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* Spuštění migrace k aktualizaci databáze.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Vygenerované uživatelské rozhraní modelu oddělení

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Department` pro třídu modelu.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

 Spusťte následující příkaz:

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Předchozí příkaz scaffold `Department` modelu. Otevřete projekt v sadě Visual Studio.

Sestavte projekt.

### <a name="update-the-departments-index-page"></a>Aktualizace oddělení indexovou stránku

Generování uživatelského rozhraní engine vytvoření `RowVersion` by neměl být zobrazen sloupec pro indexovou stránku, ale toto pole. V tomto kurzu, poslední bajt `RowVersion` zobrazí se vám může pomoci souběžnosti. Poslední bajt nemusí být jedinečný. Skutečné aplikace nezobrazily `RowVersion` nebo posledního bajtu `RowVersion`.

Aktualizace indexovou stránku:

* Nahraďte indexem oddělení.
* Nahraďte kód obsahující `RowVersion` s poslední bajt `RowVersion`.
* Nahraďte FirstMidName jméno a příjmení.

Následující kód ukazuje aktualizovanou stránku:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualizace modelu stránky úpravy

Aktualizace *Pages\Departments\Edit.cshtml.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Ke zjištění problému souběžnosti, [původní hodnota](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován `rowVersion` hodnotu z entity se načetla. EF Core generuje příkazu SQL UPDATE s klauzulí WHERE, který obsahuje původní `RowVersion` hodnotu. Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), `DbUpdateConcurrencyException` je vyvolána výjimka.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

V předchozím kódu `Department.RowVersion` je hodnota, pokud se entita načetla. `OriginalValue` je hodnota v databázi při `FirstOrDefaultAsync` byla volána v této metodě.

Následující kód načte hodnoty klienta (hodnoty, publikuje se do této metody) a hodnoty DB:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má DB hodnoty liší od co byla publikována `OnPostAsync`:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Následující zvýrazněný kód nastaví `RowVersion` z databáze načíst hodnotu na novou hodnotu. Při příštím kliknutí na tlačítko **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože poslední zobrazení stránky pro úpravu bude zachycena.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu. Na stránce Razor `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravu

Aktualizace *Pages/Departments/Edit.cshtml* následujícím kódem:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předchozí kód:

* Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.
* Přidá verze skryté řádku. `RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahradí `ViewData` pomocí silných `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testování je v konfliktu s stránky pro úpravu souběžnosti

Otevřete dvě instance prohlížeče úpravy na anglické oddělení:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**.
* Na první kartě klikněte **upravit** hypertextového odkazu pro anglickou oddělení.

Záložkách prohlížeče dvě zobrazení stejné informace.

Změňte název na první záložce prohlížeče a klikněte na tlačítko **Uložit**.

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Změňte jiné pole na druhé záložce prohlížeče.

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která se neshodují s hodnotami DB:

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

Toto okno prohlížeče neměli v úmyslu změnit název pole. Zkopírujte a vložte do pole název aktuální hodnotu (jazyky). Karta navýšení kapacity. Ověřování na straně klienta odebere chybová zpráva.

![Oddělení upravit stránku chybová zpráva](concurrency/_static/cv.png)

Klikněte na tlačítko **Uložit** znovu. Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče. Zobrazí uložené hodnoty v indexovou stránku.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizace modelu odstranění stránky s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Na stránce odstranit rozpozná konfliktů souběžnosti, pokud entita změněna po načtení. `Department.RowVersion` verze řádku je, když se entita načetla. EF Core vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s `RowVersion`. Pokud vliv na výsledky příkazu SQL odstranit v nulový počet řádků:

* `RowVersion` v odstranit SQL příkaz neodpovídá `RowVersion` v databázi.
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync` volá se `concurrencyError`.

### <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizace *Pages/Departments/Delete.cshtml* následujícím kódem:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Předchozí kód provede následující změny:

* Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.
* Přidá chybovou zprávu.
* Nahradí celý název v FirstMidName **správce** pole.
* Změny `RowVersion` k zobrazení poslední bajt.
* Přidá verze skryté řádku. `RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Konflikty souběžnosti testu se stránkou Delete

Vytvořte test oddělení.

Otevřete dvě instance prohlížeče DELETE na oddělení testu:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši **odstranit** hypertextového odkazu pro oddělení test a vyberte **otevřít na nové kartě**.
* Klikněte na tlačítko **upravit** hypertextového odkazu pro oddělení testu.

Záložkách prohlížeče dvě zobrazení stejné informace.

Rozpočet na první záložce prohlížeče a klikněte na tlačítko **Uložit**.

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Odstraňte test oddělení na druhé kartě. Došlo k chybě souběžnosti je zobrazení pomocí aktuálních hodnot z databáze. Kliknutím na **odstranit** odstraní entitu, není-li `RowVersion` byl updated.department byl odstraněn.

Zobrazit [dědičnosti](xref:data/ef-mvc/inheritance) o tom, jak dědit datový model.

### <a name="additional-resources"></a>Další zdroje

* [Tokeny souběžnosti v EF Core](/ef/core/modeling/concurrency)
* [Popisovač souběžnosti v EF Core](/ef/core/saving/concurrency)
* [YouTube verzi tohoto kurzu (zpracování konfliktů souběžnosti)](https://youtu.be/EosxHTFgYps)
* [YouTube verzi tohoto kurzu (část 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [YouTube verzi tohoto kurzu (část 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/update-related-data)
