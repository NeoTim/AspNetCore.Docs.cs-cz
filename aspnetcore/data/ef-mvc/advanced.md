---
title: 'Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core'
description: Tento kurz představuje užitečná témata pro překročení základy vývoje ASP.NET základní webové aplikace, které používají Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: fc6f8d8c4ab09848cf316be2e522bf5ce3b9ac76
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416231"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core

V předchozím kurzu jste implementovali dědičnost tabulky na hierarchii. Tento kurz představuje několik témat, která jsou užitečná, abyste si byli vědomi, když překročíte základy vývoje ASP.NET základní webové aplikace, které používají core entity frameworku.

V tomto kurzu jste:

> [!div class="checklist"]
> * Provádění nezpracovaných dotazů SQL
> * Volání dotazu k vrácení entit
> * Volání dotazu pro vrácení jiných typů
> * Volání aktualizačního dotazu
> * Prozkoumání dotazů SQL
> * Vytvoření vrstvy abstrakce
> * Další informace o automatickém zjišťování změn
> * Informace o zdrojovém kódu EF Core a plánech rozvoje
> * Naučte se používat dynamické LINQ ke zjednodušení kódu

## <a name="prerequisites"></a>Požadavky

* [Implementovat dědičnost](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Provádění nezpracovaných dotazů SQL

Jednou z výhod použití entity framework je, že se zabrání vázání kódu příliš úzce na konkrétní metodu ukládání dat. Je to tím, že generuje SQL dotazy a příkazy pro vás, což také osvobozuje od nutnosti psát sami. Existují však výjimečné scénáře, kdy potřebujete spustit konkrétní dotazy SQL, které jste vytvořili ručně. Pro tyto scénáře entity framework code first api obsahuje metody, které umožňují předat příkazy SQL přímo do databáze. V EF Core 1.0 máte následující možnosti:

* Metodu `DbSet.FromSql` použijte pro dotazy, které vracejí typy entit. Vrácené objekty musí být typu `DbSet` očekávaného objektem a jsou automaticky sledovány kontextem databáze, pokud sledování [nevypnete](crud.md#no-tracking-queries).

* Použijte `Database.ExecuteSqlCommand` pro příkazy bez dotazu.

Pokud potřebujete spustit dotaz, který vrací typy, které nejsou entity, můžete použít ADO.NET s připojením k databázi poskytované EF. Vrácená data nejsou sledována kontextem databáze, i když tuto metodu použijete k načtení typů entit.

Jak je vždy pravda při spuštění příkazů SQL ve webové aplikaci, je nutné přijmout opatření k ochraně webu před útoky injektáže SQL. Jedním ze způsobů, jak to udělat, je použít parametrizované dotazy a ujistěte se, že řetězce odeslané webovou stránkou nelze interpretovat jako příkazy SQL. V tomto kurzu budete používat parametrizované dotazy při integraci vstupu uživatele do dotazu.

## <a name="call-a-query-to-return-entities"></a>Volání dotazu k vrácení entit

Třída `DbSet<TEntity>` poskytuje metodu, kterou můžete použít ke spuštění dotazu, který vrací entitu typu `TEntity`. Chcete-li zjistit, jak to funguje, `Details` změníte kód v metodě řadiče oddělení.

V *DepartmentsController.cs*v `Details` metodě nahraďte kód, který `FromSql` načítá oddělení voláním metody, jak je znázorněno v následujícím zvýrazněném kódu:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Chcete-li ověřit, zda nový kód funguje správně, vyberte kartu **Oddělení** a potom **podrobnosti** pro jedno z oddělení.

![Podrobnosti o oddělení](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Volání dotazu pro vrácení jiných typů

Dříve jste pro stránku Informace vytvořili mřížku statistik studentů, která zobrazuje počet studentů pro každé datum zápisu. Získali jste data ze sady`_context.Students`entit Studenty ( ) a `EnrollmentDateGroup` použili jste LINQ k promítání výsledků do seznamu objektů modelu zobrazení. Předpokládejme, že chcete napsat sql sám spíše než pomocí LINQ. Chcete-li to provést, je třeba spustit dotaz SQL, který vrací něco jiného než objekty entity. V EF Core 1.0 jedním ze způsobů, jak to udělat, je napsat ADO.NET kódu a získat připojení k databázi z EF.

V *HomeController.cs*nahraďte metodu `About` následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Přidejte příkaz using:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Spusťte aplikaci a přejděte na stránku Informace. Zobrazuje stejná data jako předtím.

![O stránce](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Volání aktualizačního dotazu

Předpokládejme, že správci Univerzity Contoso chtějí provádět globální změny v databázi, například změnu počtu kreditů pro každý kurz. V případě, že univerzita má velký počet kurzů, bylo by neefektivní získat je všechny jako subjekty a změnit je jednotlivě. V této části budete implementovat webovou stránku, která umožňuje uživateli určit faktor, kterým chcete změnit počet kreditů pro všechny kurzy a provedete změnu spuštěním příkazu SQL UPDATE. Webová stránka bude vypadat na následujícím obrázku:

![Aktualizovat stránku Kreditů kurzu](advanced/_static/update-credits.png)

V *CoursesController.cs*přidejte metody UpdateCourseCredits pro HttpGet a HttpPost:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Když řadič zpracuje požadavek HttpGet, `ViewData["RowsAffected"]`nic se vrátí v aplikaci a zobrazení zobrazí prázdné textové pole a tlačítko odeslat, jak je znázorněno na předchozím obrázku.

Po klepnutí na tlačítko **Aktualizovat** je volána metoda HttpPost a multiplikátor má hodnotu zadanou v textovém poli. Kód pak spustí SQL, který aktualizuje kurzy a vrátí počet `ViewData`ovlivněných řádků do zobrazení v aplikaci . Když zobrazení získá `RowsAffected` hodnotu, zobrazí počet aktualizovaných řádků.

V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Zobrazení nebo Kurzy* a potom klepněte na příkaz **Přidat > novou položku**.

V dialogovém okně **Přidat novou položku** klepněte na **ASP.NET jádro** v části **Nainstalováno** v levém podokně, klepněte na **položku Razor View**a pojmenujte nové zobrazení *UpdateCourseCredits.cshtml*.

V *části Zobrazení/Kurzy/UpdateCourseCredits.cshtml*nahraďte kód šablony následujícím kódem:

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Spusťte metodu `UpdateCourseCredits` výběrem karty **Kurzy** a poté přidáte "/UpdateCourseCredits" na konec adresy URL `http://localhost:5813/Courses/UpdateCourseCredits`v adresním řádku prohlížeče (například: ). Do textového pole zadejte číslo:

![Aktualizovat stránku Kreditů kurzu](advanced/_static/update-credits.png)

Klikněte na **Aktualizovat**. Zobrazí se počet ovlivněných řádků:

![Aktualizace ovlivněných řádků stránky Kredity kurzu](advanced/_static/update-credits-rows-affected.png)

Kliknutím na **Zpět do seznamu** zobrazíte seznam kurzů s revidovaným počtem kreditů.

Všimněte si, že produkční kód by zajistil, že aktualizace vždy za následek platná data. Zde uvedený zjednodušený kód by mohl znásobit počet kreditů natolik, aby vedl k číslům větším než 5. (Vlastnost `Credits` má `[Range(0, 5)]` atribut.) Aktualizační dotaz by fungoval, ale neplatná data by mohla způsobit neočekávané výsledky v jiných částech systému, které předpokládají, že počet kreditů je 5 nebo méně.

Další informace o nezpracovaných dotazech SQL naleznete [v tématu Raw SQL Queries](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Prozkoumání dotazů SQL

Někdy je užitečné mít možnost zobrazit skutečné dotazy SQL, které jsou odesílány do databáze. Vestavěné funkce protokolování pro ASP.NET Core se automaticky používá EF Core k zápisu protokolů, které obsahují SQL pro dotazy a aktualizace. V této části uvidíte několik příkladů protokolování SQL.

Otevřete *StudentsController.cs* `Details` a v metodě `if (student == null)` nastavte zarážku na příkazu.

Spusťte aplikaci v režimu ladění a přejděte na stránku Podrobnosti pro studenta.

Přejděte do okna **Výstup** zobrazující výstup ladění a zobrazí se dotaz:

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

Zde si všimnete něčeho, co vás může překvapit:`TOP(2)`SQL vybere až 2 řádky ( ) z tabulky Osoba. Metoda `SingleOrDefaultAsync` není přeložit na 1 řádek na serveru. Důvod:

* Pokud dotaz vrátí více řádků, metoda vrátí null.
* Chcete-li zjistit, zda dotaz vrátí více řádků, EF musí zkontrolovat, zda vrátí alespoň 2.

Všimněte si, že není třeba použít režim ladění a zastavit na zarážky získat výstup protokolování v okně **Výstup.** Je to jen pohodlný způsob, jak zastavit protokolování v okamžiku, kdy se chcete podívat na výstup. Pokud tak neučiníte, protokolování pokračuje a budete muset posunout zpět najít části, které vás zajímají.

## <a name="create-an-abstraction-layer"></a>Vytvoření vrstvy abstrakce

Mnoho vývojářů psát kód k implementaci úložiště a jednotky pracovních vzorů jako obálku kolem kódu, který pracuje s entity framework. Tyto vzory jsou určeny k vytvoření vrstvy abstrakce mezi vrstvou přístupu k datům a vrstvou obchodní logiky aplikace. Implementace těchto vzorů může pomoci izolovat vaši aplikaci před změnami v úložišti dat a může usnadnit automatizované testování částí nebo vývoj řízený testováním (TDD). Psaní dalšího kódu k implementaci těchto vzorů však není vždy nejlepší volbou pro aplikace, které používají EF, z několika důvodů:

* Třída kontextu EF sama o sobě izoluje váš kód z kódu specifické pro úložiště dat.

* Třída kontextu EF může fungovat jako jednotka práce třídy pro aktualizace databáze, které používáte EF.

* EF obsahuje funkce pro implementaci TDD bez psaní kódu úložiště.

Informace o tom, jak implementovat úložiště a jednotky pracovních vzorů, naleznete [v entity Framework 5 verze této série kurzů](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implementuje poskytovatele databáze v paměti, který lze použít pro testování. Další informace naleznete v [tématu Test with InMemory](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Automatická detekce změn

Entity Framework určuje, jak se účetní jednotka změnila (a proto je třeba odeslat aktualizace do databáze) porovnáním aktuálních hodnot entity s původními hodnotami. Původní hodnoty jsou uloženy při dotazování nebo připojení entity. Některé metody, které způsobují automatické zjišťování změn, jsou následující:

* DbContext.SaveChanges

* DbContext.Položka

* ChangeTracker.Položky

Pokud sledujete velký počet entit a voláte jednu z těchto metod mnohokrát ve smyčce, můžete získat významné `ChangeTracker.AutoDetectChangesEnabled` zlepšení výkonu dočasným vypnutím automatického zjišťování změn pomocí vlastnosti. Příklad:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Základní zdrojový kód a plány rozvoje

Zdroj core entity framework [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)je na . Úložiště EF Core obsahuje noční sestavení, sledování problémů, specifikace funkcí, poznámky k návrhu schůzky a [plán budoucího vývoje](https://github.com/dotnet/efcore/wiki/Roadmap). Můžete soubor nebo najít chyby, a přispět.

Přestože zdrojový kód je otevřený, Entity Framework Core je plně podporován jako produkt společnosti Microsoft. Tým Microsoft Entity Framework udržuje kontrolu nad příspěvky, které jsou přijímány a testuje všechny změny kódu, aby byla zajištěna kvalita každé verze.

## <a name="reverse-engineer-from-existing-database"></a>Zpětná inženýrská instrukace z existující databáze

Chcete-li zpětně analyzovat datový model včetně tříd entit z existující databáze, použijte příkaz [scaffold-dbcontext.](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) Podívejte se na [výuku začínáme](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Zjednodušení kódu pomocí dynamického LINQ

[Třetí kurz v této sérii](sort-filter-page.md) ukazuje, jak psát kód LINQ `switch` pomocí pevně kódující názvy sloupců v příkazu. Se dvěma sloupci na výběr, to funguje dobře, ale pokud máte mnoho sloupců kód může získat podrobné. Chcete-li tento problém vyřešit, můžete použít metodu `EF.Property` k určení názvu vlastnosti jako řetězec. Chcete-li vyzkoušet tento `Index` přístup, `StudentsController` nahraďte metodu v následujícím kódu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Poděkování

Tom Dykstra a Rick @RickAndMSFTAnderson (twitter) napsal tento výukový program. Rowan Miller, Diego Vega a další členové týmu Entity Framework pomáhali s revizemi kódu a pomáhali ladit problémy, které vznikly při psaní kódu pro kurzy. John Parente a Paul Goldman pracovali na aktualizaci kurzu pro ASP.NET Core 2.2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Odstraňování běžných chyb

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll používá jiný proces

Chybová zpráva:

> Nelze otevřít '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' pro zápis – Proces nemůže získat přístup k souboru ...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll', protože je používán jiným procesem.

Řešení:

Zastavte web ve správě IIS Express. Přejděte na panel windows, najděte službu IIS Express a klepněte pravým tlačítkem myši na její ikonu, vyberte web Univerzity Contoso a klepněte na příkaz **Zastavit web**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Migrace šavle bez kódu v metodách Nahoru a Dolů

Možná příčina:

Příkazy příkazového příkazu PŘÍKAZEf automaticky neuzavírají a neukládají soubory kódu. Pokud máte neuložené změny při `migrations add` spuštění příkazu, EF nenajde změny.

Řešení:

Spusťte `migrations remove` příkaz, uložte změny `migrations add` kódu a znovu spusťte příkaz.

### <a name="errors-while-running-database-update"></a>Chyby při spuštění aktualizace databáze

Je možné získat další chyby při provádění změn schématu v databázi, která má existující data. Pokud se vám budou chyby migrace vyřešit, můžete změnit název databáze v připojovacím řetězci nebo databázi odstranit. S novou databází nejsou k dispozici žádná data k migraci a příkaz databáze aktualizací je mnohem pravděpodobnější, že bude dokončen bez chyb.

Nejjednodušší přístup je přejmenovat databázi v *appsettings.json*. Při příštím spuštění `database update`bude vytvořena nová databáze.

Chcete-li odstranit databázi ve ssoxu, klepněte pravým tlačítkem myši na databázi, klepněte na příkaz **Odstranit**a potom v dialogovém okně **Odstranit databázi** vyberte **zavřít existující připojení** a klepněte na tlačítko **OK**.

Chcete-li odstranit databázi pomocí příkazového příkazu příkazového příkazu, spusťte příkaz příkazu příkazu příkazu k příkazu: `database drop`

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Při vyhledání instance serveru SQL Server došlo k chybě.

Chybová zpráva:

> Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný. Ověřte, zda je název instance správný a zda je server SQL Server nakonfigurován tak, aby umožňoval vzdálená připojení. (poskytovatel: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)

Řešení:

Zkontrolujte připojovací řetězec. Pokud jste ručně odstranili soubor databáze, změňte název databáze ve stavebním řetězci a začněte znovu s novou databází.

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

Další informace o ef core naleznete [v dokumentaci core entity frameworku](/ef/core). Kniha je také k dispozici: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).

Informace o nasazení webové aplikace naleznete <xref:host-and-deploy/index>v tématu .

Informace o dalších tématech souvisejících s ASP.NET core MVC, například ověřování a autorizace, naleznete v tématu <xref:index>.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Provedené nezpracované dotazy SQL
> * Nazývá se dotaz na vrácení entit
> * Volal dotaz vrátit jiné typy
> * Nazývá se aktualizační dotaz
> * Zkoumané dotazy SQL
> * Vytvoření vrstvy abstrakce
> * Informace o automatickém zjišťování změn
> * Informace o zdrojovém kódu EF Core a plánech rozvoje
> * Naučili se používat dynamické LINQ ke zjednodušení kódu

Tím se dokončí tato řada kurzů o použití jádra entity frameworku v ASP.NET základní aplikace MVC. Tato řada pracovala s novou databází; Alternativou je zpětná inženýrská organizace modelu z existující databáze.

> [!div class="nextstepaction"]
> [Kurz: EF Core s MVC, existující databáze](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
