---
title: Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8
author: rick-anderson
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c4d43f26ba80e7922c3cbd37d9a5f8e1561b11ad
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656910"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Stránky Razor s EF Core v ASP.NET Core - souběžnosti - 8 8

[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra)a [Jan P Smith](https://twitter.com/thereformedprog)

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

Předtím, než Jana klikne na **Uložit**, Jan navštíví stejnou stránku a změní pole počáteční datum z 9/1/2007 na 9/1/2013.

![Změna počátečního data a 2013](concurrency/_static/change-date30.png)

Jana klikne na **Uložit** jako první a projeví se jeho změna, protože prohlížeč zobrazí stránku indexu s nulovou hodnotou rozpočtu.

Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00. Co se stane dále, podle toho, jak zpracovávat konflikty souběžnosti:

* Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.

  Ve scénáři bude ztracena žádná data. Různé vlastnosti byly aktualizovány dva uživatelé. Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny. Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě. Tento přístup má některé nevýhody:
 
  * Nelze nedošlo ke ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost.
  * Není obecně praktické ve webové aplikaci. Vyžaduje udržování významné stavu, aby bylo možné udržovat přehled o všech načtených hodnoty a nové hodnoty. Zachování velké množství stavu může ovlivnit výkon aplikace.
  * Můžete zvýšit složitost aplikace ve srovnání s detekce souběžnosti s entitou.

* Můžete nechat John's na změnu Jana změna přepsána.

  Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a počet získaných $350,000.00 hodnotu. Tento přístup se nazývá *klient WINS* nebo *Poslední ve scénáři služby WINS* . (Všechny hodnoty z klienta mají přednost před tím, co je v úložišti dat.) Pokud neprovedete žádné kódování pro zpracování souběžnosti, dojde k automatickému provedení služby WINS klienta.

* Můžete zabránit tomu, aby se změnila aktualizace od Jan v databázi. Obvykle by aplikace:

  * Zobrazí chybovou zprávu.
  * Zobrazit aktuální stav dat.
  * Povolit uživateli, který chcete znovu použít změny.

  To se označuje jako scénář *služby WINS pro Store* . (Hodnoty úložiště dat mají přednost před hodnotami odeslanými klientem.) Scénář služby WINS pro Store implementujete v tomto kurzu. Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění.

## <a name="conflict-detection-in-ef-core"></a>Zjišťování konfliktů v EF Core

EF Core vyvolá výjimky `DbConcurrencyException`, když detekuje konflikty. Aby bylo možné povolit detekci konfliktů, musí být datový model nakonfigurovaný. Mezi možnosti povolení detekce konfliktů patří následující:

* Nakonfigurujte EF Core pro zahrnutí původních hodnot sloupců nakonfigurovaných jako [tokeny souběžnosti](/ef/core/modeling/concurrency) v klauzuli WHERE příkazů Update a DELETE.

  Když je volána `SaveChanges`, klauzule WHERE hledá původní hodnoty všech vlastností pokomentovaných s atributem [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) . Příkaz Update nenajde řádek, který se má aktualizovat, pokud se některá z vlastností tokenu souběžnosti změnila od prvního načtení řádku. EF Core interpretuje, že jako konflikt souběžnosti. U databázových tabulek, které mají mnoho sloupců, může tento přístup mít za následek velmi velké klauzule WHERE a může vyžadovat velké množství stavu. Proto se tento přístup obecně nedoporučuje a nejedná se o metodu používanou v tomto kurzu.

* V tabulce databáze zahrňte sloupec sledování, který se dá použít k určení, kdy došlo ke změně řádku.

  V databázi SQL Server je datový typ sloupce sledování `rowversion`. Hodnota `rowversion` je sekvenční číslo, které se zvýší pokaždé, když se řádek aktualizuje. V příkazu Update nebo DELETE zahrnuje klauzule WHERE původní hodnotu sloupce sledování (číslo verze původního řádku). Pokud byl aktualizovaný řádek změněn jiným uživatelem, hodnota ve sloupci `rowversion` se liší od původní hodnoty. V takovém případě příkaz Update nebo DELETE nemůže najít řádek, který se má aktualizovat z klauzule WHERE. EF Core vyvolá výjimku souběžnosti, pokud nejsou žádné řádky ovlivněny příkazem Update nebo DELETE.

## <a name="add-a-tracking-property"></a>Přidat vlastnost sledování

V *modelu/oddělení. cs*přidejte vlastnost sledování s názvem rowversion:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

Atribut [Timestamp (časové razítko](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) ) identifikuje sloupec jako sloupec sledování souběžnosti. Rozhraní Fluent API je alternativní způsob, jak určit vlastnost sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

U databáze SQL Server je atribut `[Timestamp]` vlastnosti entity definovaný jako bajtové pole:

* Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.
* Nastaví typ sloupce v databázi na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

Databáze generuje číslo verze sekvenčního řádku, které se zvýší pokaždé, když se řádek aktualizuje. V příkazu `Update` nebo `Delete` zahrnuje klauzule `Where` hodnotu načtené verze řádku. Pokud se aktualizovaný řádek od načtení změnil:

* Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.
* Příkazy `Update` nebo `Delete` nenaleznou řádek, protože klauzule `Where` vyhledává hodnotu načtené verze řádku.
* Je vyvolána `DbUpdateConcurrencyException`.

Následující kód ukazuje část generovaných EF Core, když se aktualizuje název oddělení T-SQL:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód ukazuje klauzuli `WHERE` obsahující `RowVersion`. Pokud se `RowVersion` databáze neshoduje s parametrem `RowVersion` (`@p2`), neaktualizují se žádné řádky.

Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) vrátí počet řádků ovlivněných posledním příkazem. Pokud nejsou aktualizovány žádné řádky, EF Core vyvolá `DbUpdateConcurrencyException`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pro databázi SQLite je atribut `[Timestamp]` u vlastnosti entity definovaný jako bajtové pole:

* Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.
* Provede mapování na typ sloupce objektu BLOB.

Když se aktualizuje řádek, triggery databáze aktualizují sloupec RowVersion pomocí nového náhodného bajtového pole. V příkazu `Update` nebo `Delete` klauzule `Where` zahrnuje načtenou hodnotu sloupce RowVersion. Pokud se aktualizovaný řádek od načtení změnil:

* Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.
* Příkaz `Update` nebo `Delete` nenajde řádek, protože klauzule `Where` vyhledává hodnotu původní verze řádku.
* Je vyvolána `DbUpdateConcurrencyException`.

---

### <a name="update-the-database"></a>Aktualizace databáze

Přidání vlastnosti `RowVersion` změní datový model, který vyžaduje migraci.

Sestavte projekt. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V PMC spusťte následující příkaz:

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

* Vytvoří migrační soubor *_RowVersion. cs migrace/{časového razítka}* .
* Aktualizuje soubor *migrations/SchoolContextModelSnapshot. cs* . Tato aktualizace přidá do metody `BuildModel` následující zvýrazněný kód:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V PMC spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete soubor `Migrations/<timestamp>_RowVersion.cs` a přidejte zvýrazněný kód:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Předchozí kód:

  * Aktualizuje existující řádky s náhodnými hodnotami objektů BLOB.
  * Pokaždé, když se aktualizuje řádek, přidá aktivační procedury databáze, které nastaví sloupec RowVersion na náhodný hodnotu BLOB.

* V terminálu spusťte následující příkaz:

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Stránky oddělení uživatelského rozhraní

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Postupujte podle pokynů v části [stránky pro studenty](xref:data/ef-rp/intro#scaffold-student-pages) s těmito výjimkami:

* Vytvořte složku *stránky nebo oddělení* .  
* Pro třídu modelu použijte `Department`.
  * Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Vytvořte složku *stránky nebo oddělení* .

* Spusťte následující příkaz pro generování uživatelského rozhraní stránek oddělení.

  **Ve Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **V systému Linux nebo macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Sestavte projekt.

## <a name="update-the-index-page"></a>Aktualizace stránky indexu

Nástroj pro generování uživatelského rozhraní vytvořil pro stránku indexu `RowVersion` sloupec, ale toto pole by se v produkční aplikaci nezobrazovalo. V tomto kurzu se zobrazí poslední bajt `RowVersion`, který vám ukáže, jak funguje zpracování souběžnosti. Poslední bajt není zaručený jako jedinečný.

Aktualizovat stránku *Pages\Departments\Index.cshtml* :

* Nahraďte indexem oddělení.
* Změňte kód obsahující `RowVersion` pro zobrazení pouze posledního bajtu bajtového pole.
* Nahraďte FirstMidName jméno a příjmení.

Následující kód ukazuje aktualizovanou stránku:

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Aktualizace modelu stránky úpravy

*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

[Původní](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) se aktualizuje s hodnotou `rowVersion` z entity, když byla načtena v metodě `OnGet`. EF Core generuje příkaz SQL UPDATE s klauzulí WHERE obsahující původní hodnotu `RowVersion`. Pokud nejsou žádné řádky ovlivněny příkazem UPDATE (žádné řádky nemají původní hodnotu `RowVersion`), je vyvolána výjimka `DbUpdateConcurrencyException`.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

V předchozím zvýrazněném kódu:

* Hodnota v `Department.RowVersion` je to, co bylo v entitě v okamžiku, kdy byla původně načtena v žádosti o získání stránky pro úpravy. Hodnota je poskytnuta metodě `OnPost` pomocí skrytého pole na stránce Razor, které zobrazuje entitu, kterou chcete upravit. Hodnota skrytého pole je zkopírována do `Department.RowVersion` pomocí pořadače modelu.
* `OriginalValue` je to, co EF Core bude používat v klauzuli WHERE. Před spuštěním zvýrazněného řádku kódu `OriginalValue` má hodnotu, která byla v databázi, když `FirstOrDefaultAsync` byla volána v této metodě, což se může lišit od toho, co bylo zobrazeno na stránce pro úpravy.
* Zvýrazněný kód zajišťuje, aby EF Core používal původní hodnotu `RowVersion` ze zobrazené `Department` entity v klauzuli WHERE příkazu SQL UPDATE.

Když dojde k chybě souběžnosti, následující zvýrazněný kód Získá hodnoty klienta (hodnoty odeslané do této metody) a hodnoty databáze.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišně od odeslání do `OnPostAsync`:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze. Až uživatel příště klikne na možnost **Uložit**, bude zachycena pouze chyba souběžnosti, ke kterým dochází od posledního zobrazení stránky pro úpravy.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

Příkaz `ModelState.Remove` je vyžadován, protože `ModelState` má starou hodnotu `RowVersion`. Na stránce Razor má hodnota `ModelState` pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.

### <a name="update-the-razor-page"></a>Aktualizace stránky Razor

Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předchozí kód:

* Aktualizuje direktivu `page` z `@page` na `@page "{id:int}"`.
* Přidá verze skryté řádku. je nutné přidat `RowVersion`, aby postback propojí hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahradí `ViewData` silným typem `InstructorNameSL`.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testování je v konfliktu s stránky pro úpravu souběžnosti

Otevřete dvě instance prohlížeče úpravy na anglické oddělení:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem **na hypertextový** odkaz pro jazykové oddělení a vyberte **otevřít na nové kartě**.
* Na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.

Záložkách prohlížeče dvě zobrazení stejné informace.

Změňte název na první kartě prohlížeče a klikněte na **Uložit**.

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-130.png)

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Změňte jiné pole na druhé záložce prohlížeče.

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-230.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error30.png)

Toto okno prohlížeče neměli v úmyslu změnit název pole. Zkopírujte a vložte do pole název aktuální hodnotu (jazyky). Tabulátor. Ověřování na straně klienta odebere chybovou zprávu.

Znovu klikněte na **Uložit** . Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče. Zobrazí uložené hodnoty v indexovou stránku.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizujte *stránky/oddělení/odstraňte. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Na stránce odstranit rozpozná konfliktů souběžnosti, pokud entita změněna po načtení. `Department.RowVersion` je verze řádku v případě, že byla entita načtena. Když EF Core vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s `RowVersion`. Pokud vliv na výsledky příkazu SQL odstranit v nulový počet řádků:

* `RowVersion` v příkazu SQL DELETE neodpovídají `RowVersion` v databázi.
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync` se volá s `concurrencyError`.

### <a name="update-the-delete-razor-page"></a>Aktualizace stránky odstranit Razor

Aktualizovat *stránky/oddělení/odstranit. cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Předchozí kód provede následující změny:

* Aktualizuje direktivu `page` z `@page` na `@page "{id:int}"`.
* Přidá chybovou zprávu.
* Nahradí FirstMidName pomocí FullName v poli **správce** .
* Změní `RowVersion` k zobrazení posledního bajtu.
* Přidá verze skryté řádku. je nutné přidat `RowVersion`, aby postgit přidání zpětné vazby k hodnotě.

### <a name="test-concurrency-conflicts"></a>Konflikty testů v souběžnosti

Vytvořte test oddělení.

Otevřete dvě instance prohlížeče DELETE na oddělení testu:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro testovací oddělení a vyberte **otevřít na nové kartě**.
* Klikněte na odkaz **Upravit** pro testovací oddělení.

Záložkách prohlížeče dvě zobrazení stejné informace.

Změňte rozpočet na první kartě prohlížeče a klikněte na **Uložit**.

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze. Po kliknutí na **Odstranit** se entita odstraní, pokud se `RowVersion` neaktualizovala. oddělení se odstranilo.

## <a name="additional-resources"></a>Další zdroje

* [Tokeny souběžnosti v EF Core](/ef/core/modeling/concurrency)
* [Zpracování souběžnosti v EF Core](/ef/core/saving/concurrency)
* [Ladění zdrojového kódu ASP.NET Core 2. x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Další kroky

Toto je poslední kurz v řadě. Další témata jsou popsaná v tématu [verze MVC této série kurzů](xref:data/ef-mvc/index).

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz ukazuje, jak řešit konflikty při více uživateli aktualizovat entitu současně (ve stejnou dobu). Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

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

Předtím, než Jana klikne na **Uložit**, Jan navštíví stejnou stránku a změní pole počáteční datum z 9/1/2007 na 9/1/2013.

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

Jana klikne na **Uložit** a uvidí jeho změnu, když prohlížeč zobrazí stránku rejstřík.

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00. Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.

Optimistického řízení souběžnosti zahrnuje následující možnosti:

* Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi.

  Ve scénáři bude ztracena žádná data. Různé vlastnosti byly aktualizovány dva uživatelé. Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny. Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě. Tento přístup:
 
  * Nelze nedošlo ke ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost.
  * Není obecně praktické ve webové aplikaci. Vyžaduje udržování významné stavu, aby bylo možné udržovat přehled o všech načtených hodnoty a nové hodnoty. Zachování velké množství stavu může ovlivnit výkon aplikace.
  * Můžete zvýšit složitost aplikace ve srovnání s detekce souběžnosti s entitou.

* Můžete nechat John's na změnu Jana změna přepsána.

  Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a počet získaných $350,000.00 hodnotu. Tento přístup se nazývá *klient WINS* nebo *Poslední ve scénáři služby WINS* . (Všechny hodnoty z klienta mají přednost před tím, co je v úložišti dat.) Pokud neprovedete žádné kódování pro zpracování souběžnosti, dojde k automatickému provedení služby WINS klienta.

* John's na změnu může zabránit aktualizují v databázi. Obvykle by aplikace:

  * Zobrazí chybovou zprávu.
  * Zobrazit aktuální stav dat.
  * Povolit uživateli, který chcete znovu použít změny.

  To se označuje jako scénář *služby WINS pro Store* . (Hodnoty úložiště dat mají přednost před hodnotami odeslanými klientem.) Scénář služby WINS pro Store implementujete v tomto kurzu. Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění.

## <a name="handling-concurrency"></a>Ošetření souběžnosti 

Když je vlastnost konfigurovaná jako [Token souběžnosti](/ef/core/modeling/concurrency):

* EF Core ověřuje, že vlastnost byl změněn po načtení. K ověření dochází při volání [metody SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) .
* Pokud byla vlastnost po načtení změněna, je vyvolána výjimka [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) . 

DATABÁZE a datový model musí být nakonfigurovány tak, aby podporovaly aktivační `DbUpdateConcurrencyException`.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Zjišťování konfliktů souběžnosti u vlastnosti

Konflikty souběžnosti lze zjistit na úrovni vlastnosti pomocí atributu [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) . Atribut lze použít na více vlastností v modelu. Další informace najdete v tématu [data anotaces – ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

Atribut `[ConcurrencyCheck]` se v tomto kurzu nepoužívá.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Zjišťování konfliktů souběžnosti na řádek

K detekci konfliktů souběžnosti se do modelu přidá sloupec sledování [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) .  :`rowversion`

* SQL Server je konkrétní. Ostatní databáze neposkytují podobné funkce.
* Slouží k určení, že entita nebyl změněn od načtení z databáze. 

DATABÁZE generuje sekvenční `rowversion` číslo, které se zvýší při každé aktualizaci řádku. V příkazu `Update` nebo `Delete` zahrnuje klauzule `Where` hodnotu Fetch `rowversion`. Pokud došlo ke změně aktualizuje řádek:

* `rowversion` neodpovídá načtené hodnotě.
* Příkazy `Update` nebo `Delete` nenaleznou řádek, protože klauzule `Where` zahrnuje načtený `rowversion`.
* Je vyvolána `DbUpdateConcurrencyException`.

Pokud se v EF Core neaktualizovaly žádné řádky pomocí příkazu `Update` nebo `Delete`, je vyvolána výjimka souběžnosti.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Přidání vlastnosti sledování do entity oddělení

V *modelu/oddělení. cs*přidejte vlastnost sledování s názvem rowversion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Atribut [timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) určuje, že tento sloupec je zahrnutý v klauzuli `Where` `Update` a `Delete` příkazy. Atribut se nazývá `Timestamp`, protože předchozí verze SQL Server používaly datový typ `timestamp` SQL, než je tento typ `rowversion` SQL nahradil.

Rozhraní fluent API můžete také zadat vlastnosti sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Následující kód ukazuje část generovaných EF Core, když se aktualizuje název oddělení T-SQL:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód ukazuje klauzuli `WHERE` obsahující `RowVersion`. Pokud se `RowVersion` databáze neshoduje s parametrem `RowVersion` (`@p2`), žádné řádky se neaktualizují.

Následující zvýrazněný kód ukazuje T-SQL, která ověřuje, že byl aktualizován přesně jeden řádek:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) vrátí počet řádků ovlivněných posledním příkazem. V Neaktualizování řádků EF Core vyvolá `DbUpdateConcurrencyException`.

Uvidíte, že v okně výstupu sady Visual Studio generuje EF Core T-SQL.

### <a name="update-the-db"></a>Aktualizace databáze

Přidání vlastnosti `RowVersion` změní model databáze, který vyžaduje migraci.

Sestavte projekt. V příkazovém okně zadejte následující údaje:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Předchozí příkazy:

* Přidá migrační soubor *_RowVersion. cs migrace/{časového razítka}* .
* Aktualizuje soubor *migrations/SchoolContextModelSnapshot. cs* . Tato aktualizace přidá do metody `BuildModel` následující zvýrazněný kód:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Spuštění migrace k aktualizaci databáze.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Vygenerované uživatelské rozhraní modelu oddělení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-student-pages) a použijte `Department` pro třídu modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Předchozí příkaz vygeneruje model `Department`. Otevřete projekt v sadě Visual Studio.

Sestavte projekt.

### <a name="update-the-departments-index-page"></a>Aktualizace oddělení indexovou stránku

Modul pro generování uživatelského rozhraní vytvořil pro stránku indexu `RowVersion` sloupec, ale toto pole by se nemělo zobrazovat. V tomto kurzu se zobrazí poslední bajt `RowVersion`, který vám pomůže pochopit souběžnost. Poslední bajt nemusí být jedinečný. Skutečná aplikace by se nezobrazovala `RowVersion` nebo posledního bajtu `RowVersion`.

Aktualizace indexovou stránku:

* Nahraďte indexem oddělení.
* Nahraďte značky obsahující `RowVersion` posledním bajtem `RowVersion`.
* Nahraďte FirstMidName jméno a příjmení.

Následující kód ukazuje aktualizovanou stránku:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualizace modelu stránky úpravy

*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Aby se zjistil problém souběžnosti, [původní](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) se aktualizuje hodnotou `rowVersion` z entity, kterou načetla. EF Core generuje příkaz SQL UPDATE s klauzulí WHERE obsahující původní hodnotu `RowVersion`. Pokud nejsou žádné řádky ovlivněny příkazem UPDATE (žádné řádky nemají původní hodnotu `RowVersion`), je vyvolána výjimka `DbUpdateConcurrencyException`.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

V předchozím kódu `Department.RowVersion` je hodnota, když byla entita načtena. `OriginalValue` je hodnota v DB, pokud `FirstOrDefaultAsync` byla volána v této metodě.

Následující kód načte hodnoty klienta (hodnoty, publikuje se do této metody) a hodnoty DB:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync`:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze. Až uživatel příště klikne na možnost **Uložit**, bude zachycena pouze chyba souběžnosti, ke kterým dochází od posledního zobrazení stránky pro úpravy.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Příkaz `ModelState.Remove` je vyžadován, protože `ModelState` má starou hodnotu `RowVersion`. Na stránce Razor má hodnota `ModelState` pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravu

Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předchozí kód:

* Aktualizuje direktivu `page` z `@page` na `@page "{id:int}"`.
* Přidá verze skryté řádku. je nutné přidat `RowVersion`, aby postback propojí hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahradí `ViewData` silným typem `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testování je v konfliktu s stránky pro úpravu souběžnosti

Otevřete dvě instance prohlížeče úpravy na anglické oddělení:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem **na hypertextový** odkaz pro jazykové oddělení a vyberte **otevřít na nové kartě**.
* Na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.

Záložkách prohlížeče dvě zobrazení stejné informace.

Změňte název na první kartě prohlížeče a klikněte na **Uložit**.

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Změňte jiné pole na druhé záložce prohlížeče.

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která se neshodují s hodnotami DB:

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

Toto okno prohlížeče neměli v úmyslu změnit název pole. Zkopírujte a vložte do pole název aktuální hodnotu (jazyky). Tabulátor. Ověřování na straně klienta odebere chybovou zprávu.

![Oddělení upravit stránku chybová zpráva](concurrency/_static/cv.png)

Znovu klikněte na **Uložit** . Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče. Zobrazí uložené hodnoty v indexovou stránku.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizace modelu odstranění stránky s následujícím kódem:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Na stránce odstranit rozpozná konfliktů souběžnosti, pokud entita změněna po načtení. `Department.RowVersion` je verze řádku v případě, že byla entita načtena. Když EF Core vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s `RowVersion`. Pokud vliv na výsledky příkazu SQL odstranit v nulový počet řádků:

* `RowVersion` v příkazu SQL DELETE neodpovídají `RowVersion` v databázi.
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync` se volá s `concurrencyError`.

### <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Aktualizovat *stránky/oddělení/odstranit. cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Předchozí kód provede následující změny:

* Aktualizuje direktivu `page` z `@page` na `@page "{id:int}"`.
* Přidá chybovou zprávu.
* Nahradí FirstMidName pomocí FullName v poli **správce** .
* Změní `RowVersion` k zobrazení posledního bajtu.
* Přidá verze skryté řádku. je nutné přidat `RowVersion`, aby postback propojí hodnotu.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Konflikty souběžnosti testu se stránkou Delete

Vytvořte test oddělení.

Otevřete dvě instance prohlížeče DELETE na oddělení testu:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro testovací oddělení a vyberte **otevřít na nové kartě**.
* Klikněte na odkaz **Upravit** pro testovací oddělení.

Záložkách prohlížeče dvě zobrazení stejné informace.

Změňte rozpočet na první kartě prohlížeče a klikněte na **Uložit**.

Prohlížeč zobrazí indexovou stránku s změněné hodnoty a aktualizované rowVersion indikátoru. Všimněte si aktualizovanou rowVersion ukazatel, se zobrazí na druhý zpětného odeslání na druhé záložce.

Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze. Po kliknutí na **Odstranit** se entita odstraní, pokud se `RowVersion` neaktualizovala. oddělení se odstranilo.

Přečtěte si téma [dědičnosti](xref:data/ef-mvc/inheritance) způsobu dědění datového modelu.

### <a name="additional-resources"></a>Další zdroje

* [Tokeny souběžnosti v EF Core](/ef/core/modeling/concurrency)
* [Zpracování souběžnosti v EF Core](/ef/core/saving/concurrency)
* [Verze tohoto kurzu pro YouTube (zpracování konfliktů souběžnosti)](https://youtu.be/EosxHTFgYps)
* [Verze tohoto kurzu pro YouTube (část 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Verze tohoto kurzu pro YouTube (část 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/update-related-data)

::: moniker-end

