---
title: Razor Stránky s EF core v ASP.NET Core - Souběžnost - 8 z 8
author: rick-anderson
description: Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat stejnou entitu ve stejnou dobu.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c4d43f26ba80e7922c3cbd37d9a5f8e1561b11ad
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656910"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Razor Stránky s EF core v ASP.NET Core - Souběžnost - 8 z 8

[Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), a [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat entitu současně (ve stejnou dobu).

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dochází, když:

* Uživatel přejde na stránku úprav entity.
* Jiný uživatel aktualizuje stejnou entitu před zápisem změny prvního uživatele do databáze.

Pokud není povoleno zjišťování souběžnosti, ten, kdo aktualizuje databázi naposledy přepíše změny jiného uživatele. Pokud je toto riziko přijatelné, náklady na programování souběžnosti mohou převážit nad přínosy.

### <a name="pessimistic-concurrency-locking"></a>Pesimistický souběžnost (zamykání)

Jedním ze způsobů, jak zabránit konfliktům souběžnosti, je použití uzamčení databáze. Tomu se říká pesimistické souběžnosti. Předtím, než aplikace přečte řádek databáze, který má v úmyslu aktualizovat, požádá o zámek. Jakmile je řádek uzamčen pro přístup k aktualizaci, žádný jiný uživatel nemá povoleno uzamknout řádek, dokud nebude vydán první zámek.

Správa zámků má nevýhody. Může být složitý program a může způsobit problémy s výkonem, protože počet uživatelů se zvyšuje. Entity Framework Core neposkytuje žádnou integrovanou podporu pro něj a tento kurz neukazuje, jak ji implementovat.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Optimistická souběžnost umožňuje konflikty souběžnosti a pak reaguje odpovídajícím způsobem, když to dělají. Jana například navštíví stránku úprav oddělení a změní rozpočet pro anglické oddělení z 350 000,00 USD na 0,00 USD.

![Změna rozpočtu na 0](concurrency/_static/change-budget30.png)

Před kliknutím na tlačítko **Uložit**jan navštíví stejnou stránku a změní pole Datum zahájení z 9.1.2007 na 1.

![Změna počátečního data na rok 2013](concurrency/_static/change-date30.png)

Jana klikne na **Uložit** jako první a její změna se projeví, protože prohlížeč zobrazí jako částku rozpočtu stránku Rejstřík s nulou.

Jan klikne na **Uložit** na stránce Úpravy, která stále zobrazuje rozpočet 350 000,00 USD. Co se stane dál, je určeno způsobem, jakým zpracováváte konflikty souběžnosti:

* Můžete sledovat, kterou vlastnost uživatel upravil, a aktualizovat pouze odpovídající sloupce v databázi.

  Ve scénáři by dojít ke ztrátě žádných dat. Oba uživatelé aktualizovali různé vlastnosti. Až příště někdo prohledá anglické oddělení, uvidí změny Jane i Johna. Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat. Tento přístup má některé nevýhody:
 
  * Nelze se vyhnout ztrátě dat, pokud jsou ve stejné vlastnosti provedeny konkurenční změny.
  * Obecně není praktické ve webové aplikaci. Vyžaduje udržování významného stavu, aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty. Udržování velkého množství stavu může ovlivnit výkon aplikace.
  * Může zvýšit složitost aplikace ve srovnání s detekcí souběžnosti na entitě.

* Můžeš nechat Johnovu změnu přepsat Janeinu změnu.

  Příště někdo prochází anglické oddělení, uvidí 9 / 1 / 2013 a přitažené za 350.000,00 dolarů hodnotu. Tento přístup se nazývá *klient wins* nebo poslední *ve wins* scénář. (Všechny hodnoty od klienta mají přednost před tím, co je v úložišti dat.) Pokud nechcete dělat žádné kódování pro zpracování souběžnosti, klient wins se stane automaticky.

* Můžete zabránit janově změně v aktualizaci v databázi. Aplikace by obvykle:

  * Zobrazí chybovou zprávu.
  * Zobrazí aktuální stav dat.
  * Povolte uživateli znovu použít změny.

  Tento scénář se nazývá scénář *Store Wins.* (Hodnoty úložiště dat mají přednost před hodnotami odeslané klientem.) Implementovat úložiště wins scénář v tomto kurzu. Tato metoda zajišťuje, že žádné změny jsou přepsány bez upozornění uživatele.

## <a name="conflict-detection-in-ef-core"></a>Zjišťování konfliktů v ef jádru

EF Core `DbConcurrencyException` vyvolá výjimky při zjišťování konfliktů. Datový model musí být nakonfigurován tak, aby povoloval zjišťování konfliktů. Možnosti povolení zjišťování konfliktů zahrnují následující:

* Nakonfigurujte EF Core tak, aby zahrnoval původní hodnoty sloupců nakonfigurovaných jako [tokeny souběžnosti](/ef/core/modeling/concurrency) v příkazech Where update and Delete.

  Když `SaveChanges` je volána, Where klauzule hledá původní hodnoty všechny vlastnosti s notated s [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) atribut. Příkaz update nenajde řádek aktualizovat, pokud některý z vlastností tokenu souběžnosti změnil od prvního čtení řádku. EF Core interpretuje, že jako konflikt souběžnosti. Pro databázové tabulky, které mají mnoho sloupců, tento přístup může mít za následek velmi velké Where klauzule a může vyžadovat velké množství stavu. Proto tento přístup se obecně nedoporučuje a není metoda použitá v tomto kurzu.

* V databázové tabulce zahrňte sledovací sloupec, který lze použít k určení, kdy byl řádek změněn.

  V databázi serveru SQL Server je `rowversion`datový typ sledovacího sloupce . Hodnota `rowversion` je pořadové číslo, které se při každé aktualizaci řádku zintáží. V příkazu Aktualizovat nebo Odstranit obsahuje klauzule Where původní hodnotu sledovacího sloupce (původní číslo verze řádku). Pokud byl řádek, který je aktualizován, změněn `rowversion` jiným uživatelem, hodnota ve sloupci se liší od původní hodnoty. V takovém případě update nebo Delete prohlášení nelze najít řádek aktualizovat z důvodu Where klauzule. EF Core vyvolá výjimku souběžnosti, když žádné řádky jsou ovlivněny Update nebo Delete příkaz.

## <a name="add-a-tracking-property"></a>Přidání vlastnosti sledování

V *souboru Models/Department.cs*přidejte vlastnost sledování s názvem RowVersion:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

Atribut [časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) je co identifikuje sloupec jako sloupec sledování souběžnosti. Fluent API je alternativní způsob, jak určit vlastnost sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pro databázi serveru `[Timestamp]` SQL Server atribut vlastnosti entity definované jako bajtové pole:

* Způsobí, že sloupec, které mají být zahrnuty do DELETE a UPDATE WHERE klauzule.
* Nastaví typ sloupce v databázi na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

Databáze generuje číslo verze sekvenční řádek, který se zintácí při každé aktualizaci řádku. V `Update` příkazu `Delete` nebo `Where` klauzule obsahuje načtenou hodnotu verze řádku. Pokud se aktualizovaný řádek od načtení změnil:

* Aktuální hodnota verze řádku neodpovídá načtené hodnotě.
* `Update` Příkazy `Delete` nebo nenajdou řádek, `Where` protože klauzule hledá hodnotu načtené verze řádku.
* A `DbUpdateConcurrencyException` je hozen.

Následující kód ukazuje část T-SQL generované EF Core při aktualizaci názvu oddělení:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód zobrazuje `WHERE` klauzuli `RowVersion`obsahující . Pokud se `RowVersion` databáze nerovná `RowVersion` parametru (`@p2`), nebudou aktualizovány žádné řádky.

Následující zvýrazněný kód ukazuje T-SQL, který ověřuje přesně jeden řádek byl aktualizován:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrátí počet řádků ovlivněných posledním příkazem. Pokud nejsou aktualizovány žádné řádky, `DbUpdateConcurrencyException`EF Core vyvolá .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pro databázi SQLite `[Timestamp]` atribut vlastnosti entity definované jako bajtové pole:

* Způsobí, že sloupec, které mají být zahrnuty do DELETE a UPDATE WHERE klauzule.
* Mapuje na typ sloupce BLOB.

Databáze aktivační události aktualizovat RowVersion sloupec s novým náhodným bajt pole při každé aktualizaci řádku. V `Update` příkazu `Delete` `Where` nebo klauzule obsahuje načtenou hodnotu rowversion sloupce. Pokud se aktualizovaný řádek od načtení změnil:

* Aktuální hodnota verze řádku neodpovídá načtené hodnotě.
* Příkaz `Update` `Delete` or řádek nenajde, protože `Where` klauzule hledá původní hodnotu verze řádku.
* A `DbUpdateConcurrencyException` je hozen.

---

### <a name="update-the-database"></a>Aktualizace databáze

Přidánívlastnosti `RowVersion` změní datový model, který vyžaduje migraci.

Sestavte projekt. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Spusťte následující příkaz v PMC:

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V terminálu spusťte následující příkaz:

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

Tento příkaz:

* Vytvoří migrační soubor *Migrace/{time stamp}_RowVersion.cs.*
* Aktualizuje soubor *Migrations/SchoolContextModelSnapshot.cs.* Aktualizace přidá do `BuildModel` metody následující zvýrazněný kód:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Spusťte následující příkaz v PMC:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete `Migrations/<timestamp>_RowVersion.cs` soubor a přidejte zvýrazněný kód:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Předcházející kód:

  * Aktualizuje existující řádky s náhodnými hodnotami objektů blob.
  * Přidá aktivační události databáze, které nastaví sloupec RowVersion na hodnotu náhodného objektu blob při každé aktualizaci řádku.

* V terminálu spusťte následující příkaz:

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Stránky oddělení lešení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Postupujte podle pokynů na [stránkách Student lešení](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:

* Vytvořte složku *Stránky/oddělení.*  
* Slouží `Department` pro třídu modelu.
  * Místo vytvoření nové třídy kontextu použijte existující třídu kontextu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Vytvořte složku *Stránky/oddělení.*

* Spusťte následující příkaz pro zakládání uživatelského lístku na stránkách oddělení.

  **Ve Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **Na Linuxu nebo macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Sestavte projekt.

## <a name="update-the-index-page"></a>Aktualizace stránky Rejstřík

Nástroj pro vytváření uživatelského `RowVersion` okna vytvořil sloupec pro stránku Index, ale toto pole by se v produkční aplikaci nezobrazovalo. V tomto kurzu `RowVersion` je zobrazen poslední bajt, který vám pomůže zobrazit, jak funguje zpracování souběžnosti. Poslední bajt není zaručeno, že bude jedinečný sám o sobě.

Aktualizovat *stránky\Departments\Index.cshtml:*

* Nahraďte index odděleními.
* Změňte kód `RowVersion` obsahující tak, aby zobrazoval pouze poslední bajt bajtového pole.
* Nahraďte FirstMidName fullname.

Následující kód zobrazuje aktualizovanou stránku:

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Aktualizace modelu stránky Úpravy

Aktualizovat *stránky\Departments\Edit.cshtml.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

[OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován `rowVersion` s hodnotou z entity, když `OnGet` byl načten v metodě. EF Core generuje příkaz SQL UPDATE s klauzulí `RowVersion` WHERE obsahující původní hodnotu. Pokud žádný řádek jsou ovlivněny příkazem UPDATE `RowVersion` (žádné `DbUpdateConcurrencyException` řádky mají původní hodnotu), je vyvolána výjimka.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

V předchozím zvýrazněném kódu:

* Hodnota v `Department.RowVersion` je to, co bylo v entitě, když byla původně načtena na stránce Získat požadavek na úpravy. Hodnota je poskytována `OnPost` metodě skrytým polem na stránce Razor, které zobrazuje entitu, která má být upravena. Hodnota skrytého pole je `Department.RowVersion` zkopírována pořadačem modelu.
* `OriginalValue`je to, co EF Core použije v klauzuli Kde. Před zvýrazněný řádek kódu spustí, má hodnotu, `OriginalValue` která `FirstOrDefaultAsync` byla v databázi, když byl volán v této metodě, které se mohou lišit od toho, co bylo zobrazeno na stránce Upravit.
* Zvýrazněný kód zajišťuje, že EF `RowVersion` Core používá původní `Department` hodnotu ze zobrazené entity v klauzuli Where příkazu SQL UPDATE.

Když dojde k chybě souběžnosti, následující zvýrazněný kód získá hodnoty klienta (hodnoty zaúčtované do této metody) a hodnoty databáze.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který `OnPostAsync`má hodnoty databáze odlišné od toho, co bylo zaúčtováno do :

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Následující zvýrazněný kód `RowVersion` nastaví hodnotu na novou hodnotu načtenou z databáze. Při příštím kliknutí uživatele na **uložit**budou zachyceny pouze chyby souběžnosti, ke kterým dojde od posledního zobrazení stránky Úpravy.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

Příkaz `ModelState.Remove` je povinný, `ModelState` protože `RowVersion` má starou hodnotu. Na stránce holicí strojek má `ModelState` hodnota pole přednost před hodnotami vlastností modelu, pokud jsou k dispozici obě.

### <a name="update-the-razor-page"></a>Aktualizace stránky Razor

Aktualizovat *stránky/oddělení/Edit.cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předcházející kód:

* Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`
* Přidá verzi skrytého řádku. `RowVersion`musí být přidán, takže post zpět váže hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahradí `ViewData` se silným typem `InstructorNameSL`.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testování konfliktů souběžnosti se stránkou Úpravy

Otevřete dvě instance prohlížeče Upravit na anglickém oddělení:

* Spusťte aplikaci a vyberte Oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Upravit** pro anglické oddělení a **na nové kartě**vyberte otevřít .
* Na první kartě klikněte na hypertextový odkaz **Upravit** pro anglické oddělení.

Na dvou kartách prohlížeče se zobrazují stejné informace.

Změňte název na první kartě prohlížeče a klepněte na **tlačítko Uložit**.

![Oddělení Upravit stránku 1 po změně](concurrency/_static/edit-after-change-130.png)

Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.

Změňte jiné pole na druhé kartě prohlížeče.

![Oddělení Upravit stránku 2 po změně](concurrency/_static/edit-after-change-230.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/edit-error30.png)

Toto okno prohlížeče nemělo v úmyslu změnit pole Název. Zkopírujte a vložte aktuální hodnotu (jazyky) do pole Název. Vyjděte si. Ověření na straně klienta odebere chybovou zprávu.

Klikněte znovu na **Uložit.** Hodnota zadaná na druhé kartě prohlížeče se uloží. Uložené hodnoty se zobrazí na stránce Rejstřík.

## <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

Aktualizujte *stránky/oddělení/Delete.cshtml.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Stránka Odstranit zjistí konflikty souběžnosti, když se entita po načtení změnila. `Department.RowVersion`je verze řádku při načítání entity. Když EF Core vytvoří příkaz SQL DELETE, `RowVersion`obsahuje klauzuli WHERE s . Pokud příkaz SQL DELETE má za následek nula řádků ovlivněny:

* Příkaz `RowVersion` DELETE v sql delete `RowVersion` se neshoduje v databázi.
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync`je volána `concurrencyError`s .

### <a name="update-the-delete-razor-page"></a>Aktualizace stránky Odstranit holicí strojek

Aktualizovat *stránky/oddělení/Delete.cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Předchozí kód provede následující změny:

* Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`
* Přidá chybovou zprávu.
* Nahradí firstmidname v poli **Správce** názvem FullName.
* Změní `RowVersion` zobrazení posledního bajtu.
* Přidá verzi skrytého řádku. `RowVersion`musí být přidán, takže postgit add back váže hodnotu.

### <a name="test-concurrency-conflicts"></a>Testovat konflikty souběžnosti

Vytvořte testovací oddělení.

Otevřete dvě instance prohlížeče Delete na testovacím oddělení:

* Spusťte aplikaci a vyberte Oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** testovacího oddělení a **na nové kartě**vyberte otevřít .
* Klikněte na hypertextový odkaz **Upravit** testovacího oddělení.

Na dvou kartách prohlížeče se zobrazují stejné informace.

Změňte rozpočet na první kartě prohlížeče a klepněte na tlačítko **Uložit**.

Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.

Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazí s aktuálními hodnotami z databáze. Kliknutím na **Odstranit** odstraníte `RowVersion` entitu, pokud nebyla aktualizována.oddělení bylo odstraněno.

## <a name="additional-resources"></a>Další zdroje

* [Tokeny souběžnosti v jádru EF](/ef/core/modeling/concurrency)
* [Zpracování souběžnosti v jádru EF](/ef/core/saving/concurrency)
* [Ladění ASP.NET zdroj Core 2.x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Další kroky

Toto je poslední výukový program v sérii. Další témata jsou popsána ve [verzi MVC této série kurzů](xref:data/ef-mvc/index).

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat entitu současně (ve stejnou dobu). Pokud narazíte na problémy, které nemůžete vyřešit, [stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Stáhnout pokyny](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dochází, když:

* Uživatel přejde na stránku úprav entity.
* Jiný uživatel aktualizuje stejnou entitu před první změna uživatele je zapsána do DB.

Pokud není povoleno zjišťování souběžnosti, pokud dojde k souběžným aktualizacím:

* Poslední aktualizace vyhrává. To znamená, že poslední hodnoty aktualizace jsou uloženy do DB.
* První z aktuálních aktualizací jsou ztraceny.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Optimistická souběžnost umožňuje konflikty souběžnosti a pak reaguje odpovídajícím způsobem, když to dělají. Jana například navštíví stránku úprav oddělení a změní rozpočet pro anglické oddělení z 350 000,00 USD na 0,00 USD.

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

Před kliknutím na tlačítko **Uložit**jan navštíví stejnou stránku a změní pole Datum zahájení z 9.1.2007 na 1.

![Změna počátečního data na rok 2013](concurrency/_static/change-date.png)

Jan klikne na **Uložit** jako první a uvidí její změnu, když prohlížeč zobrazí stránku Rejstřík.

![Rozpočet změněn na nulu](concurrency/_static/budget-zero.png)

Jan klikne na **Uložit** na stránce Úpravy, která stále zobrazuje rozpočet 350 000,00 USD. Co se stane dál, je určena tím, jak zpracovat konflikty souběžnosti.

Optimistická souběžnost zahrnuje následující možnosti:

* Můžete sledovat, kterou vlastnost uživatel upravil, a aktualizovat pouze odpovídající sloupce v databázi.

  Ve scénáři by dojít ke ztrátě žádných dat. Oba uživatelé aktualizovali různé vlastnosti. Až příště někdo prohledá anglické oddělení, uvidí změny Jane i Johna. Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat. Tento přístup:
 
  * Nelze se vyhnout ztrátě dat, pokud jsou ve stejné vlastnosti provedeny konkurenční změny.
  * Obecně není praktické ve webové aplikaci. Vyžaduje udržování významného stavu, aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty. Udržování velkého množství stavu může ovlivnit výkon aplikace.
  * Může zvýšit složitost aplikace ve srovnání s detekcí souběžnosti na entitě.

* Můžeš nechat Johnovu změnu přepsat Janeinu změnu.

  Příště někdo prochází anglické oddělení, uvidí 9 / 1 / 2013 a přitažené za 350.000,00 dolarů hodnotu. Tento přístup se nazývá *klient wins* nebo poslední *ve wins* scénář. (Všechny hodnoty od klienta mají přednost před tím, co je v úložišti dat.) Pokud nechcete dělat žádné kódování pro zpracování souběžnosti, klient wins se stane automaticky.

* Můžete zabránit Janově změně v aktualizaci v databázi. Aplikace by obvykle:

  * Zobrazí chybovou zprávu.
  * Zobrazí aktuální stav dat.
  * Povolte uživateli znovu použít změny.

  Tento scénář se nazývá scénář *Store Wins.* (Hodnoty úložiště dat mají přednost před hodnotami odeslané klientem.) Implementovat úložiště wins scénář v tomto kurzu. Tato metoda zajišťuje, že žádné změny jsou přepsány bez upozornění uživatele.

## <a name="handling-concurrency"></a>Zpracování souběžnosti 

Když je vlastnost nakonfigurována jako [token souběžnosti](/ef/core/modeling/concurrency):

* EF Core ověří, že vlastnost nebyla změněna po načtení. Ke kontrole dochází při [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána.
* Pokud vlastnost byla změněna po načtení, [dbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) je vyvolána. 

DB a datový model musí být `DbUpdateConcurrencyException`nakonfigurovántak, aby podporoval y házení .

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Zjišťování konfliktů souběžnosti ve vlastnosti

Konflikty souběžnosti lze zjistit na úrovni vlastnosti s atributem [ConcurrencyCheck.](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) Atribut lze použít na více vlastností v modelu. Další informace naleznete [v tématu Data Anotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

Atribut `[ConcurrencyCheck]` se v tomto kurzu nepoužívá.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Zjišťování konfliktů souběžnosti na řádku

Chcete-li zjistit konflikty souběžnosti, sloupec sledování [verze řádků](/sql/t-sql/data-types/rowversion-transact-sql) je přidán do modelu.  `rowversion` :

* Je sql server specifický. Jiné databáze nemusí poskytovat podobnou funkci.
* Používá se k určení, že entita nebyla změněna od doby, kdy byla načtena z DB. 

DB generuje pořadové `rowversion` číslo, které se při každé aktualizaci řádku zintátáží. V `Update` příkazu `Delete` nebo `Where` klauzule obsahuje načtenou hodnotu `rowversion`. Pokud se aktualizovaný řádek změnil:

* `rowversion`neodpovídá načtené hodnotě.
* `Update` Příkazy `Delete` nebo nenajdou řádek, `Where` protože klauzule `rowversion`obsahuje načtené .
* A `DbUpdateConcurrencyException` je hozen.

V EF Core, když žádné řádky `Update` `Delete` byly aktualizovány příkazem nebo, je vyvolána výjimka souběžnosti.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Přidání vlastnosti sledování do entity Oddělení

V *souboru Models/Department.cs*přidejte vlastnost sledování s názvem RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Atribut [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) určuje, že tento sloupec `Where` je `Update` `Delete` součástí klauzule a příkazy. Atribut je `Timestamp` volán, protože předchozí verze `timestamp` serveru SQL Server `rowversion` používaly datový typ SQL předtím, než jej nahradil typ SQL.

Fluent API můžete také určit vlastnost sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Následující kód ukazuje část T-SQL generované EF Core při aktualizaci názvu oddělení:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód zobrazuje `WHERE` klauzuli `RowVersion`obsahující . Pokud DB `RowVersion` nerovná `RowVersion` parametr (`@p2`), žádné řádky jsou aktualizovány.

Následující zvýrazněný kód ukazuje T-SQL, který ověřuje přesně jeden řádek byl aktualizován:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrátí počet řádků ovlivněných posledním příkazem. V žádné řádky jsou aktualizovány `DbUpdateConcurrencyException`EF Core vyvolá .

Můžete vidět T-SQL EF Core generuje ve výstupním okně Visual Studio.

### <a name="update-the-db"></a>Aktualizace databáze

Přidání `RowVersion` vlastnosti změní model DB, který vyžaduje migraci.

Sestavte projekt. Do příkazového okna zadejte následující:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Předchozí příkazy:

* Přidá migrační soubor *Migrace/{time stamp}_RowVersion.cs.*
* Aktualizuje soubor *Migrations/SchoolContextModelSnapshot.cs.* Aktualizace přidá do `BuildModel` metody následující zvýrazněný kód:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Spustí migrace k aktualizaci DATABÁZE.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Lešení oddělení model

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [lešení model studenta](xref:data/ef-rp/intro#scaffold-student-pages) a použít `Department` pro model třídy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Předchozí příkaz uchylovací `Department` příkaz y model. Otevřete projekt v sadě Visual Studio.

Sestavte projekt.

### <a name="update-the-departments-index-page"></a>Aktualizace stránky Index oddělení

Modul lešení vytvořil `RowVersion` sloupec pro stránku Index, ale toto pole by nemělo být zobrazeno. V tomto kurzu `RowVersion` je zobrazen poslední bajt, který pomáhá pochopit souběžnost. Poslední bajt není zaručeno, že je jedinečný. Skutečná aplikace by se `RowVersion` nezobrazila `RowVersion`ani se nezobrazila poslední bajt aplikace .

Aktualizujte stránku Rejstříku:

* Nahraďte index odděleními.
* Nahraďte značky `RowVersion` obsahující poslední bajt `RowVersion`.
* Nahraďte FirstMidName fullname.

Následující značky zobrazují aktualizovanou stránku:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualizace modelu stránky Úpravy

Aktualizovat *stránky\Departments\Edit.cshtml.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Chcete-li zjistit problém souběžnosti, [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován s hodnotou `rowVersion` z entity, která byla načtena. EF Core generuje příkaz SQL UPDATE s klauzulí `RowVersion` WHERE obsahující původní hodnotu. Pokud žádný řádek jsou ovlivněny příkazem UPDATE `RowVersion` (žádné `DbUpdateConcurrencyException` řádky mají původní hodnotu), je vyvolána výjimka.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

V předchozím kódu `Department.RowVersion` je hodnota při načtení entity. `OriginalValue`je hodnota v DB, když `FirstOrDefaultAsync` byl volán v této metodě.

Následující kód získá hodnoty klienta (hodnoty zaúčtované do této metody) a hodnoty DB:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který `OnPostAsync`má hodnoty DB odlišné od toho, co bylo zaúčtováno do :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Následující zvýrazněný kód `RowVersion` nastaví hodnotu na novou hodnotu načtenou z DB. Při příštím kliknutí uživatele na **uložit**budou zachyceny pouze chyby souběžnosti, ke kterým dojde od posledního zobrazení stránky Úpravy.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Příkaz `ModelState.Remove` je povinný, `ModelState` protože `RowVersion` má starou hodnotu. Na stránce holicí strojek má `ModelState` hodnota pole přednost před hodnotami vlastností modelu, pokud jsou k dispozici obě.

## <a name="update-the-edit-page"></a>Aktualizace stránky Úpravy

Aktualizujte *stránky/oddělení/Edit.cshtml* pomocí následujících značek:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předchozí značka:

* Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`
* Přidá verzi skrytého řádku. `RowVersion`musí být přidán, takže post zpět váže hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahradí `ViewData` se silným typem `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testování konfliktů souběžnosti se stránkou Úpravy

Otevřete dvě instance prohlížeče Upravit na anglickém oddělení:

* Spusťte aplikaci a vyberte Oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Upravit** pro anglické oddělení a **na nové kartě**vyberte otevřít .
* Na první kartě klikněte na hypertextový odkaz **Upravit** pro anglické oddělení.

Na dvou kartách prohlížeče se zobrazují stejné informace.

Změňte název na první kartě prohlížeče a klepněte na **tlačítko Uložit**.

![Oddělení Upravit stránku 1 po změně](concurrency/_static/edit-after-change-1.png)

Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.

Změňte jiné pole na druhé kartě prohlížeče.

![Oddělení Upravit stránku 2 po změně](concurrency/_static/edit-after-change-2.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám DB:

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/edit-error.png)

Toto okno prohlížeče nemělo v úmyslu změnit pole Název. Zkopírujte a vložte aktuální hodnotu (jazyky) do pole Název. Vyjděte si. Ověření na straně klienta odebere chybovou zprávu.

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/cv.png)

Klikněte znovu na **Uložit.** Hodnota zadaná na druhé kartě prohlížeče se uloží. Uložené hodnoty se zobrazí na stránce Rejstřík.

## <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

Aktualizujte model stránky Odstranit pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Stránka Odstranit zjistí konflikty souběžnosti, když se entita po načtení změnila. `Department.RowVersion`je verze řádku při načítání entity. Když EF Core vytvoří příkaz SQL DELETE, `RowVersion`obsahuje klauzuli WHERE s . Pokud příkaz SQL DELETE má za následek nula řádků ovlivněny:

* Příkaz `RowVersion` DELETE v příkazu SQL `RowVersion` DELETE se neshoduje v databázi.
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync`je volána `concurrencyError`s .

### <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

Aktualizovat *stránky/oddělení/Delete.cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Předchozí kód provede následující změny:

* Aktualizuje `page` direktivu z `@page` na . `@page "{id:int}"`
* Přidá chybovou zprávu.
* Nahradí firstmidname v poli **Správce** názvem FullName.
* Změní `RowVersion` zobrazení posledního bajtu.
* Přidá verzi skrytého řádku. `RowVersion`musí být přidán, takže post zpět váže hodnotu.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Testování konfliktů souběžnosti se stránkou Odstranit

Vytvořte testovací oddělení.

Otevřete dvě instance prohlížeče Delete na testovacím oddělení:

* Spusťte aplikaci a vyberte Oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** testovacího oddělení a **na nové kartě**vyberte otevřít .
* Klikněte na hypertextový odkaz **Upravit** testovacího oddělení.

Na dvou kartách prohlížeče se zobrazují stejné informace.

Změňte rozpočet na první kartě prohlížeče a klepněte na tlačítko **Uložit**.

Prohlížeč zobrazí stránku Index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazí na druhém postbacku na druhé kartě.

Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazí s aktuálními hodnotami z DB. Kliknutím na **Odstranit** odstraníte `RowVersion` entitu, pokud nebyla aktualizována.oddělení bylo odstraněno.

Viz [Dědičnost](xref:data/ef-mvc/inheritance) o tom, jak zdědit datový model.

### <a name="additional-resources"></a>Další zdroje

* [Tokeny souběžnosti v jádru EF](/ef/core/modeling/concurrency)
* [Zpracování souběžnosti v jádru EF](/ef/core/saving/concurrency)
* [Verze tohoto kurzu pro YouTube (Zpracování konfliktů souběžnosti)](https://youtu.be/EosxHTFgYps)
* [YouTube verze tohoto výukového programu (část 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [YouTube verze tohoto výukového programu (část 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/update-related-data)

::: moniker-end

