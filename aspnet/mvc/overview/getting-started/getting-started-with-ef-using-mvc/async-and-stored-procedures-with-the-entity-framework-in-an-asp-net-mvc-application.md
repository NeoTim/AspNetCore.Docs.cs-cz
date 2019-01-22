---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Použití async a uložené procedury s EF v aplikaci ASP.NET MVC'
description: V tomto kurzu můžete zjistit, jak implementovat asynchronní programovací model a zjistěte, jak pomocí uložených procedur.
author: tdykstra
ms.author: riande
ms.date: 01/18/2019
ms.topic: tutorial
ms.assetid: 27d110fc-d1b7-4628-a763-26f1e6087549
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 0896664174bc2fee65b73ecf256d994f2abacc0a
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444360"
---
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a>Kurz: Použití async a uložené procedury s EF v aplikaci ASP.NET MVC

V předchozích kurzech jste zjistili, jak číst a aktualizovat data pomocí synchronní programovací model. V tomto kurzu můžete zjistit, jak implementovat asynchronní programovací model. Asynchronní kód může pomoct lépe provést, protože umožňuje lepší využití serverových prostředků aplikace.

V tomto kurzu můžete také zjistit, jak pomocí uložené procedury pro vložení, aktualizace nebo odstranění operace s entitou.

Nakonec znovu nasadit aplikaci do Azure, spolu s všechny změny databáze, které jsme implementovali od první chvíle, kdy jste nasadili.

Následující ilustrace znázorňují některé stránky, které budete pracovat.

![Oddělení stránky](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Vytvoření oddělení](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Další informace o asynchronní kód
> * Vytvoření kontroleru oddělení
> * Použití uložených procedur
> * Nasazení do Azure

## <a name="prerequisites"></a>Požadavky

* [Aktualizace souvisejících dat](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a>Proč používat asynchronní kód

Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán. Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna. Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny. Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků. V důsledku toho asynchronního kódu umožňuje serveru prostředků efektivněji používat, a abyste zvládli větší provoz bez zpoždění je povoleno na serveru.

V dřívějších verzích rozhraní .NET byl složitý, psaní a testování asynchronní kód chyby náchylné k chybám a těžko ladění. V rozhraní .NET 4.5 je mnohem jednodušší, pokud nemáte důvod, proč nebylo by měly obecně psaní asynchronního kódu psaní, testování a ladění asynchronního kódu. Asynchronní kód zavést malé množství režie, ale v situacích s nízkým provozem výkonu přístupů je zanedbatelný, při vysoké návštěvnosti situacích, je možné zlepšení výkonu podstatné.

Další informace o asynchronním programování naleznete v tématu [podpory asynchronních operací pomocí rozhraní .NET 4.5 k zabránění blokování volání](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).

## <a name="create-department-controller"></a>Vytvoření kontroleru oddělení

Vytvoření kontroleru oddělení, stejně jako jste to udělali starších řadičů, tentokrát vyberte **použít asynchronní akce kontroleru** zaškrtávací políčko.

Následujícím hlavním funkcím zobrazit, co byl přidán do synchronní kód `Index` provést asynchronní metody:

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

Čtyři změny byly použity k povolení databázového dotazu Entity Framework spustit asynchronně:

- Metoda je označena třídou `async` – klíčové slovo, které instruuje kompilátor generovat zpětná volání pro části tělo metody a automaticky vytvářet `Task<ActionResult>` vráceného objektu.
- Návratový typ se změnil z `ActionResult` k `Task<ActionResult>`. `Task<T>` Typ představuje probíhající práci s výsledkem typu `T`.
- `await` – Klíčové slovo byla použita k volání webové služby. Když kompilátor narazí toto klíčové slovo, na pozadí rozdělí metodu na dva oddíly. První část končí operace, která se spustí asynchronně. Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.
- Asynchronní verze `ToList` byla volána metoda rozšíření.

Proč je `departments.ToList` příkaz upravit, ale ne `departments = db.Departments` příkaz? Důvodem je, že pouze příkazy, které způsobují dotazy nebo příkazy, které se odesílají do databáze se provedl asynchronně. `departments = db.Departments` Příkaz nastaví dotazu, ale není dotaz provést, dokud `ToList` metoda je volána. Proto pouze `ToList` metoda provádí asynchronně.

V `Details` metoda a `HttpGet` `Edit` a `Delete` metody, `Find` metoda je ten, který způsobí, že dotaz k odeslání do databáze, tak, aby se metoda, která se provede asynchronně:

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

V `Create`, `HttpPost Edit`, a `DeleteConfirmed` metody, je `SaveChanges` volání metody, která způsobí, že příkaz má být proveden, nikoli příkazy, jako `db.Departments.Add(department)` entity což vedlo jenom v paměti, která má být upraven.

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

Otevřít *Views\Department\Index.cshtml*a nahraďte kód šablony následujícím kódem:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

Tento kód změní název z indexu na oddělení, přesune jméno správce napravo a poskytuje úplné jméno správce.

V části Vytvoření, odstranění, podrobností a upravte zobrazení, změnit titulek `InstructorID` pole "Administrator" stejným způsobem jako v zobrazeních kurzu změníte pole název oddělení "Oddělení".

Zobrazení v vytvořit a upravit pomocí následujícího kódu:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

V zobrazení Delete a podrobnosti pomocí následujícího kódu:

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

Spusťte aplikaci a klikněte na tlačítko **oddělení** kartu.

Všechno, co funguje stejně jako v ostatních řadičů, ale v tomto kontroleru všechny dotazy SQL jsou asynchronně.

Je potřeba vědět při použití asynchronní programování s rozhraním Entity Framework pár věcí:

- Asynchronní kód není bezpečné pro vlákna. Jinými slovy jinými slovy, nedoporučujeme provádět více operací paralelně pomocí stejné instance kontextu.
- Pokud chcete využít výhod výkony těží z asynchronní kód, ujistěte se, že všechny knihovny balíčky, které používáte (například stránkování), asynchronní použijte i v případě volají všechny Entity Framework metody, které způsobují dotazů k odeslání do databáze.

## <a name="use-stored-procedures"></a>Použití uložených procedur

Některé vývojáře a specializující raději pomocí uložených procedur pro přístup k databázi. V dřívějších verzích sady Entity Framework můžete načíst data pomocí uložené procedury pomocí [spuštění neupraveného dotazu SQL](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), ale nemůže dát pokyn EF pouze pomocí uložených procedur pro operace update. V EF 6 je snadno konfigurovatelné Code First pomocí uložených procedur.

1. V *DAL\SchoolContext.cs*, přidejte zvýrazněný kód, který `OnModelCreating` metody.

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    Tento kód nastaví Entity Frameworku na použití uložené procedury pro vložení, aktualizovat a odstraňovat operace `Department` entity.
2. V konzole pro správu balíčků zadejte následující příkaz:

    `add-migration DepartmentSP`

    Otevřít *migrace\&lt; časové razítko&gt;\_DepartmentSP.cs* zobrazíte kód `Up` metodu, která vytvoří Insert, Update a Delete uložené procedury:

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. V konzole pro správu balíčků zadejte následující příkaz:

     `update-database`
4. Spusťte aplikaci v režimu ladění, klikněte na tlačítko **oddělení** kartu a potom klikněte na tlačítko **vytvořit nový**.
5. Zadejte data pro jiného oddělení a klikněte na **vytvořit**.

6. V sadě Visual Studio, podívejte se na protokoly v **výstup** okno a zobrazit, uložené procedury byla použita k vložit nový řádek oddělení.

     ![Oddělení vložit SP](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Kód nejprve vytvoří výchozí uložené procedury názvy. Pokud používáte existující databázi, můžete potřebovat k přizpůsobení názvy uložených postupů Chcete-li použít uložené procedury v databázi již definována. Informace o tom, jak to udělat, najdete v části [Entity Framework Code první Insert/Update/Delete uložené procedury](https://msdn.microsoft.com/data/dn468673).

Pokud chcete přizpůsobit, co vygeneruje uložených procedur, můžete upravit automaticky generovaný kód pro přenesení `Up` metodu, která vytvoří uloženou proceduru. Díky tomu vaše změny se projeví vždy, když, migrace je spuštěný a uplatní se na vaši produkční databázi když migrace spustí automaticky v produkčním prostředí po nasazení.

Pokud chcete změnit stávající úložnou proceduru, který byl vytvořen v předchozí migrace, můžete pomocí příkazu Add-migrace generovat prázdné migrace a ručně napsat kód, který volá [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) – metoda .

## <a name="deploy-to-azure"></a>Nasazení do Azure

Tato část vyžaduje, abyste dokončili nepovinný **nasazení aplikace do Azure** tématu [Migration a nasazení](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) této série kurzů. Pokud jste měli migrace chyby, které jste vyřešili tak, že odstraníte databáze v projektu pro místní, tuto část přeskočte.

1. Ve Visual Studiu klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat** v místní nabídce.
2. Klikněte na tlačítko **publikovat**.

    Visual Studio nasadí aplikaci do Azure a aplikace se otevře ve výchozím prohlížeči, běží v Azure.
3. Otestování aplikace ověří, že funguje.

    Při prvním spuštění stránky, který přistupuje k databázi, Entity Framework provozovat všechny migrace `Up` metod požadovaných k aktuální s aktuálním datovým modelem databáze. Teď můžete použít všechny webové stránky, které jste přidali od posledního, které jste nasadili, včetně oddělení stránek, které jste přidali v tomto kurzu.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework najdete v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Dozvěděli jste se o asynchronní kód
> * Vytvoří kontroler oddělení
> * Používat uložené procedury
> * Nasazení do Azure

Přejděte k dalším článku se naučíte, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
> [!div class="nextstepaction"]
> [Ošetření souběžnosti](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
