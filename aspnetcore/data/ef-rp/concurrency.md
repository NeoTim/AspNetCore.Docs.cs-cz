---
title: Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8
author: rick-anderson
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: 4d1e8ef2f55910fa5456171e45311feacff16919
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914894"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra), a [Jan Macek P](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Tento kurz ukazuje, jak řešit konflikty při více uživateli aktualizovat entitu současně (ve stejnou dobu).

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Dojde ke konfliktu souběžnosti při:

* Uživatel přejde na stránku upravit pro entitu.
* Jiný uživatel aktualizuje stejnou entitu před zapsáním změny prvního uživatele do databáze.

Pokud detekce souběžnosti není povolená, kdokoli aktualizuje databázi poslední přepsání změn provedených ostatními uživateli. Pokud je toto riziko přijatelné, náklady na programování pro souběžnost můžou převážit výhody.

### <a name="pessimistic-concurrency-locking"></a>Pesimistická souběžnost (uzamykání)

Jedním ze způsobů, jak zabránit konfliktům souběžnosti, je použití zámků databáze. Tato metoda se nazývá pesimistická souběžnost. Předtím, než aplikace přečte řádek databáze, který je v úmyslu aktualizovat, si vyžádá zámek. Když je řádek uzamčený pro přístup k aktualizacím, žádní jiní uživatelé nemůžou Uzamknout řádek, dokud se neuvolní první zámek.

Správa zámků má nevýhody. Může být komplexní pro program a může způsobit problémy s výkonem při zvýšení počtu uživatelů. Entity Framework Core neposkytuje žádnou integrovanou podporu pro IT a v tomto kurzu se nezobrazuje, jak ho implementovat.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Optimistického řízení souběžnosti umožňuje konfliktů souběžnosti, která se provede, a potom reaguje správně při dělají. Například Jana návštěv stránky pro úpravu oddělení a změny rozpočet anglické oddělení od $350,000.00 0,00 USD.

![Změna rozpočtu na 0](concurrency/_static/change-budget30.png)

Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.

![Změna počátečního data a 2013](concurrency/_static/change-date30.png)

Jana klikne na **Uložit** jako první a projeví se jeho změna, protože prohlížeč zobrazí stránku indexu s nulovou hodnotou rozpočtu.

Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $. Co se stane dále, podle toho, jak zpracovávat konflikty souběžnosti:

* Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.

  Ve scénáři bude ztracena žádná data. Různé vlastnosti byly aktualizovány dva uživatelé. Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny. Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě. Tento přístup má některé nevýhody:
 
  * Nelze nedošlo ke ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost.
  * Není obecně praktické ve webové aplikaci. Vyžaduje udržování významné stavu, aby bylo možné udržovat přehled o všech načtených hodnoty a nové hodnoty. Zachování velké množství stavu může ovlivnit výkon aplikace.
  * Můžete zvýšit složitost aplikace ve srovnání s detekce souběžnosti s entitou.

* Můžete nechat John's na změnu Jana změna přepsána.

  Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a počet získaných $350,000.00 hodnotu. Tento přístup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář. (Všechny hodnoty z klienta přednost co je v úložišti.) Pokud tak učiníte nemusíte vytvářet kód pro zpracování souběžnosti, Wins, klient probíhá automaticky.

* Můžete zabránit tomu, aby se změnila aktualizace od Jan v databázi. Obvykle by aplikace:

  * Zobrazí chybovou zprávu.
  * Zobrazit aktuální stav dat.
  * Povolit uživateli, který chcete znovu použít změny.

  Tento postup se nazývá *Store Wins* scénář. (Hodnoty úložiště dat přednost hodnoty odeslány klientem.) Implementace služby Wins Store scénář v tomto kurzu. Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění.

## <a name="conflict-detection-in-ef-core"></a>Zjišťování konfliktů v EF Core

EF Core vyvolá `DbConcurrencyException` výjimky, když detekuje konflikty. Aby bylo možné povolit detekci konfliktů, musí být datový model nakonfigurovaný. Mezi možnosti povolení detekce konfliktů patří následující:

* Nakonfigurujte EF Core pro zahrnutí původních hodnot sloupců nakonfigurovaných jako [tokeny souběžnosti](/ef/core/modeling/concurrency) v klauzuli WHERE příkazů Update a DELETE.

  Když `SaveChanges` je volána, klauzule WHERE hledá původní hodnoty všech vlastností pokomentovaných s atributem [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) . Příkaz Update nenajde řádek, který se má aktualizovat, pokud se některá z vlastností tokenu souběžnosti změnila od prvního načtení řádku. EF Core interpretuje, že jako konflikt souběžnosti. U databázových tabulek, které mají mnoho sloupců, může tento přístup mít za následek velmi velké klauzule WHERE a může vyžadovat velké množství stavu. Proto se tento přístup obecně nedoporučuje a nejedná se o metodu používanou v tomto kurzu.

* V tabulce databáze zahrňte sloupec sledování, který se dá použít k určení, kdy došlo ke změně řádku.

  V databázi SQL Server je `rowversion`datový typ sloupce sledování. `rowversion` Hodnota je sekvenční číslo, které se zvýší pokaždé, když se řádek aktualizuje. V příkazu Update nebo DELETE zahrnuje klauzule WHERE původní hodnotu sloupce sledování (číslo verze původního řádku). Pokud byl aktualizovaný řádek změněn jiným uživatelem, hodnota ve `rowversion` sloupci se liší od původní hodnoty. V takovém případě příkaz Update nebo DELETE nemůže najít řádek, který se má aktualizovat z klauzule WHERE. EF Core vyvolá výjimku souběžnosti, pokud nejsou žádné řádky ovlivněny příkazem Update nebo DELETE.

## <a name="add-a-tracking-property"></a>Přidat vlastnost sledování

V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

Atribut [Timestamp (časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) ) identifikuje sloupec jako sloupec sledování souběžnosti. Rozhraní Fluent API je alternativní způsob, jak určit vlastnost sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

U databáze `[Timestamp]` SQL Server je atribut vlastnosti entity definovaný jako bajtové pole:

* Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.
* Nastaví typ sloupce v databázi na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

Databáze generuje číslo verze sekvenčního řádku, které se zvýší pokaždé, když se řádek aktualizuje. `Delete` V příkazu `Update` nebozahrnujeklauzulehodnotunačtené`Where` verze řádku. Pokud se aktualizovaný řádek od načtení změnil:

* Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.
* Příkazy `Update` nebo `Delete` nenaleznouřádek,protožeklauzulevyhledáváhodnotunačtené`Where` verze řádku.
* A `DbUpdateConcurrencyException` je vyvolána výjimka.

Následující kód ukazuje část generovaných EF Core, když se aktualizuje název oddělení T-SQL:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód ukazuje `WHERE` obsahující klauzuli `RowVersion`. Pokud se databáze `RowVersion` neshoduje `RowVersion` s parametrem (`@p2`), žádné řádky se neaktualizují.

Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) vrací počet řádků, které jsou ovlivněny poslední příkaz. Pokud nejsou aktualizovány žádné řádky, EF Core vyvolá `DbUpdateConcurrencyException`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pro databázi SQLite je `[Timestamp]` atribut vlastnosti entity definovaný jako bajtové pole:

* Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.
* Provede mapování na typ sloupce objektu BLOB.

Když se aktualizuje řádek, triggery databáze aktualizují sloupec RowVersion pomocí nového náhodného bajtového pole. `Delete` V příkazu `Update` orzahrnujeklauzulenačtenou`Where` hodnotu sloupce rowversion. Pokud se aktualizovaný řádek od načtení změnil:

* Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.
* Příkaz `Update` nebo `Delete` nenalezneřádek,protožeklauzulevyhledápůvodní`Where` hodnotu verze řádku.
* A `DbUpdateConcurrencyException` je vyvolána výjimka.

---

### <a name="update-the-database"></a>Aktualizace databáze

`RowVersion` Přidáním vlastnosti se změní datový model, který vyžaduje migraci.

Sestavte projekt. 

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V PMC spusťte následující příkaz:

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* V terminálu spusťte následující příkaz:

  ```console
  dotnet ef migrations add RowVersion
  ```

---

Tento příkaz:

* Vytvoří migrační soubor *_RowVersion. cs migrace/{Time razítko}* .
* Aktualizace *Migrations/SchoolContextModelSnapshot.cs* souboru. Tato aktualizace přidává následující zvýrazněný kód do `BuildModel` metody:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V PMC spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* `Migrations/<timestamp>_RowVersion.cs` Otevřete soubor a přidejte zvýrazněný kód:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Předchozí kód:

  * Aktualizuje existující řádky s náhodnými hodnotami objektů BLOB.
  * Pokaždé, když se aktualizuje řádek, přidá aktivační procedury databáze, které nastaví sloupec RowVersion na náhodný hodnotu BLOB.

* V terminálu spusťte následující příkaz:

  ```console
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Stránky oddělení uživatelského rozhraní

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Postupujte podle pokynů v části [stránky](xref:data/ef-rp/intro#scaffold-student-pages) pro studenty s těmito výjimkami:

* Vytvořte složku *stránky nebo oddělení* .  
* Použijte `Department` pro třídu modelu.
  * Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Vytvořte složku *stránky nebo oddělení* .

* Spusťte následující příkaz pro generování uživatelského rozhraní stránek oddělení.

  **Ve Windows:**

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **V systému Linux nebo macOS:**

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Sestavte projekt.

## <a name="update-the-index-page"></a>Aktualizace stránky indexu

Nástroj pro generování uživatelského rozhraní vytvořil `RowVersion` sloupec pro stránku indexu, ale toto pole by se v produkční aplikaci nezobrazovalo. V tomto kurzu se zobrazí poslední bajt `RowVersion` , který vám ukáže, jak funguje zpracování souběžnosti. Poslední bajt není zaručený jako jedinečný.

Aktualizace indexovou stránku:

* Nahraďte indexem oddělení.
* Změňte kód obsahující `RowVersion` tak, aby zobrazoval pouze poslední bajt pole bajtů.
* Nahraďte FirstMidName jméno a příjmení.

Následující kód ukazuje aktualizovanou stránku:

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Aktualizace modelu stránky úpravy

*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

[Původní](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) se aktualizuje `rowVersion` hodnotou z entity, když byla `OnGet` načtena v metodě. EF Core generuje příkazu SQL UPDATE s klauzulí WHERE, který obsahuje původní `RowVersion` hodnotu. Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), `DbUpdateConcurrencyException` je vyvolána výjimka.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

V předchozím zvýrazněném kódu:

* Hodnota v `Department.RowVersion` poli je to, co bylo v entitě v okamžiku, kdy byla původně načtena v žádosti o získání stránky pro úpravy. Hodnota je poskytnuta `OnPost` metodě pomocí skrytého pole na stránce Razor, které zobrazuje entitu, kterou chcete upravit. Hodnota skrytého pole je zkopírována `Department.RowVersion` do pořadače modelu.
* `OriginalValue`je to, co EF Core bude používat v klauzuli WHERE. Předtím, než se spustí zvýrazněný řádek `OriginalValue` kódu, má hodnotu, která byla v `FirstOrDefaultAsync` databázi volána v této metodě, která se může lišit od toho, co bylo zobrazeno na stránce pro úpravy.
* Zvýrazněný kód zajišťuje, aby EF Core používal původní `RowVersion` hodnotu ze zobrazené `Department` entity v klauzuli WHERE příkazu SQL Update.

Když dojde k chybě souběžnosti, následující zvýrazněný kód Získá hodnoty klienta (hodnoty odeslané do této metody) a hodnoty databáze.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync`:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze. Při příštím kliknutí na tlačítko **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože poslední zobrazení stránky pro úpravu bude zachycena.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu. Na stránce Razor `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.

### <a name="update-the-razor-page"></a>Aktualizace stránky Razor

Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předchozí kód:

* Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.
* Přidá verze skryté řádku. `RowVersion` je nutné přidat tak příspěvek zpět váže hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahradí `ViewData` pomocí silných `InstructorNameSL`.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testování je v konfliktu s stránky pro úpravu souběžnosti

Otevřete dvě instance prohlížeče úpravy na anglické oddělení:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**.
* Na první kartě klikněte **upravit** hypertextového odkazu pro anglickou oddělení.

Záložkách prohlížeče dvě zobrazení stejné informace.

Změňte název na první záložce prohlížeče a klikněte na tlačítko **Uložit**.

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-130.png)

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Změňte jiné pole na druhé záložce prohlížeče.

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-230.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error30.png)

Toto okno prohlížeče neměli v úmyslu změnit název pole. Zkopírujte a vložte do pole název aktuální hodnotu (jazyky). Karta navýšení kapacity. Ověřování na straně klienta odebere chybová zpráva.

Klikněte na tlačítko **Uložit** znovu. Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče. Zobrazí uložené hodnoty v indexovou stránku.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizujte *stránky/oddělení/odstraňte. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Na stránce odstranit rozpozná konfliktů souběžnosti, pokud entita změněna po načtení. `Department.RowVersion` verze řádku je, když se entita načetla. EF Core vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s `RowVersion`. Pokud vliv na výsledky příkazu SQL odstranit v nulový počet řádků:

* Příkaz v příkazu SQL DELETE se v databázi neshoduje `RowVersion`. `RowVersion`
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync` volá se `concurrencyError`.

### <a name="update-the-delete-razor-page"></a>Aktualizace stránky odstranit Razor

Aktualizace *Pages/Departments/Delete.cshtml* následujícím kódem:

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Předchozí kód provede následující změny:

* Aktualizace `page` direktiv z `@page` k `@page "{id:int}"`.
* Přidá chybovou zprávu.
* Nahradí celý název v FirstMidName **správce** pole.
* Změny `RowVersion` k zobrazení poslední bajt.
* Přidá verze skryté řádku. `RowVersion`je nutné přidat, aby postgit přidat zpátky vazby hodnoty.

### <a name="test-concurrency-conflicts"></a>Konflikty testů v souběžnosti

Vytvořte test oddělení.

Otevřete dvě instance prohlížeče DELETE na oddělení testu:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši **odstranit** hypertextového odkazu pro oddělení test a vyberte **otevřít na nové kartě**.
* Klikněte na tlačítko **upravit** hypertextového odkazu pro oddělení testu.

Záložkách prohlížeče dvě zobrazení stejné informace.

Rozpočet na první záložce prohlížeče a klikněte na tlačítko **Uložit**.

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Odstraňte test oddělení na druhé kartě. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze. Kliknutím na **odstranit** odstraní entitu, není-li `RowVersion` byl updated.department byl odstraněn.

## <a name="additional-resources"></a>Další zdroje

* [Tokeny souběžnosti v EF Core](/ef/core/modeling/concurrency)
* [Popisovač souběžnosti v EF Core](/ef/core/saving/concurrency)
* [Ladění zdrojového kódu ASP.NET Core 2. x](https://github.com/aspnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Další postup

Toto je poslední kurz v řadě. Další témata jsou popsaná v tématu [verze MVC této série kurzů](xref:data/ef-mvc/index).

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód ukazuje `WHERE` obsahující klauzuli `RowVersion`. Pokud databáze `RowVersion` není roven `RowVersion` parametr (`@p2`), jsou aktualizovány žádné řádky.

Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

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

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Spuštění migrace k aktualizaci databáze.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Vygenerované uživatelské rozhraní modelu oddělení

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-student-pages) a použít `Department` pro třídu modelu.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

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

*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Ke zjištění problému souběžnosti, [původní hodnota](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) je aktualizován `rowVersion` hodnotu z entity se načetla. EF Core generuje příkazu SQL UPDATE s klauzulí WHERE, který obsahuje původní `RowVersion` hodnotu. Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), `DbUpdateConcurrencyException` je vyvolána výjimka.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

V předchozím kódu `Department.RowVersion` je hodnota, pokud se entita načetla. `OriginalValue` je hodnota v databázi při `FirstOrDefaultAsync` byla volána v této metodě.

Následující kód načte hodnoty klienta (hodnoty, publikuje se do této metody) a hodnoty DB:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync`:

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
* [Verze tohoto kurzu pro YouTube (zpracování konfliktů souběžnosti)](https://youtu.be/EosxHTFgYps)
* [Verze tohoto kurzu pro YouTube (část 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Verze tohoto kurzu pro YouTube (část 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/update-related-data)

::: moniker-end

