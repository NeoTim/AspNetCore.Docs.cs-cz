---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Pomocí migrace EF v aplikaci ASP.NET MVC a nasadit do Azure'
author: tdykstra
description: V tomto kurzu povolení migrace Code First a nasazení aplikace do cloudu v Azure.
ms.author: riande
ms.date: 01/16/2019
ms.topic: tutorial
ms.assetid: d4dfc435-bda6-4621-9762-9ba270f8de4e
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 131540113f5ac8ce9e15c8ab92b8dc7ee11de115
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444217"
---
# <a name="tutorial-use-ef-migrations-in-an-aspnet-mvc-app-and-deploy-to-azure"></a>Kurz: Pomocí migrace EF v aplikaci ASP.NET MVC a nasadit do Azure

Zatím ukázkovou webovou aplikaci Contoso University běžel místně v rámci služby IIS Express na vašem vývojovém počítači. Aplikace skutečný zpřístupnit ostatním uživatelům používat přes Internet, musíte nasadit na web poskytovatele hostitelských služeb. V tomto kurzu povolení migrace Code First a nasazení aplikace do cloudu v Azure:

- Povolení migrace Code First. Funkce migrace umožňuje změnit datový model a vaše změny nasadí do produkčního prostředí o aktualizaci schématu databáze, aniž by bylo nutné vyřadit a znovu vytvořit databázi.
- Nasazení do Azure. Tento krok je nepovinný; Zbývající kurzech můžete pokračovat bez nutnosti nasazení projektu.

Doporučujeme použít proces průběžné integrace se správou zdrojového kódu pro nasazení, ale v tomto kurzu ale nenajdete, tato témata. Další informace najdete v tématu [správy zdrojového kódu](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) a [kontinuální integrace](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) kapitoly [vytváření skutečných cloudových aplikací s využitím Azure](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení migrace Code First
> * Nasazení aplikace v Azure (volitelné)

## <a name="prerequisites"></a>Požadavky

- [Odolnost připojení a zachycení příkazů](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-code-first-migrations"></a>Povolení migrace Code First

Při vývoji nových aplikací, datového modelu mění často a pokaždé, když změny modelu, získá synchronizován s databází. Nakonfigurovali jste automaticky vyřadit a znovu vytvořit databázi pokaždé, když změníte datový model Entity Framework. Při přidání, odebrání, nebo změnit tříd entit nebo změnit váš `DbContext` třídy, při příštím spuštění aplikace automaticky odstraní existující databázi, vytvoří nový, který odpovídá modelu a nasazení se nasazuje s testovací data.

Tato metoda zachování databáze synchronizované s datovým modelem funguje dobře, dokud nasadit aplikaci do produkčního prostředí. Když aplikace běží v produkčním prostředí, je obvykle ukládá data, která chcete zachovat, a nechcete ztratit všechno, co pokaždé, když provedete změny, např. přidejte nový sloupec. [Migrace Code First](https://msdn.microsoft.com/data/jj591621) funkce tento problém řeší tím, že Code First pro aktualizaci schématu databáze místo vyřadit a znovu vytvořit databázi. V tomto kurzu budete nasazovat aplikaci a příprava, který vám umožní migraci.

1. Zakázat inicializátoru, který jste nastavili dříve okomentováním odpovídajícího nebo odstraněním `contexts` element, který jste přidali do souboru Web.config aplikace.

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.xml?highlight=2,6)]
2. Také v aplikaci *Web.config* změňte název databáze v připojovacím řetězci ContosoUniversity2.

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.xml?highlight=2)]

    Tato změna nastaví projekt tak, aby první migraci vytvoří novou databázi. Tato akce není povinná, ale zobrazí se vám později důvod, proč je vhodné.
3. Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.

1. Na `PM>` řádku zadejte následující příkazy:

    ```text
    enable-migrations
    add-migration InitialCreate
    ```

    `enable-migrations` Příkaz vytvoří *migrace* složky v projektu ContosoUniversity a umístí v této složce *Configuration.cs* souboru, který můžete upravit konfiguraci migrace.

    (Pokud předchozí krok, který nasměruje můžete změnit název databáze, migrace se najít existující databázi a automaticky provést `add-migration` příkazu. Který je v pořádku, že stačí znamená to, že před nasazením databáze nebude spuštění testu migrace kódu. Později při spuštění `update-database` příkaz nic se nestane, protože databáze již existuje.)

    Otevřít *ContosoUniversity\Migrations\Configuration.cs* souboru. Jako inicializátor třídy, která jste viděli dříve, `Configuration` třída zahrnuje `Seed` metoda.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

    Účelem [počáteční hodnoty](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) metodou je vám umožní vložit nebo aktualizovat data testu po Code First vytvoří nebo aktualizuje databázi. Metoda je volána při vytvoření databáze a pokaždé, když je schéma databáze se aktualizuje po změně datového modelu.

### <a name="set-up-the-seed-method"></a>Nastavit Seed – metoda

Když vyřadit a znovu vytvořit databázi u každé změny datového modelu, můžete použít třídu inicializátor `Seed` metoda vložit testovací data, protože po každé změně modelu databázi vyřadit a všechny testovací data se ztratí. Pomocí migrace Code First, testovací data se uchovávají po provedení změn databáze, takže včetně testovací data v [počáteční hodnoty](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) metodu není obvykle nutné. Ve skutečnosti nechcete, aby `Seed` metoda k vložení dat testu, pokud budete používat migrace nasazení databáze do produkčního prostředí, protože `Seed` metoda bude spuštěna v produkčním prostředí. V takovém případě má `Seed` metoda k vložení do databáze pouze data, která budete potřebovat v produkčním prostředí. Například může být vhodné zahrnout názvy skutečné oddělení v databázi `Department` tabulky, když se aplikace zpřístupní v produkčním prostředí.

Pro účely tohoto kurzu budete používat migrace pro nasazení, ale vaše `Seed` metoda bude přesto vložit testovací data pro snazší zjistit, jak funguje funkčnost aplikace, aniž byste museli ručně vložit velké množství dat.

1. Nahraďte obsah *Configuration.cs* souboru následujícím kódem, který načte testovací data do nové databáze.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    [Počáteční hodnoty](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) metoda přijímá jako vstupní parametr objekt kontextu databáze, a kód v metodě používá tento objekt k přidání nové entity do databáze. Pro každý typ entity kód vytvoří kolekci nové entity, se přidají do příslušné [DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.103).aspx) vlastnost a uloží změny do databáze. Není nutné volat [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) metoda po každé skupiny entit, jako se zde provádí, ale způsobem, který pomáhá při hledání příčiny problému, pokud dojde k výjimce, zatímco kód zapisuje do databáze.

    Některé příkazy, které vkládají data používají [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) metody k provedení operace "upsert". Protože `Seed` metoda se spustí pokaždé, když spustíte `update-database` příkazu, obvykle po každou migraci dat, nemůže právě vložit, protože se snažíte přidat řádky už budou tam po první migrace, který vytvoří databázi. Operace "upsert", zabraňuje chybám, které by mohlo dojít, pokud se pokusíte vložit řádek, který již existuje, ale ***přepíše*** všechny změny dat, který může mít provedené při testování aplikace. Pomocí testovacích dat v některých tabulkách nechcete provést: v některých případech při změně dat při testování chcete své změny zůstat po aktualizacích databáze. V takovém případě ji chcete vložit podmíněné operace: Vložit řádek jenom v případě, že ještě neexistuje. Seed – metoda používá oba přístupy.

    První parametr předána [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) metody určuje vlastnost, která má použít ke kontrole, jestli řádek již existuje. Pro testovací data studentů, které poskytujete `LastName` vlastnost lze použít pro tento účel, protože je jedinečný název každé poslední v seznamu:

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    Tento kód předpokládá, že poslední názvy musí být jedinečné. Pokud chcete ručně přidat studenta s duplicitním názvem poslední, získáte následující výjimku při příštím provedení migrace:

    **Posloupnost obsahuje více než jeden prvek.**

    Informace o tom, jak zpracování redundantních dat, jako je například dvou studentů s názvem "Alexander Carsonovy" najdete v tématu [financování a ladění Entity Framework (EF) databází](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) na blogu Ricka Andersona. Další informace o `AddOrUpdate` metodu, najdete v článku [postará metodou AddOrUpdate 4.3 EF](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/) na blogu Julie Lerman.

    Kód, který vytvoří `Enrollment` entity předpokládá, že máte `ID` hodnotu entit v `students` kolekcí, i když jste nenastavili tuto vlastnost v kódu, který vytvoří kolekci.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs?highlight=2)]

    Můžete použít `ID` vlastnost zde protože `ID` hodnotu při volání `SaveChanges` pro `students` kolekce. EF automaticky získá hodnotu primárního klíče, když se vloží entitu do databáze, a aktualizuje `ID` vlastnost entity v paměti.

    Kód, který přidá všechny `Enrollment` entita, která má `Enrollments` nepoužívá sadu entit `AddOrUpdate` metoda. Zkontroluje, jestli entita již existuje a vloží entitu, pokud neexistuje. Tento přístup se zachovat změny provedené na podnikové úrovni registraci pomocí aplikace uživatelského rozhraní. Kód prochází každý člen `Enrollment` [seznamu](https://msdn.microsoft.com/library/6sh2ey19.aspx) a pokud registrace nebyl nalezen v databázi, přidá přihlášení k databázi. Při prvním aktualizaci databáze, databáze se bude prázdný, tak přidá každý registrace.

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

2. Sestavte projekt.

### <a name="execute-the-first-migration"></a>Spuštění první migrace

Při spouštění `add-migration` příkazu migrace vygeneruje kód, který by vytvořila databázi úplně od začátku. Tento kód je také v *migrace* složku, v souboru s názvem  *&lt;časové razítko&gt;\_InitialCreate.cs*. `Up` Metodu `InitialCreate` třída vytvoří databázové tabulky, které odpovídají sady entit datového modelu a `Down` je metoda odstraní.

[!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Migrace volání `Up` metody k implementaci změn datových modelů pro migraci. Po zadání příkazu vrácení zpět aktualizace, volání migrace `Down` metody.

Toto je počáteční migraci, která byla vytvořena, když jste zadali `add-migration InitialCreate` příkazu. Parametr (`InitialCreate` v příkladu) se používá pro soubor pojmenujte a může být cokoliv, co chcete; obvykle zvolíte slovo nebo slovní spojení, které shrnuje, co se provádí v migraci. Můžete například pojmenovat pozdější migraci &quot;AddDepartmentTable&quot;.

Pokud jste vytvořili počáteční migraci databáze již existuje, vygeneruje kód pro vytvoření databáze, ale nemusí spustit, protože databáze již odpovídá datového modelu. Když nasadíte aplikaci do jiného prostředí, kde databáze neexistuje ale tento kód spustí k vytvoření databáze, proto je vhodné se nejdřív otestovat. To je důvod, proč jste změnili název databáze v připojovacím řetězci dříve&mdash;tak, aby migrace můžete vytvořit nový úplně od začátku.

1. V **Konzola správce balíčků** okno, zadejte následující příkaz:

    `update-database`

    `update-database` Příkaz spustí `Up` spustí metodu pro vytvoření databáze a pak ho `Seed` metoda k naplnění databáze. Stejný postup se spustí automaticky v produkčním prostředí po nasazení aplikace, jak uvidíte v následující části.
2. Použití **Průzkumníka serveru** ke kontrole databáze, stejně jako v prvním kurzu a spuštění aplikace pro ověření, že vše stále funguje stejně jako předtím.

## <a name="deploy-to-azure"></a>Nasazení do Azure

Zatím aplikace je spuštěné místně v rámci služby IIS Express na vašem vývojovém počítači. Chcete-li k dispozici pro ostatní použití přes Internet, musíte nasadit na web poskytovatele hostitelských služeb. V této části kurzu budete ji nasadit do Azure. Tato část je volitelná. Můžete to přeskočit a pokračovat v následujícím kurzu, nebo můžete si přizpůsobit pokyny v této části pro jiného poskytovatele hostitelských služeb podle vašeho výběru.

### <a name="use-code-first-migrations-to-deploy-the-database"></a>Pomocí migrace Code First pro nasazení databáze

Pokud chcete nasadit databázi, budete používat migrace Code First. Když vytvoříte profil publikování, který použijete ke konfiguraci nastavení pro nasazení ze sady Visual Studio, vyberte zaškrtávací políčko s názvem **aktualizace databáze**. Toto nastavení způsobí, že proces nasazení pro automatickou konfiguraci aplikace *Web.config* souboru na cílovém serveru tak, aby používala Code First `MigrateDatabaseToLatestVersion` inicializátor třídy.

Visual Studio nebude dělat nic s databází během procesu nasazení, zatímco kopíruje váš projekt na cílový server. Při spuštění aplikace nasazené a přistupuje k databázi po nasazení poprvé, Code First zkontroluje, jestli databáze se shoduje s datovým modelem. Pokud dojde k neshodě, Code First automaticky vytvoří databáze (pokud ještě neexistuje) nebo aktualizuje schéma databáze na nejnovější verzi (Pokud je databáze existuje, ale neodpovídá modelu). Pokud aplikace implementuje migrace `Seed` metody, metoda spustí po vytvoření databáze nebo schéma se aktualizuje.

Vaše migrace `Seed` metoda vloží testovací data. Pokud se nasazení do produkčního prostředí, je třeba změnit `Seed` metodu tak, že pouze vkládá nějaká data, která má být vložen do provozní databáze. Aktuální model dat můžete například mít skutečné kurzů, ale fiktivní studenty v databázi vývoj. Můžete napsat `Seed` má metoda načíst i při vývoji a poté komentář fiktivní studenty, před nasazením do produkčního prostředí. Nebo můžete napsat `Seed` má metoda načíst pouze kurzů a zadejte fiktivní studenty v testovací databázi ručně pomocí uživatelského rozhraní aplikace.

### <a name="get-an-azure-account"></a>Vytvoření účtu Azure

Budete potřebovat účet Azure. Pokud ho ještě nemáte, ale máte předplatné sady Visual Studio, můžete si [aktivovat výhody předplatného](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/
). V opačném případě můžete vytvořit bezplatného zkušebního účtu stačí pár minut. Podrobnosti najdete v tématu [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/).

### <a name="create-a-web-site-and-a-sql-database-in-azure"></a>Vytvoření webu a SQL database v Azure

Webové aplikace v Azure se spustí ve sdíleném hostování prostředí, což znamená, že běží na virtuálních počítačích (VM), které jsou sdíleny s jinými klienty Azure. Sdílené hostitelského prostředí je s nízkými náklady způsob, jak začít pracovat v cloudu. Později hodnota se zvyšuje webový provoz, aplikace můžete škálovat podle potřeby spouští na vyhrazených virtuálních počítačích. Další informace o cenové možnosti pro službu Azure App Service, [ceny služeb App Service](https://azure.microsoft.com/pricing/details/app-service/).

Budete nasazovat databázi do Azure SQL database. SQL database je služba relační databáze založené na cloudu, která je založená na technologiích systému SQL Server. Nástroje a aplikace, které fungují s SQL serverem se také fungují s SQL database.

1. V [portálu pro správu Azure](https://portal.azure.com), zvolte **vytvořit prostředek** v levé kartě a klikněte na tlačítko **zobrazit všechny** na **nový** podokna (nebo *okno*) zobrazíte všechny dostupné prostředky. Zvolte **Web App + SQL** v **webové** část **všechno, co** okno. Nakonec vyberte **vytvořit**.

    ![Vytvořit prostředek na webu Azure Portal](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/create-azure-resource.png)

   Formulář pro vytvoření nového **nový Web App + SQL** otevře prostředků.

2. Zadejte řetězec **název aplikace** pole použít jako jedinečná adresa URL pro vaši aplikaci. Úplná adresa URL bude obsahovat co zde zadáte plus výchozí doménu služby Azure App Services (. azurewebsites.net). Pokud **název aplikace** se už používá, Průvodce vás upozorní červeným *název aplikace není k dispozici* zprávy. Pokud **název aplikace** je k dispozici, zobrazí se zelené zaškrtnutí.

3. V **předplatné** vyberte předplatné Azure, ve kterém chcete **služby App Service** uložená.

4. V **skupiny prostředků** textového pole, vyberte skupinu prostředků nebo vytvořte novou. Toto nastavení určuje datové centrum, kterého webu se spustí v. Další informace o skupinách prostředků najdete v tématu [skupiny prostředků](/azure/azure-resource-manager/resource-group-overview#resource-groups).

5. Vytvořte nový **plán služby App Service** kliknutím *oddílu služby App Service*, **vytvořit nový**a vyplňte **plán služby App Service** (může mít stejný název jako App Service), **umístění**, a **cenová úroveň** (je bezplatnou možností).

6. Klikněte na tlačítko **SQL Database**a klikněte na tlačítko **vytvořit novou databázi** nebo vyberte existující databázi.

7. V **název** pole, zadejte název pro vaši databázi.
8. Klikněte na tlačítko **cílový Server** a potom vyberte **vytvořit nový server**. Dříve vytvořený server, je můžete vybrat tento server ze seznamu dostupných serverů.
9. Zvolte **cenová úroveň** zvolte *Free*. Pokud jsou potřeba další prostředky, je možné škálovat databáze v čase. Další informace o cenách pro Azure SQL najdete v tématu [cenami služby Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
10. Upravit [kolace](/sql/relational-databases/collations/collation-and-unicode-support) podle potřeby.
11. Zadejte správce **uživatelské jméno správce SQL** a **heslo správce SQL**.

   - Pokud jste vybrali **nová databáze SQL serveru**, zadejte nový název a heslo, které budete potřebovat později při přístupu k databázi.
   - Pokud jste vybrali serveru, který jste vytvořili dříve, zadejte přihlašovací údaje pro tento server.

12. Shromažďování telemetrie je možné povolit pro službu App Service pomocí služby Application Insights. S málo konfigurace Application Insights shromažďuje cenné událost, výjimky, závislosti, žádosti a informace o trasování. Další informace o Application Insights najdete v tématu [Azure Monitor](https://azure.microsoft.com/services/monitor/).
13. Klikněte na tlačítko **vytvořit** v dolní části k označení, že budete hotovi.

    Vrátí na portálu pro správu na stránce řídicího panelu a **oznámení** oblasti v horní části stránky se zobrazuje, že se vytváří web. Po chvíli (obvykle během méně než minutu) je oznámení, že nasazení proběhlo úspěšně. V navigačním panelu na levé straně se zobrazí nová služba App Service v **App Services** oddílu a nová databáze SQL se zobrazí v **databází SQL** oddílu.

### <a name="deploy-the-app-to-azure"></a>Nasazení aplikace do Azure

1. Ve Visual Studiu klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat** v místní nabídce.

2. Na **vyberte cíl publikování** zvolte **služby App Service** a potom **vybrat existující**a klikněte na tlačítko **publikovat**.

    ![Vyberte cílové stránky publikování](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/publish-select-existing-azure-app-service.png)

3. Pokud jste nepřidali dříve vašeho předplatného Azure v sadě Visual Studio, proveďte kroky na obrazovce. Tyto kroky aktivují sady Visual Studio pro připojení k vašemu předplatnému Azure tak, která seznam **App Services** bude obsahovat vaše webová stránka.

4. Na **služby App Service** stránky, vyberte **předplatné** jste přidali do služby App Service. V části **zobrazení**vyberte **skupiny prostředků**. Rozbalte skupinu zdrojů, kterou jste přidali služby App a pak vyberte aplikace služby. Zvolte **OK** Chcete-li publikovat aplikace.

5. **Výstup** okno zobrazuje, jaké akce nasazení byly provedeny a oznámí úspěšné dokončení nasazení.

6. Po úspěšném nasazení na adresu URL nasazené webové stránky automaticky otevře výchozí prohlížeč.

    ![Students_index_page_with_paging](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/cloud-app-browser.png)

    Vaše aplikace je nyní spuštěna v cloudu.

V tomto okamžiku *SchoolContext* databáze byla vytvořena v databázi Azure SQL, protože jste vybrali **provést první přenesení kódu (spuštění na spuštění aplikace)**. *Web.config* soubor v nasazený web byl změněn tak, aby [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) inicializátor prvním spuštění kódu čtení nebo zápis dat v databázi (která se stalo Pokud jste vybrali **studenty** kartu):

![Výňatek souboru Web.config](https://asp.net/media/4367421/mig.png)

Proces nasazení vytvoří taky nový připojovací řetězec *(SchoolContext\_DatabasePublish*) pro migrace Code First pro aktualizaci schématu databáze a synchronizace replik indexů databáze.

![Připojovací řetězec v souboru Web.config](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image26.png)

Můžete najít nasazenou verzi souboru Web.config na vašem počítači v *ContosoUniversity\obj\Release\Package\PackageTmp\Web.config*. Můžete přistupovat nasazeném *Web.config* vlastní soubor pomocí protokolu FTP. Pokyny najdete v tématu [nasazení webu ASP.NET pomocí sady Visual Studio: Nasazení aktualizace kódu](xref:web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update). Postupujte podle pokynů, které začínají znakem "Pokud chcete použít nástroj FTP, potřebujete tři věci: adresa URL protokolu FTP, uživatelské jméno a heslo."

> [!NOTE]
> Webové aplikace neimplementuje zabezpečení, takže každý, kdo najde adresu URL mohou změnit data. Pokyny k zabezpečení webového serveru naleznete v tématu [zabezpečení technologie ASP.NET MVC aplikace s databází členství, OAuth a SQL Azure nasadit](/aspnet/core/security/authorization/secure-data). Můžete zabránit ostatním uživatelům webu pomocí zastavení služby pomocí portálu pro správu Azure nebo **Server Explorer** v sadě Visual Studio.

![Položka nabídky služeb app ukončit](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/server-explorer-stop-app-service.png)

## <a name="advanced-migrations-scenarios"></a>Scénáře migrace pro pokročilé

Pokud se nasazení databáze spuštěním migrace automaticky, jak je znázorněno v tomto kurzu a provádíte nasazení na web, který běží na několika serverech, získáte víc serverů, které chcete spustit migrace ve stejnou dobu. Migrace jsou atomické, takže pokud dva servery, zkuste spustit stejný migrace, jeden proběhne úspěšně a druhé se nepodaří (za předpokladu, že operace nelze provádět dvakrát). V tomto scénáři Pokud chcete se vyhnout těmto problémům, můžete volat migrace ručně a nastavit váš vlastní kód tak, aby ho situace nastane pouze jednou. Další informace naleznete v tématu [systémem a skriptování při přenesení kódu](http://romiller.com/2012/02/09/running-scripting-migrations-from-code/) na blogu Rowan Miller a [Migrate.exe](/ef/ef6/modeling/code-first/migrations/migrate-exe) (pro provádění migrace z příkazového řádku).

Informace o dalších scénářů migrace najdete v tématu [migrace záznam dění na monitoru řady](https://blogs.msdn.com/b/adonet/archive/2014/03/12/migrations-screencast-series.aspx).

## <a name="code-first-initializers"></a>Kód první inicializátory

V části nasazení viděli [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx) používán inicializátoru. Kód nejprve také poskytuje jiné inicializátory, včetně [CreateDatabaseIfNotExists](https://msdn.microsoft.com/library/gg679221(v=vs.103).aspx) (výchozí), [DropCreateDatabaseIfModelChanges](https://msdn.microsoft.com/library/gg679604(v=VS.103).aspx) (který jste použili dříve) a [ DropCreateDatabaseAlways](https://msdn.microsoft.com/library/gg679506(v=VS.103).aspx). `DropCreateAlways` Inicializátor může být užitečné pro nastavení podmínky pro testování částí. Můžete je zapsat také vlastních inicializátory a může volat inicializátor explicitně Pokud nechcete čekat, dokud se čte z aplikace nebo zapisuje do databáze.

Další informace o inicializátory, naleznete v tématu [Principy inicializátory databáze v Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm) a kapitola 6 knihu [programování rozhraní Entity Framework: Code First](http://shop.oreilly.com/product/0636920022220.do) Julie Lerman a Rowan Miller.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework lze nalézt v [přístup k datům ASP.NET – doporučené zdroje informací](xref:whitepapers/aspnet-data-access-content-map).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolená migrace Code First
> * Nasazení aplikace v Azure (volitelné)

Přejděte k dalším článku se naučíte, jak vytvořit složitější datový model pro aplikaci ASP.NET MVC.
> [!div class="nextstepaction"]
> [Vytvoření složitějšího datového modelu](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)
