---
title: 'Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core'
description: Tento kurz představuje užitečné tématech překračují základní informace o vývoji webových aplikací ASP.NET Core využívající Entity Framework Core.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: c4804bd6614c7d5a2a30c8f59a645f603929ad52
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264585"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core

V předchozím kurzu jste implementovali tabulky na hierarchii dědičnosti. Tento kurz představuje několik témat, které jsou užitečné mít na paměti při nad rámec základní informace o vývoji webových aplikací ASP.NET Core využívající Entity Framework Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Spouštějte nezpracované dotazy SQL
> * Volání dotaz, který vrací entity
> * Volání dotaz, který jsou vraceny další typy
> * Volání dotaz update
> * Zkontrolujte dotazy SQL
> * Vytvořit vrstvu HAL
> * Další informace o zjišťování automaticky změnit
> * Další informace o plánech zdrojového kódu a vývoj EF Core
> * Další informace o použití dynamického LINQ pro zjednodušení kódu

## <a name="prerequisites"></a>Požadavky

* [Implementace dědičnosti s EF Core ve webové aplikaci ASP.NET Core MVC](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Spouštějte nezpracované dotazy SQL

Jednou z výhod používající nástroj Entity Framework je, že se eliminuje propojí váš kód příliš úzce na konkrétní metodu ukládat data. Dělá to tak, že generování dotazy SQL a příkazy, které také díky které by bylo nutné napsat sami. Ale existují výjimečné situace, kdy je potřeba spustit konkrétní dotazy SQL, které ručně vytvoříte. Pro tyto scénáře prvního rozhraní API sady Entity Framework kód obsahuje metody, které vám umožní předat příkazů SQL přímo do databáze. Máte následující možnosti v EF Core 1.0:

* Použití `DbSet.FromSql` metodu pro dotazy vracející typy entit. Vrácených objektů musí být typu očekává `DbSet` objekt a že jste automaticky sleduje provedené kontext databáze není-li je [vypnout sledování](crud.md#no-tracking-queries).

* Použití `Database.ExecuteSqlCommand` pro příkazy bez dotazů.

Pokud je potřeba spustit dotaz, který vrátí typy, které nejsou entity, můžete pomocí připojení k databázi poskytované EF ADO.NET. Vrácená data není sledována kontext databáze, i když používáte tuto metodu pro načtení typů entit.

Jak platí vždy při spuštění příkazů SQL ve webové aplikaci, je nutné provést opatření k ochraně před útoky prostřednictvím injektáže SQL serveru. Jednou z možností, které se pomocí parametrizovaných dotazů se ujistěte, že řetězce odeslané na webové stránce se nedá interpretovat jako příkazy SQL. V tomto kurzu použijete parametrizovaných dotazů při integraci uživatelský vstup do dotazu.

## <a name="call-a-query-to-return-entities"></a>Volání dotaz, který vrací entity

`DbSet<TEntity>` Třída poskytuje metodu, která můžete použít k provedení dotazu, který vrací entity typu `TEntity`. Pokud chcete zobrazit, jak vám to funguje budete změnit kód v `Details` metody kontroleru oddělení.

V *DepartmentsController.cs*v `Details` metoda, nahraďte kód, který načte oddělení s `FromSql` volání metody, jak je znázorněno v následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

Chcete-li ověřit, že nový kód funguje správně, vyberte **oddělení** kartu a potom **podrobnosti** pro jeden z jako vodítko použijte oddělení.

![Podrobnosti o oddělení](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Volání dotaz, který jsou vraceny další typy

Dříve jste vytvořili mřížky student statistiky o stránky, které jsme si ukázali, počet studentů pro každé datum registrace. Jste získali data ze sady entit pro studenty (`_context.Students`) a použít k projekci výsledků do seznamu LINQ `EnrollmentDateGroup` zobrazit objekty modelu. Předpokládejme, že chcete napsat SQL samotné spíše než pomocí jazyka LINQ. Chcete-li provést, je nutné spustit dotaz SQL, který vrací jinou hodnotu než objekty entity. Jeden způsob, jak to udělat v EF Core 1.0, je psaní kódu rozhraní ADO.NET a získání připojení k databázi z EF.

V *HomeController.cs*, nahraďte `About` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Přidat sadu pomocí příkazu:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Spusťte aplikaci a přejděte na stránku o. Zobrazí se stejná data, která předtím.

![O stránku](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Volání dotaz update

Předpokládejme, že správce společnosti Contoso University chcete provést globálních změn v databázi, jako je například změna číslo kredity pro každý kurz. Pokud univerzity má velký počet kurzů, bylo by neefektivní načíst vše jako entity a měnit je jednotlivě. V této části budete implementovat webovou stránku, která umožňuje uživateli zadat faktor, podle kterého chcete změnit počet kredity pro všechny kurzy a provede změny spuštěním příkazu SQL UPDATE. Webové stránky bude vypadat jako na následujícím obrázku:

![Stránka pro aktualizaci kurzu kredity](advanced/_static/update-credits.png)

V *CoursesController.cs*, přidejte UpdateCourseCredits metody třídy MetadataExchangeClientMode a HttpPost:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Pokud kontroler zpracovává požadavek HttpGet, nevrátí `ViewData["RowsAffected"]`, a zobrazení zobrazí prázdné textové pole a tlačítko pro odeslání, jak je znázorněno na předchozím obrázku.

Když **aktualizace** po kliknutí na tlačítko, je volána metoda HttpPost a multiplikátor má hodnota zadaná v textovém poli. Kód spustí SQL, který aktualizuje kurzy a vrátí počet ovlivněných řádků na zobrazení `ViewData`. Při zobrazení získá `RowsAffected` hodnotu, zobrazí počet aktualizovaných řádků.

V **Průzkumníku řešení**, klikněte pravým tlačítkem *zobrazení/kurzy* složku a pak klikněte na tlačítko **Přidat > Nová položka**.

V **přidat novou položku** dialogového okna, klikněte na tlačítko **ASP.NET Core** pod **nainstalováno** v levém podokně klikněte na tlačítko **zobrazení Razor**a pojmenujte nové zobrazení  *UpdateCourseCredits.cshtml*.

V *Views/Courses/UpdateCourseCredits.cshtml*, nahraďte kód šablony následujícím kódem:

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Spustit `UpdateCourseCredits` metodu tak, že vyberete **kurzy** kartu a následným přidáním "/ UpdateCourseCredits" na konci adresy URL v adresním řádku prohlížeče (například: `http://localhost:5813/Courses/UpdateCourseCredits`). Zadejte číslo v textovém poli:

![Stránka pro aktualizaci kurzu kredity](advanced/_static/update-credits.png)

Klikněte na tlačítko **aktualizace**. Zobrazí počet ovlivněných řádků:

![Počet ovlivněných řádků kurzu kredity stránku aktualizace](advanced/_static/update-credits-rows-affected.png)

Klikněte na tlačítko **zpět do seznamu** zobrazíte seznam kurzů s revidované množství kreditu, které.

Všimněte si, že produkčního kódu zajistí, že se aktualizace vždy výsledkem platná data. Zjednodušené kód zobrazený zde vynásobit počet dostatečně kredity za následek čísla větší než 5. ( `Credits` Má vlastnost `[Range(0, 5)]` atribut.) Dotaz update bude fungovat, ale neplatná data může vést k neočekávaným výsledkům v jiných částí systému, které předpokládají, že je číslo kredity ve výši 5 nebo menší.

Další informace o nezpracované dotazy SQL najdete v tématu [nezpracované dotazy SQL](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Zkontrolujte dotazy SQL

Někdy je vhodné se může zobrazit skutečné dotazy SQL, které se odesílají do databáze. Funkce vestavěné protokolování pro ASP.NET Core je automaticky používá EF Core zápis protokolů, které obsahují SQL pro dotazy a aktualizace. V této části uvidíte několik příkladů protokolování SQL.

Otevřít *StudentsController.cs* a `Details` metody nastavte zarážku na `if (student == null)` příkazu.

Spusťte aplikaci v režimu ladění a přejděte na stránku podrobností pro student.

Přejděte **výstup** zobrazující ladění okna výstupu a zobrazit dotaz:

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

Můžete si všimnout sem něco, co možná vás překvapí: SQL vybere až 2 řádky (`TOP(2)`) z tabulky osob. `SingleOrDefaultAsync` Metoda nepřekládá na 1 řádek na serveru. Tady jsou důvody:

* Pokud dotaz by vrátil více řádků, metoda vrátí hodnotu null.
* Pokud chcete zjistit, zda dotaz by vrátil více řádků, EF má na registraci, pokud vrací alespoň 2.

Všimněte si, že není nutné použít režim ladění a zastavení na zarážce k získání výstupu protokolování **výstup** okna. Je jenom pohodlný způsob, jak zastavit protokolování v okamžiku, kdy chcete výstup. Pokud není to uděláte, pokračuje v protokolování a budete muset přejděte zpět a najděte částí, které vás zajímají.

## <a name="create-an-abstraction-layer"></a>Vytvořit vrstvu HAL

Mnoho vývojářů napsat kód pro implementaci úložiště a jednotky pracovních vzorů jako obálka kolem kód, který funguje s Entity Framework. Tyto vzory jsou určená k vytvoření abstraktní vrstvu mezi vrstva přístupu k datům a vrstvu obchodní logiky aplikace. Implementaci těchto vzorců může pomoci izolovat aplikace před změnami v úložišti dat a mohou usnadnit automatizované testování částí nebo testy řízený vývoj (TDD). Ale psaním dalšího kódu k implementaci těchto vzorců není vždy nejlepší volbou pro aplikace, které používají EF, z několika důvodů:

* Vlastní třídy kontextu EF insulates kódu z konkrétní data úložiště kódu.

* Třídy kontextu EF může fungovat jako třída jednotek práce pro databázi aktualizace, který používáte pomocí EF.

* EF obsahuje funkce pro implementaci TDD bez psaní kódu v úložišti.

Informace o tom, jak implementovat úložiště a jednotky pracovních vzorů najdete v tématu [verze Entity Framework 5 v této sérii kurzů](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implementuje poskytovatele databáze v paměti, který lze použít pro testování. Další informace najdete v tématu [Test s InMemory](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Automaticky změnit zjišťování

Entity Framework Určuje, jak se entity změnil (a proto aktualizací musí být odeslán do databáze) srovnáním aktuální entity s původními hodnotami. Původní hodnoty jsou uloženy při entity je dotazovat nebo připojen. Některé metody, které způsobí automatickou změnu zjišťování jsou následující:

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

Pokud sledujete velké množství entit a volání jedné z těchto metod v mnoha případech ve smyčce, můžete dosáhnout výrazné zlepšení výkonu dočasně vypnout automatickou změnu pomocí zjišťování `ChangeTracker.AutoDetectChangesEnabled` vlastnost. Příklad:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Core zdrojového kódu a vývoj plány

Zdrojové Entity Framework Core je na [ https://github.com/aspnet/EntityFrameworkCore ](https://github.com/aspnet/EntityFrameworkCore). EF Core úložiště obsahuje denně automatizovaných buildů, sledování problémů, funkce specifikace, poznámky, návrhu a [plán pro budoucí vývoj](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap). Soubor nebo najít chyby a přispívat.

I když se zdrojový kód je otevřený, Entity Framework Core plně podporovat jako produkt společnosti Microsoft. Tým Microsoft Entity Framework udržuje ovládacího prvku nad tím, které jsou přijaty příspěvky a testy k zajištění kvality každé vydané verze všech změn kódu.

## <a name="reverse-engineer-from-existing-database"></a>Zpětná analýza z existující databáze

Chcete-li provést zpětnou analýzu datový model, včetně tříd entit z existující databáze, použijte [vygenerované uživatelské rozhraní dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) příkazu. Zobrazit [úvodním kurzu](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Zjednodušení kód pomocí dynamických LINQ

[Třetím kurzu této série](sort-filter-page.md) ukazuje, jak napsat kód LINQ podle pevného kódování názvů sloupců v `switch` příkazu. Se dvěma sloupci lze vybírat to funguje správně, ale pokud máte mnoho sloupců kód by mohl získat podrobné. K vyřešení tohoto problému, můžete použít `EF.Property` metody zadejte název vlastnosti jako řetězec. Pokud chcete vyzkoušet tento přístup, nahraďte `Index` metodu `StudentsController` následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Potvrzení

Petr Dykstra a Rick Anderson (twitter @RickAndMSFT) napsal v tomto kurzu. Rowan Miller, Diegu Vega a ostatní členové týmu, Entity Framework s asistencí s revizemi kódu a pomohl ladění problémů, které vznikly, když jsme se pro kurzy psaní kódu. Jan Parente a Paul Goldman pracovali na aktualizaci kurz pro ASP.NET Core 2.2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Řešení běžných chyb

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll používá jiný proces

Chybová zpráva:

> Nelze otevřít "... bin\Debug\netcoreapp1.0\ContosoUniversity.dll" pro zápis--"proces nemůže otevřít soubor"... \bin\Debug\netcoreapp1.0\ContosoUniversity.dll' protože je používán jiným procesem.

Řešení:

Zastavte lokalitu ve službě IIS Express. Přejít na hlavním panelu systému Windows najít službu IIS Express a klikněte pravým tlačítkem na ikonu, vyberte lokalitu vysoké školy Contoso a potom klikněte na tlačítko **zastavení webu**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Migrace generované uživatelské rozhraní bez kódu v nahoru a dolů metody

Možné příčiny:

Příkazy rozhraní příkazového řádku EF není automaticky zavřete a uložte soubory s kódem. Pokud máte neuložené změny při spuštění `migrations add` příkazu EF nenajde provedené změny.

Řešení:

Spustit `migrations remove` příkazu, uložte provedené změny kódu a znovu spustit `migrations add` příkazu.

### <a name="errors-while-running-database-update"></a>Chyby při spuštění aktualizace databáze

Je možné získat další chyby při provedení změn schématu v databázi, která obsahuje už existující data. Pokud se zobrazí chyby při migraci, které nelze vyřešit, můžete změnit název databáze v připojovacím řetězci nebo odstranit databázi. S novou databázi nejsou žádná data k migraci a příkaz aktualizace databáze je mnohem pravděpodobnější k dokončení bez chyb.

Nejjednodušším způsobem je přejmenovat databázi v *appsettings.json*. Při příštím spuštění `database update`, vytvoří se nová databáze.

Odstranění databáze v SSOX, klikněte pravým tlačítkem na databázi, klikněte na tlačítko **odstranit**a pak v **odstranit databázi** dialogového okna vyberte **ukončete stávající připojení** a klikněte na tlačítko  **OK**.

Pokud chcete odstranit databázi pomocí rozhraní příkazového řádku, spusťte `database drop` příkazu rozhraní příkazového řádku:

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Chyba vyhledáním instanci systému SQL Server

Chybová zpráva:

> Při navazování připojení k serveru SQL Server došlo k chybě související se sítí nebo s instancí. Server nebyl nalezen nebo nebyl přístupný. Ověřte, zda je název instance správný a, SQL Server je nakonfigurován pro povolit vzdálená připojení. (poskytovatel: Rozhraní sítě systému SQL, chyba: 26 - Chyba při vyhledávání zadaného serveru či Instance)

Řešení:

Zkontrolujte připojovací řetězec. Pokud jste ručně odstranili databázový soubor, změňte název databáze v řetězci konstrukce začít znovu s novou databázi.

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

Další informace o EF Core najdete v článku [dokumentace Entity Framework Core](/ef/core). Kniha je také k dispozici: [Entity Framework Core v akci](https://www.manning.com/books/entity-framework-core-in-action).

Informace o tom, jak nasadit webovou aplikaci, najdete v části <xref:host-and-deploy/index>.

Informace o dalších témat souvisejících s ASP.NET Core MVC, jako je například ověřování a autorizaci, najdete v části <xref:index>.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Prováděné nezpracované dotazy SQL
> * Volá se dotaz, který vrací entity
> * Volá se dotaz, který jsou vraceny další typy
> * Volá se, k aktualizaci dotazu
> * Přezkoumán dotazy SQL
> * Vytvořit vrstvu HAL
> * Dozvěděli jste se o změnu automatické zjišťování
> * Dozvěděli jste se o EF Core zdrojového kódu a vývoj plány
> * Zjistili jste, jak používat dynamické LINQ pro zjednodušení kódu

Dokončení tohoto postupu Tato série kurzů týkající se použití Entity Framework Core v aplikaci ASP.NET Core MVC. Pokud chcete další informace o použití EF 6 pomocí ASP.NET Core, najdete v následujícím článku.
> [!div class="nextstepaction"]
> [EF 6 s ASP.NET Core](../entity-framework-6.md)
