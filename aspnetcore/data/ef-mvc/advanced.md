---
title: 'Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core'
description: V tomto kurzu se seznámíte s užitečnými tématy, která se týkají vývoje ASP.NET Core webových aplikací, které používají Entity Framework Core.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: 913511948def86cb2065459a3fcf5f16362a92d5
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975036"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core

V předchozím kurzu jste implementovali dědičnost tabulek na hierarchii. V tomto kurzu se seznámíte s několika tématy, která jsou užitečná, když překročíte základy vývoje ASP.NET Core webových aplikací, které používají Entity Framework Core.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Provádění nezpracovaných dotazů SQL
> * Volání dotazu pro vrácení entit
> * Volání dotazu pro vrácení jiných typů
> * Volání aktualizačního dotazu
> * Kontrola dotazů SQL
> * Vytvoření vrstvy abstrakce
> * Další informace o automatickém zjišťování změn
> * Další informace o EF Core zdrojového kódu a vývojářských plánech
> * Naučte se používat dynamickou technologii LINQ ke zjednodušení kódu

## <a name="prerequisites"></a>Požadavky

* [Implementovat dědičnost](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Provádění nezpracovaných dotazů SQL

Jednou z výhod používání Entity Framework je, že se vyhnete tomu, že váš kód je příliš úzce k určité metodě ukládání dat. Provede to tím, že vygeneruje dotazy a příkazy SQL za vás, což vám taky zabrání v jejich psaní. Existují však výjimečné scénáře, pokud potřebujete spustit konkrétní dotazy SQL, které jste vytvořili ručně. V těchto scénářích obsahuje rozhraní API Entity Framework Code First metody, které vám umožní předat příkazy SQL přímo do databáze. V EF Core 1,0 máte následující možnosti:

* `DbSet.FromSql` Použijte metodu pro dotazy, které vracejí typy entit. Vrácené objekty musí být typu očekávaného `DbSet` objektem a automaticky sledovány pomocí kontextu databáze, pokud nevypnete [sledování](crud.md#no-tracking-queries).

* Použijte příkaz `Database.ExecuteSqlCommand` pro příkazy, které nejsou dotazem.

Pokud potřebujete spustit dotaz, který vrací typy, které nejsou entitami, můžete použít ADO.NET s databázovým připojením poskytovaným EF. Vrácená data nejsou sledována kontextem databáze, a to i v případě, že použijete tuto metodu k načtení typů entit.

Jak je vždy true při provádění příkazů SQL ve webové aplikaci, je nutné podniknout preventivní opatření k ochraně vašeho webu před útoky prostřednictvím injektáže SQL. Jedním ze způsobů, jak to provést, je použít parametrizované dotazy, aby se zajistilo, že řetězce odeslané webovou stránkou nejde interpretovat jako příkazy SQL. V tomto kurzu použijete parametrizované dotazy při integraci vstupu uživatele do dotazu.

## <a name="call-a-query-to-return-entities"></a>Volání dotazu pro vrácení entit

Třída poskytuje metodu, kterou lze použít ke spuštění dotazu, který vrací entitu typu `TEntity`. `DbSet<TEntity>` Chcete-li zjistit, jak to funguje, změňte kód v `Details` metodě řadiče oddělení.

V *DepartmentsController.cs*v `Details` metodě nahraďte kód, který získá oddělení, pomocí `FromSql` volání metody, jak je znázorněno v následujícím zvýrazněném kódu:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Chcete-li ověřit, zda nový kód funguje správně, vyberte kartu **oddělení** a potom **Podrobnosti** pro jedno z oddělení.

![Podrobnosti o oddělení](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Volání dotazu pro vrácení jiných typů

Dříve jste vytvořili tabulku statistik studentů pro stránku o produktu, která ukázala počet studentů pro každé datum registrace. Dostali jste data ze sady entit Students (`_context.Students`) a pomocí LINQ můžete promítnout výsledky do `EnrollmentDateGroup` seznamu objektů modelu zobrazení. Předpokládejme, že chcete napsat samotný SQL místo použití LINQ. K tomu je nutné spustit dotaz SQL, který vrací jinou hodnotu než objekty entity. V EF Core 1,0 je jedním ze způsobů, jak to udělat, je zápis ADO.NET kódu a získání připojení k databázi z EF.

V *HomeController.cs*nahraďte `About` metodu následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Přidat sadu pomocí příkazu:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Spusťte aplikaci a pokračujte na stránku o produktu. Zobrazuje stejná data jako předtím.

![O stránce](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Volání aktualizačního dotazu

Předpokládejme, že správci služby contoso University chtějí v databázi provádět globální změny, jako je třeba Změna počtu kreditů pro každý kurz. Pokud má univerzita velký počet kurzů, je třeba je neefektivně načíst jako entity a jednotlivě je měnit. V této části implementujete webovou stránku, která uživateli umožní zadat faktor, podle kterého se má změnit počet kreditů pro všechny kurzy, a provedete změnu provedením příkazu SQL UPDATE. Webová stránka bude vypadat jako na následujícím obrázku:

![Stránka aktualizovat kredity kurzu](advanced/_static/update-credits.png)

V *CoursesController.cs*přidejte metody UpdateCourseCredits pro HttpGet a HTTPPOST:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Když kontroler zpracuje požadavek HttpGet, nic se nevrátí `ViewData["RowsAffected"]`a v zobrazení se zobrazí prázdné textové pole a tlačítko Odeslat, jak je znázorněno na předchozím obrázku.

Po kliknutí na tlačítko **aktualizovat** se zavolá metoda HTTPPOST a násobitel má hodnotu zadanou v textovém poli. Kód potom spustí SQL, který aktualizuje kurzy a vrátí počet ovlivněných řádků do zobrazení v `ViewData`. Když zobrazení získá `RowsAffected` hodnotu, zobrazí se počet aktualizovaných řádků.

V **Průzkumník řešení**klikněte pravým tlačítkem na složku *views/kurzy* a pak klikněte na **Přidat > Nová položka**.

V dialogovém okně **Přidat novou položku** klikněte **ASP.NET Core** v části **nainstalováno** v levém podokně klikněte na **zobrazení Razor**a pojmenujte nové zobrazení *UpdateCourseCredits. cshtml*.

V *zobrazeních/kurzů/UpdateCourseCredits. cshtml*nahraďte kód šablony následujícím kódem:

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Spusťte metodu tak, že vyberete kartu **kurzy** a pak na konec adresy URL v adresním řádku prohlížeče přidáte "/UpdateCourseCredits" (například: `http://localhost:5813/Courses/UpdateCourseCredits`). `UpdateCourseCredits` Do textového pole zadejte číslo:

![Stránka aktualizovat kredity kurzu](advanced/_static/update-credits.png)

Klikněte na tlačítko **aktualizace**. Zobrazí se počet ovlivněných řádků:

![Aktualizace ovlivněných řádků na stránce kurzů](advanced/_static/update-credits-rows-affected.png)

Kliknutím na **zpět na seznam** zobrazíte seznam kurzů s revidovaným počtem kreditů.

Všimněte si, že produkční kód zajistí, že aktualizace vždy mají za následek platná data. Zde zobrazený zjednodušený kód může vynásobit počet kreditů, které jsou dostatečné k tomu, aby byly čísla větší než 5. `Credits` ( Vlastnost`[Range(0, 5)]` má atribut.) Aktualizační dotaz by mohl fungovat, ale neplatná data by mohla způsobit neočekávané výsledky v jiných částech systému, které předpokládají, že počet kreditů je 5 nebo méně.

Další informace o nezpracovaných dotazech SQL naleznete v tématu [raw SQL dotazy](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Kontrola dotazů SQL

Někdy je užitečné, abyste si mohli prohlédnout skutečné dotazy SQL, které se odesílají do databáze. Integrovaná funkce protokolování pro ASP.NET Core automaticky používá EF Core k zápisu protokolů, které obsahují SQL pro dotazy a aktualizace. V této části se zobrazí některé příklady protokolování SQL.

Otevřete *StudentsController.cs* a v `Details` metodě nastavte zarážku na `if (student == null)` příkazu.

Spusťte aplikaci v režimu ladění a pokračujte na stránku podrobností pro studenta.

Přejděte do okna **výstup** zobrazující výstup ladění a zobrazí se dotaz:

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

Všimnete si, že se vám může stát, že váš příkaz SQL vybere až 2 řádky (`TOP(2)`) z tabulky Person. `SingleOrDefaultAsync` Metoda není přeložena na 1 řádek na serveru. Důvody:

* Pokud by dotaz vrátil více řádků, vrátí metoda hodnotu null.
* Chcete-li zjistit, zda dotaz by vrátil více řádků, EF musí ověřit, zda se vrátí alespoň 2.

Všimněte si, že nemusíte používat režim ladění a zastavte zarážku, abyste získali výstup protokolování v okně **výstup** . Je to jenom pohodlný způsob, jak zastavit protokolování v místě, kde chcete podívat na výstup. Pokud to neuděláte, budete pokračovat v protokolování a budete muset přejít zpátky a vyhledat části, které vás zajímají.

## <a name="create-an-abstraction-layer"></a>Vytvoření vrstvy abstrakce

Mnoho vývojářů napíše kód, který implementuje úložiště a pracovní postupy jako obálku kolem kódu, který pracuje s Entity Framework. Tyto vzory mají za cíl vytvořit vrstvu abstrakce mezi vrstvou pro přístup k datům a vrstvou obchodní logiky aplikace. Implementace těchto vzorů vám může přispět k izolaci aplikace před změnami v úložišti dat a může usnadnit automatizované testování částí nebo vývoj řízený testováním (TDD). Nicméně psaní dalšího kódu pro implementaci těchto vzorů není vždy nejlepší volbou pro aplikace, které používají EF, z několika důvodů:

* Třída kontextu EF sama neizolací váš kód z kódu specifického pro úložiště dat.

* Třída kontextu EF může fungovat jako jednotka pro práci s aktualizacemi databáze, které používáte pomocí EF.

* EF obsahuje funkce pro implementaci služby TDD bez psaní kódu úložiště.

Informace o implementaci vzorového úložiště a pracovní jednotky najdete v části [Entity Framework 5 této série kurzů](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implementuje poskytovatele databáze v paměti, který lze použít k testování. Další informace najdete v tématu [test s](/ef/core/miscellaneous/testing/in-memory)nepamětí.

## <a name="automatic-change-detection"></a>Automatické zjišťování změn

Entity Framework určuje, jak se entita změnila (takže se aktualizace musí odeslat do databáze) porovnáním aktuálních hodnot entity s původními hodnotami. Původní hodnoty se uloží, když je entita dotazována nebo připojena. Některé z metod, které způsobují automatickou detekci změn, jsou následující:

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker. Entries

Pokud sledujete velký počet entit a v rámci smyčky několikrát voláte jednu z těchto metod, můžete dosáhnout výrazného zlepšení výkonu tím, že se `ChangeTracker.AutoDetectChangesEnabled` při automatickém vypnutí automatického zjišťování změn použije vlastnost. Příklad:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Core zdrojový kód a vývojové plány

Zdroj Entity Framework Core je v [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore). EF Core úložiště obsahuje noční buildy, sledování problémů, specifikace funkcí, návrhy poznámek na schůzce a [plán pro budoucí vývoj](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap). Můžete soubor nebo najít chyby a přispívat.

I když je zdrojový kód otevřený, Entity Framework Core je plně podporovaný jako produkt společnosti Microsoft. Tým Microsoft Entity Framework udržuje kontrolu nad tím, které příspěvky jsou přijaty, a testuje všechny změny kódu, aby se zajistila kvalita jednotlivých verzí.

## <a name="reverse-engineer-from-existing-database"></a>Zpětná analýza z existující databáze

Chcete-li provést zpětnou analýzu datového modelu, včetně tříd entit z existující databáze, použijte příkaz pro [generování uživatelského rozhraní (DbContext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) ). Přečtěte si [Úvodní kurz](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Zjednodušení kódu pomocí dynamického jazyka LINQ

[Třetí kurz v této sérii](sort-filter-page.md) ukazuje, jak napsat kód LINQ pomocí pevného kódování názvů sloupců v `switch` příkazu. Se dvěma sloupci, ze kterých si můžete vybrat, to funguje dobře, ale pokud máte mnoho sloupců, může kód získat podrobné. Chcete-li tento problém vyřešit, můžete použít `EF.Property` metodu k určení názvu vlastnosti jako řetězce. Chcete-li vyzkoušet tento přístup, nahraďte `Index` metodu `StudentsController` v kódu následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Potvrzení

V tomto kurzu jste napsali Dykstra @RickAndMSFTa Rick Anderson (Twitter). Rowan Miller, Diegu Vega a další členové Entity Framework týmu s asistencí revize kódu a pomohli ladit problémy, které vznikly při psaní kódu pro kurzy. Jan rodiče a Paul Goldman pracovali na aktualizaci kurzu pro ASP.NET Core 2,2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Řešení běžných chyb

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity. dll používá jiný proces.

Chybová zpráva:

> Nejde otevřít... bin\Debug\netcoreapp1.0\ContosoUniversity.dll ' pro zápis-' proces nemůže získat přístup k souboru '. ..\bin\Debug\netcoreapp1.0\ContosoUniversity.dll ', protože je používán jiným procesem.

Řešení

Zastavte lokalitu v IIS Express. Přejděte na hlavní panel systému Windows, vyhledejte IIS Express a klikněte pravým tlačítkem na jeho ikonu, vyberte web společnosti Contoso University a pak klikněte na **Zastavit Web**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Replikace – generování uživatelského rozhraní bez kódu v rámci metod up a Down

Možná příčina:

Příkazy EF CLI automaticky nezavřou a neukládají soubory kódu. Pokud máte neuložené změny při spuštění `migrations add` příkazu, EF nezjistí vaše změny.

Řešení

Spusťte příkaz, uložte změny kódu a `migrations add` spusťte příkaz znovu. `migrations remove`

### <a name="errors-while-running-database-update"></a>Chyby při spuštění aktualizace databáze

Při provádění změn schématu v databázi, která obsahuje existující data, je možné získat další chyby. Pokud se zobrazí chyby migrace, které nemůžete vyřešit, můžete buď změnit název databáze v připojovacím řetězci, nebo databázi odstranit. V případě nové databáze není k dispozici žádná data k migraci a příkaz Update-Database je mnohem pravděpodobnější, že se nedokončí bez chyb.

Nejjednodušším přístupem je přejmenovat databázi v souboru *appSettings. JSON*. Při příštím spuštění `database update`se vytvoří nová databáze.

Databázi v SSOX odstraníte tak, že kliknete pravým tlačítkem na databázi, kliknete na **Odstranit**a pak v dialogovém okně **odstranit databázi** vyberete **Zavřít existující připojení** a kliknete na **OK**.

Chcete-li odstranit databázi pomocí rozhraní příkazového řádku, `database drop` spusťte příkaz CLI:

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Při hledání instance SQL Server došlo k chybě.

Chybová zpráva:

> Při navazování připojení k SQL Server došlo k chybě související se sítí nebo instanci. Server nebyl nalezen nebo k němu nelze získat přístup. Ověřte, zda je název instance správný a zda je SQL Server nakonfigurovaná tak, aby povolovala vzdálená připojení. zprostředkovatele Síťová rozhraní SQL, chyba: 26 – Chyba při hledání zadaného serveru/instance)

Řešení

Ověřte připojovací řetězec. Pokud jste soubor databáze odstranili ručně, změňte název databáze v řetězci konstrukce, aby bylo možné začít znovu s novou databází.

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

Další informace o EF Core najdete v dokumentaci k [Entity Framework Core](/ef/core). K dispozici je také kniha: [Entity Framework Core v akci](https://www.manning.com/books/entity-framework-core-in-action).

Informace o tom, jak nasadit webovou aplikaci, najdete v <xref:host-and-deploy/index>tématu.

Informace o dalších tématech souvisejících s ASP.NET Core MVC, jako je ověřování a autorizace, najdete v <xref:index>tématu.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Provedené nezpracované dotazy SQL
> * Volal se dotaz na vrácení entit.
> * Volal se dotaz, který vrátí jiné typy.
> * Volal se dotaz na aktualizaci.
> * Prověření dotazů SQL
> * Vytvořila se vrstva abstrakce.
> * Seznámili jste se o automatické detekci změn
> * Dozvěděli jste se o EF Core zdrojového kódu a vývojářských plánech.
> * Zjistili jsme, jak pomocí dynamického jazyka LINQ zjednodušit kód

Tím se dokončí Tato série kurzů na používání Entity Framework Core v aplikaci ASP.NET Core MVC. Tato série se pracovala s novou databází. alternativou je provést zpětnou analýzu modelu z existující databáze.

> [!div class="nextstepaction"]
> [Kurz: EF Core s MVC, existující databází](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
