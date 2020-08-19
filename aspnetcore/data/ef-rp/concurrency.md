---
title: Část 8, Razor stránky s EF Core v ASP.NET Core-concurrency
author: rick-anderson
description: Část 8 Razor stránek a Entity Framework řady kurzů.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/concurrency
ms.openlocfilehash: e03711d970c83c2b7d6cc76039cb0d556a751018
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628908"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a>Část 8, Razor stránky s EF Core v ASP.NET Core-concurrency

[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra)a [Jan P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se dozvíte, jak řešit konflikty, když více uživatelů aktualizuje entitu souběžně (ve stejnou dobu).

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dojde v těchto případech:

* Uživatel přejde na stránku pro úpravy pro entitu.
* Jiný uživatel aktualizuje stejnou entitu před zapsáním změny prvního uživatele do databáze.

Pokud detekce souběžnosti není povolená, kdokoli aktualizuje databázi poslední přepsání změn provedených ostatními uživateli. Pokud je toto riziko přijatelné, náklady na programování pro souběžnost můžou převážit výhody.

### <a name="pessimistic-concurrency-locking"></a>Pesimistická souběžnost (uzamykání)

Jedním ze způsobů, jak zabránit konfliktům souběžnosti, je použití zámků databáze. Tato metoda se nazývá pesimistická souběžnost. Předtím, než aplikace přečte řádek databáze, který je v úmyslu aktualizovat, si vyžádá zámek. Když je řádek uzamčený pro přístup k aktualizacím, žádní jiní uživatelé nemůžou Uzamknout řádek, dokud se neuvolní první zámek.

Správa zámků má nevýhody. Může být komplexní pro program a může způsobit problémy s výkonem při zvýšení počtu uživatelů. Entity Framework Core neposkytuje žádnou integrovanou podporu pro IT a v tomto kurzu se nezobrazuje, jak ho implementovat.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Optimistická souběžnost umožňuje konfliktům souběžnosti a pak správné chování při jejich provádění. Jana například navštíví stránku pro úpravy oddělení a změní rozpočet pro anglické oddělení z $350 000,00 na $0,00.

![Změna rozpočtu na 0](concurrency/_static/change-budget30.png)

Předtím, než Jana klikne na **Uložit**, Jan navštíví stejnou stránku a změní pole počáteční datum z 9/1/2007 na 9/1/2013.

![Změna počátečního data na 2013](concurrency/_static/change-date30.png)

Jana klikne na **Uložit** jako první a projeví se jeho změna, protože prohlížeč zobrazí stránku indexu s nulovou hodnotou rozpočtu.

Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00. Co se stane dále, podle toho, jak zpracovávat konflikty souběžnosti:

* Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.

  V tomto scénáři by se neztratila žádná data. Dva uživatelé aktualizovali různé vlastnosti. Když někdo příště prochází v anglickém oddělení, uvidí změny Jana i Jan. Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat. Tento přístup má některé nevýhody:
 
  * Nelze zabránit ztrátě dat, pokud jsou provedeny konkurenční změny stejné vlastnosti.
  * Většinou není v rámci webové aplikace praktické. Aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty, je nutné zachovat značný stav. Udržování velkých objemů stavu může ovlivnit výkon aplikace.
  * Může zvýšit složitost aplikace v porovnání s detekcí souběžnosti u entity.

* Změnu můžete nechat v případě, že se změní Jan.

  Když někdo příště prochází v anglickém oddělení, uvidí 9/1/2013 a načtenou hodnotu $350 000,00. Tento přístup se nazývá *klient WINS* nebo *Poslední ve scénáři služby WINS* . (Všechny hodnoty z klienta mají přednost před tím, co je v úložišti dat.) Pokud neprovedete žádné kódování pro zpracování souběžnosti, dojde k automatickému provedení služby WINS klienta.

* Můžete zabránit tomu, aby se změnila aktualizace od Jan v databázi. Obvykle by aplikace měla:

  * Zobrazí chybovou zprávu.
  * Zobrazí aktuální stav dat.
  * Povolí uživateli znovu použít změny.

  To se označuje jako scénář *služby WINS pro Store* . (Hodnoty úložiště dat mají přednost před hodnotami odeslanými klientem.) Scénář služby WINS pro Store implementujete v tomto kurzu. Tato metoda zajistí, že nedojde k přepsání žádné změny bez upozornění uživatele.

## <a name="conflict-detection-in-ef-core"></a>Zjišťování konfliktů v EF Core

EF Core vyvolá `DbConcurrencyException` výjimky, když detekuje konflikty. Aby bylo možné povolit detekci konfliktů, musí být datový model nakonfigurovaný. Mezi možnosti povolení detekce konfliktů patří následující:

* Nakonfigurujte EF Core pro zahrnutí původních hodnot sloupců nakonfigurovaných jako [tokeny souběžnosti](/ef/core/modeling/concurrency) v klauzuli WHERE příkazů Update a DELETE.

  Když `SaveChanges` je volána, klauzule WHERE hledá původní hodnoty všech vlastností pokomentovaných s <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> atributem. Příkaz Update nenajde řádek, který se má aktualizovat, pokud se některá z vlastností tokenu souběžnosti změnila od prvního načtení řádku. EF Core interpretuje, že jako konflikt souběžnosti. U databázových tabulek, které mají mnoho sloupců, může tento přístup mít za následek velmi velké klauzule WHERE a může vyžadovat velké množství stavu. Proto se tento přístup obecně nedoporučuje a nejedná se o metodu používanou v tomto kurzu.

* V tabulce databáze zahrňte sloupec sledování, který se dá použít k určení, kdy došlo ke změně řádku.

  V databázi SQL Server je datový typ sloupce sledování `rowversion` . `rowversion`Hodnota je sekvenční číslo, které se zvýší pokaždé, když se řádek aktualizuje. V příkazu Update nebo DELETE zahrnuje klauzule WHERE původní hodnotu sloupce sledování (číslo verze původního řádku). Pokud byl aktualizovaný řádek změněn jiným uživatelem, hodnota ve `rowversion` sloupci se liší od původní hodnoty. V takovém případě příkaz Update nebo DELETE nemůže najít řádek, který se má aktualizovat z klauzule WHERE. EF Core vyvolá výjimku souběžnosti, pokud nejsou žádné řádky ovlivněny příkazem Update nebo DELETE.

## <a name="add-a-tracking-property"></a>Přidat vlastnost sledování

V *modelu/oddělení. cs*přidejte vlastnost sledování s názvem rowversion:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<xref:System.ComponentModel.DataAnnotations.TimestampAttribute>Atribut je to, co identifikuje sloupec jako sloupec sledování souběžnosti. Rozhraní Fluent API je alternativní způsob, jak určit vlastnost sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

U databáze SQL Server je `[Timestamp]` atribut vlastnosti entity definovaný jako bajtové pole:

* Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.
* Nastaví typ sloupce v databázi na [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

Databáze generuje číslo verze sekvenčního řádku, které se zvýší pokaždé, když se řádek aktualizuje. V `Update` příkazu nebo `Delete` `Where` zahrnuje klauzule hodnotu načtené verze řádku. Pokud se aktualizovaný řádek od načtení změnil:

* Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.
* `Update`Příkazy nebo `Delete` nenaleznou řádek, protože `Where` klauzule vyhledává hodnotu načtené verze řádku.
* `DbUpdateConcurrencyException`Je vyvolána výjimka.

Následující kód ukazuje část T-SQL vygenerované EF Core, když je název oddělení aktualizovaný:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód ukazuje `WHERE` klauzuli obsahující `RowVersion` . Pokud se databáze `RowVersion` neshoduje s `RowVersion` parametrem ( `@p2` ), žádné řádky se neaktualizují.

Následující zvýrazněný kód ukazuje T-SQL, který ověří právě jeden řádek, který byl aktualizován:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Vrátí počet řádků ovlivněných posledním příkazem. Pokud nejsou aktualizovány žádné řádky, EF Core vyvolá `DbUpdateConcurrencyException` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pro databázi SQLite je `[Timestamp]` atribut vlastnosti entity definovaný jako bajtové pole:

* Způsobí, že bude sloupec zahrnut v klauzulích DELETE a UPDATE WHERE.
* Provede mapování na typ sloupce objektu BLOB.

Když se aktualizuje řádek, triggery databáze aktualizují sloupec RowVersion pomocí nového náhodného bajtového pole. V `Update` příkazu OR `Delete` `Where` zahrnuje klauzule načtenou hodnotu sloupce rowversion. Pokud se aktualizovaný řádek od načtení změnil:

* Hodnota verze aktuálního řádku se neshoduje s načtenou hodnotou.
* `Update`Příkaz nebo `Delete` nenalezne řádek, protože `Where` klauzule vyhledá původní hodnotu verze řádku.
* `DbUpdateConcurrencyException`Je vyvolána výjimka.

---

### <a name="update-the-database"></a>Aktualizace databáze

Přidáním `RowVersion` vlastnosti se změní datový model, který vyžaduje migraci.

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
* Aktualizuje soubor *migrations/SchoolContextModelSnapshot. cs* . Aktualizace přidá do metody následující zvýrazněný kód `BuildModel` :

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V PMC spusťte následující příkaz:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete `Migrations/<timestamp>_RowVersion.cs` soubor a přidejte zvýrazněný kód:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Předcházející kód:

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
* Použijte `Department` pro třídu modelu.
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

Nástroj pro generování uživatelského rozhraní vytvořil `RowVersion` sloupec pro stránku indexu, ale toto pole by se v produkční aplikaci nezobrazovalo. V tomto kurzu se zobrazí poslední bajt, `RowVersion` který vám ukáže, jak funguje zpracování souběžnosti. Poslední bajt není zaručený jako jedinečný.

Aktualizovat stránku *Pages\Departments\Index.cshtml* :

* Nahraďte index "odděleními".
* Změňte kód obsahující `RowVersion` tak, aby zobrazoval pouze poslední bajt pole bajtů.
* Nahraďte FirstMidName řetězcem FullName.

Následující kód ukazuje aktualizovanou stránku:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Aktualizace modelu úprav stránek

*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue>Je aktualizována `rowVersion` hodnotou z entity, když byla načtena v `OnGet` metodě. EF Core generuje příkaz SQL UPDATE s klauzulí WHERE obsahující původní `RowVersion` hodnotu. Pokud nejsou žádné řádky ovlivněny příkazem UPDATE (žádné řádky nemají původní `RowVersion` hodnotu), `DbUpdateConcurrencyException` je vyvolána výjimka.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

V předchozím zvýrazněném kódu:

* Hodnota v `Department.RowVersion` poli je to, co bylo v entitě v okamžiku, kdy byla původně načtena v žádosti o získání stránky pro úpravy. Hodnota je poskytnuta `OnPost` metodě pomocí skrytého pole na Razor stránce, které zobrazuje entitu, kterou chcete upravit. Hodnota skrytého pole je zkopírována do `Department.RowVersion` pořadače modelu.
* `OriginalValue` je to, co EF Core bude používat v klauzuli WHERE. Předtím, než se spustí zvýrazněný řádek kódu, `OriginalValue` má hodnotu, která byla v databázi `FirstOrDefaultAsync` volána v této metodě, která se může lišit od toho, co bylo zobrazeno na stránce pro úpravy.
* Zvýrazněný kód zajišťuje, aby EF Core používal původní `RowVersion` hodnotu ze zobrazené `Department` entity v klauzuli WHERE příkazu SQL Update.

Když dojde k chybě souběžnosti, následující zvýrazněný kód Získá hodnoty klienta (hodnoty odeslané do této metody) a hodnoty databáze.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync` :

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze. Až uživatel příště klikne na možnost **Uložit**, bude zachycena pouze chyba souběžnosti, ke kterým dochází od posledního zobrazení stránky pro úpravy.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

`ModelState.Remove`Příkaz je vyžadován, protože `ModelState` má starou `RowVersion` hodnotu. Na Razor stránce `ModelState` má hodnota pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.

### <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravy

Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předcházející kód:

* Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .
* Přidá verzi skrytého řádku. `RowVersion` je nutné přidat, aby postback navázal hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahrazuje `ViewData` silným typem `InstructorNameSL` .

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Souběžnost testů v konfliktu se stránkou pro úpravy

V anglickém oddělení otevřete dvě instance pro úpravy v angličtině:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem **na hypertextový** odkaz pro jazykové oddělení a vyberte **otevřít na nové kartě**.
* Na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.

Dvě karty prohlížeče zobrazují stejné informace.

Změňte název na první kartě prohlížeče a klikněte na **Uložit**.

![Stránka pro úpravy v oddělení 1 po změně](concurrency/_static/edit-after-change-130.png)

Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.

Změňte jiné pole na druhé kartě prohlížeče.

![Stránka pro úpravy v oddělení 2 po změně](concurrency/_static/edit-after-change-230.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:

![Chybová zpráva stránky pro úpravu oddělení](concurrency/_static/edit-error30.png)

Toto okno prohlížeče nemá v úmyslu změnit pole název. Zkopírujte a vložte aktuální hodnotu (jazyky) do pole název. Tabulátor. Ověřování na straně klienta odebere chybovou zprávu.

Znovu klikněte na **Uložit** . Hodnota, kterou jste zadali na druhé záložce prohlížeče, se uloží. Uložené hodnoty se zobrazí na stránce index.

## <a name="update-the-delete-page-model"></a>Aktualizace odstranění modelu stránky

Aktualizujte *stránky/oddělení/odstraňte. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Stránka odstranit detekuje konflikty souběžnosti, když se entita po jejím načtení změnila. `Department.RowVersion` je verze řádku v případě, že byla entita načtena. Když EF Core vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s `RowVersion` . Pokud příkaz SQL DELETE má vliv na nulové řádky:

* `RowVersion`Příkaz v příkazu SQL DELETE se v databázi neshoduje `RowVersion` .
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync` je volána pomocí `concurrencyError` .

### <a name="update-the-delete-page"></a>Aktualizace stránky pro odstranění

Aktualizovat *stránky/oddělení/odstranit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

Předchozí kód provede následující změny:

* Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .
* Přidá chybovou zprávu.
* Nahradí FirstMidName pomocí FullName v poli **správce** .
* Změny `RowVersion` pro zobrazení posledního bajtu
* Přidá verzi skrytého řádku. `RowVersion` je nutné přidat, aby postback navázal hodnotu.

### <a name="test-concurrency-conflicts"></a>Konflikty testů v souběžnosti

Vytvořte testovací oddělení.

V testovacím středisku otevřete dvě instance pro odstraňování těchto prohlížečů:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro testovací oddělení a vyberte **otevřít na nové kartě**.
* Klikněte na odkaz **Upravit** pro testovací oddělení.

Dvě karty prohlížeče zobrazují stejné informace.

Změňte rozpočet na první kartě prohlížeče a klikněte na **Uložit**.

Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.

Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze. Kliknutím na **Odstranit** odstraníte entitu, pokud `RowVersion` se neaktualizovala.

## <a name="additional-resources"></a>Další zdroje informací

* [Tokeny souběžnosti v EF Core](/ef/core/modeling/concurrency)
* [Zpracování souběžnosti v EF Core](/ef/core/saving/concurrency)
* [Ladění zdrojového kódu ASP.NET Core 2. x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Další kroky

Toto je poslední kurz v řadě. Další témata jsou popsaná v tématu [verze MVC této série kurzů](xref:data/ef-mvc/index).

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se dozvíte, jak řešit konflikty, když více uživatelů aktualizuje entitu souběžně (ve stejnou dobu). Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Pokyny ke stažení](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dojde v těchto případech:

* Uživatel přejde na stránku pro úpravy pro entitu.
* Jiný uživatel aktualizuje stejnou entitu před zapsáním změny prvního uživatele do databáze.

Pokud detekce souběžnosti není povolená, když dojde k souběžným aktualizacím:

* Poslední aktualizace služby WINS. To znamená, že poslední hodnoty aktualizace se uloží do databáze.
* První z aktuálních aktualizací bude ztracena.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Optimistická souběžnost umožňuje konfliktům souběžnosti a pak správné chování při jejich provádění. Jana například navštíví stránku pro úpravy oddělení a změní rozpočet pro anglické oddělení z $350 000,00 na $0,00.

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

Předtím, než Jana klikne na **Uložit**, Jan navštíví stejnou stránku a změní pole počáteční datum z 9/1/2007 na 9/1/2013.

![Změna počátečního data na 2013](concurrency/_static/change-date.png)

Jana klikne na **Uložit** a uvidí jeho změnu, když prohlížeč zobrazí stránku rejstřík.

![Rozpočet se změnil na nula.](concurrency/_static/budget-zero.png)

Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00. Co se stane dál, určíte tak, jak můžete zpracovávat konflikty souběžnosti.

Optimistická souběžnost zahrnuje následující možnosti:

* Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.

  V tomto scénáři by se neztratila žádná data. Dva uživatelé aktualizovali různé vlastnosti. Když někdo příště prochází v anglickém oddělení, uvidí změny Jana i Jan. Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat. Tento přístup:
 
  * Nelze zabránit ztrátě dat, pokud jsou provedeny konkurenční změny stejné vlastnosti.
  * Většinou není v rámci webové aplikace praktické. Aby bylo možné sledovat všechny načtené hodnoty a nové hodnoty, je nutné zachovat značný stav. Udržování velkých objemů stavu může ovlivnit výkon aplikace.
  * Může zvýšit složitost aplikace v porovnání s detekcí souběžnosti u entity.

* Změnu můžete nechat v případě, že se změní Jan.

  Když někdo příště prochází v anglickém oddělení, uvidí 9/1/2013 a načtenou hodnotu $350 000,00. Tento přístup se nazývá *klient WINS* nebo *Poslední ve scénáři služby WINS* . (Všechny hodnoty z klienta mají přednost před tím, co je v úložišti dat.) Pokud neprovedete žádné kódování pro zpracování souběžnosti, dojde k automatickému provedení služby WINS klienta.

* Můžete zabránit tomu, aby se změny od Jan aktualizovaly v databázi. Obvykle by aplikace měla:

  * Zobrazí chybovou zprávu.
  * Zobrazí aktuální stav dat.
  * Povolí uživateli znovu použít změny.

  To se označuje jako scénář *služby WINS pro Store* . (Hodnoty úložiště dat mají přednost před hodnotami odeslanými klientem.) Scénář služby WINS pro Store implementujete v tomto kurzu. Tato metoda zajistí, že nedojde k přepsání žádné změny bez upozornění uživatele.

## <a name="handling-concurrency"></a>Zpracování souběžnosti 

Když je vlastnost konfigurovaná jako [Token souběžnosti](/ef/core/modeling/concurrency):

* EF Core ověří, že se vlastnost po načtení nezměnila. K ověření dochází při volání [metody SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) nebo [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) .
* Pokud byla vlastnost po načtení změněna, je vyvolána výjimka [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) . 

DATABÁZE a datový model musí být nakonfigurovány tak, aby podporovaly vyvolání `DbUpdateConcurrencyException` .

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Zjištění konfliktů souběžnosti u vlastnosti

Konflikty souběžnosti lze zjistit na úrovni vlastnosti pomocí atributu [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) . Atribut lze použít na více vlastností modelu. Další informace najdete v tématu [data anotaces – ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

`[ConcurrencyCheck]`Atribut se v tomto kurzu nepoužívá.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Zjištění konfliktů souběžnosti na řádku

K detekci konfliktů souběžnosti se do modelu přidá sloupec sledování [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) .  `rowversion` :

* Je SQL Server specifické. Jiné databáze nemusí poskytovat podobnou funkci.
* Slouží k určení, že entita nebyla od načtení z databáze změněna. 

DATABÁZE generuje pořadové `rowversion` číslo, které se zvýší při každé aktualizaci řádku. V `Update` příkazu nebo `Delete` `Where` obsahuje klauzule načtenou hodnotu `rowversion` . Pokud se změnil řádek, který se má aktualizovat:

* `rowversion` neodpovídá načtené hodnotě.
* `Update`Příkazy nebo `Delete` nenaleznou řádek, protože `Where` klauzule zahrnuje načtenou hodnotu `rowversion` .
* `DbUpdateConcurrencyException`Je vyvolána výjimka.

Pokud se v EF Core neaktualizovaly žádné řádky `Update` `Delete` příkazem nebo, je vyvolána výjimka souběžnosti.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Přidat vlastnost sledování k entitě oddělení

V *modelu/oddělení. cs*přidejte vlastnost sledování s názvem rowversion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Atribut [timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) určuje, že tento sloupec je zahrnut v `Where` klauzuli `Update` `Delete` příkazů a. Atribut je volán, `Timestamp` protože předchozí verze SQL Server používaly `timestamp` datový typ SQL předtím, než `rowversion` jej nahradil typ SQL.

Rozhraní Fluent API může také určovat vlastnost sledování:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Následující kód ukazuje část T-SQL vygenerované EF Core, když je název oddělení aktualizovaný:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Předchozí zvýrazněný kód ukazuje `WHERE` klauzuli obsahující `RowVersion` . Pokud se databáze `RowVersion` neshoduje s `RowVersion` parametrem ( `@p2` ), žádné řádky se neaktualizují.

Následující zvýrazněný kód ukazuje T-SQL, který ověří právě jeden řádek, který byl aktualizován:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Vrátí počet řádků ovlivněných posledním příkazem. V Neaktualizování řádků EF Core vyvolá `DbUpdateConcurrencyException` .

V okně výstup aplikace Visual Studio můžete zobrazit EF Core T-SQL.

### <a name="update-the-db"></a>Aktualizace databáze

Přidáním `RowVersion` vlastnosti se změní model databáze, který vyžaduje migraci.

Sestavte projekt. V příkazovém okně zadejte následující:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Předchozí příkazy:

* Přidá migrační soubor *_RowVersion. cs migrace/{časového razítka}* .
* Aktualizuje soubor *migrations/SchoolContextModelSnapshot. cs* . Aktualizace přidá do metody následující zvýrazněný kód `BuildModel` :

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Spustí migrace, aby se aktualizovala databáze.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Generování uživatelského rozhraní modelu oddělení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-student-pages) a použijte `Department` pro třídu modelu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Spusťte následující příkaz:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Předchozí příkaz vygeneruje `Department` model. Otevřete projekt v sadě Visual Studio.

Sestavte projekt.

### <a name="update-the-departments-index-page"></a>Aktualizovat stránku indexu oddělení

Modul generování uživatelského rozhraní vytvořil `RowVersion` sloupec pro stránku indexu, ale toto pole by se nemělo zobrazovat. V tomto kurzu se zobrazí poslední bajt, `RowVersion` který vám pomůže pochopit souběžnost. Poslední bajt není zaručený jako jedinečný. Skutečná aplikace se nezobrazila `RowVersion` nebo poslední bajt `RowVersion` .

Aktualizujte stránku indexu:

* Nahraďte index "odděleními".
* Nahraďte značku obsahující `RowVersion` Poslední bajt `RowVersion` .
* Nahraďte FirstMidName řetězcem FullName.

Následující kód ukazuje aktualizovanou stránku:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualizace modelu úprav stránek

*Pages\Departments\Edit.cshtml.cs* aktualizujte pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Aby se zjistil problém souběžnosti, aktualizuje se [původní](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) `rowVersion` hodnotou z entity, kterou načetla. EF Core generuje příkaz SQL UPDATE s klauzulí WHERE obsahující původní `RowVersion` hodnotu. Pokud nejsou žádné řádky ovlivněny příkazem UPDATE (žádné řádky nemají původní `RowVersion` hodnotu), `DbUpdateConcurrencyException` je vyvolána výjimka.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

V předchozím kódu `Department.RowVersion` je hodnota, když byla entita načtena. `OriginalValue` je hodnota v databázi, která `FirstOrDefaultAsync` byla volána v této metodě.

Následující kód Získá hodnoty klienta (hodnoty odeslané do této metody) a hodnoty databáze:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Následující kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od odeslání do `OnPostAsync` :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Následující zvýrazněný kód nastaví `RowVersion` hodnotu na novou hodnotu načtenou z databáze. Až uživatel příště klikne na možnost **Uložit**, bude zachycena pouze chyba souběžnosti, ke kterým dochází od posledního zobrazení stránky pro úpravy.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

`ModelState.Remove`Příkaz je vyžadován, protože `ModelState` má starou `RowVersion` hodnotu. Na Razor stránce `ModelState` má hodnota pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravy

Aktualizovat *stránky/oddělení/upravit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Předchozí kód:

* Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .
* Přidá verzi skrytého řádku. `RowVersion` je nutné přidat, aby postback znovu váže hodnotu.
* Zobrazí poslední bajt `RowVersion` pro účely ladění.
* Nahrazuje `ViewData` silným typem `InstructorNameSL` .

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Souběžnost testů v konfliktu se stránkou pro úpravy

V anglickém oddělení otevřete dvě instance pro úpravy v angličtině:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem **na hypertextový** odkaz pro jazykové oddělení a vyberte **otevřít na nové kartě**.
* Na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.

Dvě karty prohlížeče zobrazují stejné informace.

Změňte název na první kartě prohlížeče a klikněte na **Uložit**.

![Stránka pro úpravy v oddělení 1 po změně](concurrency/_static/edit-after-change-1.png)

Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.

Změňte jiné pole na druhé kartě prohlížeče.

![Stránka pro úpravy v oddělení 2 po změně](concurrency/_static/edit-after-change-2.png)

Klikněte na **Uložit**. Zobrazí se chybové zprávy pro všechna pole, která neodpovídají hodnotám databáze:

![Chybová zpráva stránky pro úpravu oddělení](concurrency/_static/edit-error.png)

Toto okno prohlížeče nemá v úmyslu změnit pole název. Zkopírujte a vložte aktuální hodnotu (jazyky) do pole název. Tabulátor. Ověřování na straně klienta odebere chybovou zprávu.

![Chybová zpráva stránky pro úpravu oddělení](concurrency/_static/cv.png)

Znovu klikněte na **Uložit** . Hodnota, kterou jste zadali na druhé záložce prohlížeče, se uloží. Uložené hodnoty se zobrazí na stránce index.

## <a name="update-the-delete-page"></a>Aktualizace stránky pro odstranění

Aktualizujte odstranit model stránky pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Stránka odstranit detekuje konflikty souběžnosti, když se entita po jejím načtení změnila. `Department.RowVersion` je verze řádku v případě, že byla entita načtena. Když EF Core vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s `RowVersion` . Pokud příkaz SQL DELETE má vliv na nulové řádky:

* `RowVersion`Příkaz v příkazu SQL DELETE se v databázi neshoduje `RowVersion` .
* Je vyvolána výjimka DbUpdateConcurrencyException.
* `OnGetAsync` je volána pomocí `concurrencyError` .

### <a name="update-the-delete-page"></a>Aktualizace stránky pro odstranění

Aktualizovat *stránky/oddělení/odstranit. cshtml* pomocí následujícího kódu:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Předchozí kód provede následující změny:

* Aktualizuje `page` direktivu z `@page` na `@page "{id:int}"` .
* Přidá chybovou zprávu.
* Nahradí FirstMidName pomocí FullName v poli **správce** .
* Změny `RowVersion` pro zobrazení posledního bajtu
* Přidá verzi skrytého řádku. `RowVersion` je nutné přidat, aby postback znovu váže hodnotu.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Souběžnost testů v konfliktu se stránkou odstranit

Vytvořte testovací oddělení.

V testovacím středisku otevřete dvě instance pro odstraňování těchto prohlížečů:

* Spusťte aplikaci a vyberte oddělení.
* Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro testovací oddělení a vyberte **otevřít na nové kartě**.
* Klikněte na odkaz **Upravit** pro testovací oddělení.

Dvě karty prohlížeče zobrazují stejné informace.

Změňte rozpočet na první kartě prohlížeče a klikněte na **Uložit**.

Prohlížeč zobrazí stránku index se změněnou hodnotou a aktualizovaným indikátorem rowVersion. Všimněte si aktualizovaného indikátoru rowVersion, který se zobrazuje na druhém panelu pro odeslání na druhé kartě.

Odstraňte testovací oddělení z druhé karty. Chyba souběžnosti se zobrazuje s aktuálními hodnotami z databáze. Kliknutím na **Odstranit** odstraníte entitu, pokud `RowVersion` se neaktualizovala.

Přečtěte si téma [dědičnosti](xref:data/ef-mvc/inheritance) způsobu dědění datového modelu.

### <a name="additional-resources"></a>Další zdroje informací

* [Tokeny souběžnosti v EF Core](/ef/core/modeling/concurrency)
* [Zpracování souběžnosti v EF Core](/ef/core/saving/concurrency)
* [Verze tohoto kurzu pro YouTube (zpracování konfliktů souběžnosti)](https://youtu.be/EosxHTFgYps)
* [Verze tohoto kurzu pro YouTube (část 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Verze tohoto kurzu pro YouTube (část 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/update-related-data)

::: moniker-end

