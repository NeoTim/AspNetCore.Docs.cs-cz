---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Odolnost proti chybám a příkaz zachycení připojení pomocí EF v aplikaci ASP.NET MVC'
author: tdykstra
description: V tomto kurzu dozvíte, jak používat připojení odolnost proti chybám a příkaz zachycení. Jsou dvě důležité funkce Entity Framework 6.
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: c89d809f-6c65-4425-a3fa-c9f6e8ac89f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 276266f8ae9df38529d44742ebe6ac0dc8e79727
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889818"
---
# <a name="tutorial-use-connection-resiliency-and-command-interception-with-entity-framework-in-an-aspnet-mvc-app"></a>Kurz: Použití připojení odolnost proti chybám a příkaz zachycení s Entity Framework v aplikaci ASP.NET MVC

Zatím aplikace je spuštěné místně v rámci služby IIS Express na vašem vývojovém počítači. Aplikace skutečný zpřístupnit ostatním uživatelům používat přes Internet, je zapotřebí nasadit na web poskytovatele hostitelských služeb a je zapotřebí nasadit databázi k databázovému serveru.

V tomto kurzu dozvíte, jak používat připojení odolnost proti chybám a příkaz zachycení. Jsou dvě důležité funkce Entity Framework 6, které jsou obzvláště cenná tehdy, když provádíte nasazení do prostředí cloudu: odolnost připojení (automatické opakování pro přechodné chyby) a příkaz zachycení (catch všechny dotazy SQL odeslané do databáze Pokud chcete protokolovat nebo je změnit).

Tento kurz pro zachycení odolnost proti chybám a příkaz připojení je volitelné. Pokud jste tento kurz přeskočit, bude mít několik drobné úpravy má být provedeno v dalších kurzech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolit odolnost připojení
> * Povolit zachycení příkazů
> * Nová konfigurace testu

## <a name="prerequisites"></a>Požadavky

* [Řazení, filtrování a stránkování](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-connection-resiliency"></a>Povolit odolnost připojení

Když nasadíte aplikaci do Windows Azure, budete nasazovat databázi k Windows Azure SQL Database je Cloudová databázová služba. Připojení přechodné chyby jsou obvykle častější při připojování k Cloudová databázová služba než kdy webový server a databáze serveru byli přímo připojení společně ve stejném datovém centru. I v případě, že webový server cloud a Cloudová databázová služba hostované ve stejném datovém centru, existují další síťová připojení mezi nimi, které mohou mít problémy, jako jsou nástroje pro vyrovnávání zatížení.

Také cloudovou službu se obvykle sdílí jiní uživatelé, což znamená, že jeho reakce mohou být ovlivněny. A váš přístup k databázi může podléhat omezení šířky pásma. Omezení znamená, že databázová služba vyvolá výjimky při pokusu o přístup k jeho další často než je povolené v smlouva o úrovni služeb (SLA).

Mnoho nebo většinu problémy s připojením při přístupech cloudové služby jsou přechodné, to znamená, že tyto možnosti vyřeší sami v krátké době. Proto při operaci databáze a získat typ chyby, je nejčastěji přechodné, můžete zkusit operaci zopakovat po krátkém čekání a operace může být úspěšné. Pokud zpracování přechodných chyb tak, že automaticky zkusíte znovu, můžete zadat mnohem lepší prostředí pro vaše uživatele viditelnosti většina z nich zákazníka. Funkce odolnost připojení v Entity Framework 6 automatizuje, že proces opakování neúspěšných dotazů SQL.

Funkce odolnosti proti chybám připojení musí být správně nakonfigurovaný pro konkrétní databázi služby:

- Je třeba vědět, jaké výjimky jsou může být přechodná. Chcete opakovat chyb způsobených k dočasné ztrátě v připojení k síti, nejsou chyby způsobené chyby programu, například.
- Má odpovídající množství času mezi opakovanými pokusy o neúspěšné operaci čekání. Můžete počkat déle mezi opakovanými pokusy pro dávkové zpracování než pro online webovou stránku, kde uživatel je čekání na odpověď.
- Má to chcete zkusit znovu příslušným počtem niž se. Můžete chtít opakovat je víckrát v dávkové zpracování, která byste to udělali v aplikaci online.

Můžete nakonfigurovat tato nastavení ručně pro všechny databáze prostředí podporovaná službou zprostředkovatele Entity Framework, ale už jsou nakonfigurované výchozí hodnoty, které obvykle fungují dobře pro online aplikaci, která používá Windows Azure SQL Database, a Toto jsou nastavení, která bude implementace pro aplikaci Contoso University.

Vše, co musíte udělat, aby povolit odolnost připojení je vytvoření třídy v sestavení, která je odvozena z [DbConfiguration](https://msdn.microsoft.com/data/jj680699.aspx) třídy a v dané třídě nastavení databáze SQL *strategie provádění*, což je v EF je jiný termín pro *zásady opakování*.

1. Ve složce DAL, přidejte soubor třídy s názvem *SchoolConfiguration.cs*.
2. Nahraďte kód šablony následujícím kódem:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

    Entity Framework automaticky spustí kód, který najde ve třídě, která je odvozena z `DbConfiguration`. Můžete použít `DbConfiguration` třídě, aby provedl úlohy konfigurace v kódu, který by jinak provedete *Web.config* souboru. Další informace najdete v tématu [EntityFramework konfigurace založená na kódu](https://msdn.microsoft.com/data/jj680699).
3. V *StudentController.cs*, přidejte `using` příkaz pro `System.Data.Entity.Infrastructure`.

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]
4. Změnit všechny `catch` blokuje této catch `DataException` výjimky tak, aby se zachytit `RetryLimitExceededException` výjimky místo. Příklad:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=1)]

    Jste používali `DataException` se pokouší zjistit chyby, které může být přechodná, aby bylo možné poskytnout přátelskou zprávou "zkuste to znovu". Ale teď, když jste zapnuli zásady opakování, může být přechodná pouze chyby se již mít se snažily neúspěšně několikrát, a skutečné vrátila výjimku budou zabalené v `RetryLimitExceededException` výjimky.

Další informace najdete v tématu [odolnost Entity Framework připojení / logice opakování](https://msdn.microsoft.com/data/dn456835).

## <a name="enable-command-interception"></a>Povolit zachycení příkazů

Teď, když jste zapnuli zásady opakování, jak můžete otestovat ověření, že funguje podle očekávání? Není tak snadné vynutit o přechodnou chybu, která se provede, zejména pokud používáte místně a by být obzvláště složité je integrovat skutečné přechodné chyby testu jednotek automatizované. K otestování funkce odolnosti proti chybám připojení, potřebujete způsob, jak zachytit dotazy, které odešle Entity Framework pro SQL Server a nahraďte odpovědi serveru SQL Server, který je obvykle přechodný typ výjimky.

Kvůli implementaci osvědčený postup pro cloudové aplikace můžete také použít dotaz zachycení: [protokolování latence a úspěch nebo neúspěch všechna volání externích služeb](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log) například databázové služby. Poskytuje EF6 [vyhrazené protokolování rozhraní API](https://msdn.microsoft.com/data/dn469464) , který lze provádět jednodušší protokolování, ale v této části kurzu se dozvíte o použití rozhraní Entity Framework [zachycení funkce](https://msdn.microsoft.com/data/dn469464) přímo, i pro protokolování a pro simulaci přechodné chyby.

### <a name="create-a-logging-interface-and-class"></a>Vytvoření protokolování rozhraní a třídy

A [osvědčený postup pro protokolování](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log) , je to udělat pomocí rozhraní namísto pevně kódováno pomocí volání System.Diagnostics.Trace nebo třída protokolování. Který usnadňuje mechanismu protokolování později změnit, pokud byste někdy potřebovali udělat. Takže v této části vytvoříte rozhraní protokolování a třídu pro implementaci ní/p >

1. Vytvoření složky v projektu a pojmenujte ho *protokolování*.
2. V *protokolování* složce vytvořte soubor třídy *ILogger.cs*a nahraďte kód šablony následujícím kódem:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    Rozhraní obsahuje tři úrovně trasování a určete relativní důležitost protokoly a je určen k zadání informací o latenci pro volání externích služeb, například databázové dotazy. Protokolování metody mají přetížení, které umožňují předat výjimku. Je to tak, aby se informace o výjimkách včetně zásobníku trasování a vnitřní výjimky spolehlivě protokoluje třídou, která implementuje rozhraní, aniž byste museli spoléhat na, který provádí v jednotlivých voláních metody protokolování v celé aplikaci.

    Metody TraceApi umožňují vám umožní sledovat latenci každého volání externí službě, jako je SQL Database.
3. V *protokolování* složce vytvořte soubor třídy *Logger.cs*a nahraďte kód šablony následujícím kódem:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    Implementace použije k provedení trasování System.Diagnostics. Toto je integrovaná funkce .NET, takže se dá snadno generovat a používat informace o trasování. Existuje mnoho "naslouchacích procesů" pomocí trasování System.Diagnostics můžete použít k zápisu do souborů, například protokoly nebo zapsat do úložiště objektů blob v Azure. Některé z možností a odkazy na další zdroje informací pro další informace najdete v tématu v [Poradce při potížích s weby Azure v sadě Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio). V tomto kurzu budete díval jenom na protokoly v sadě Visual Studio **výstup** okna.

    V produkční aplikace může být vhodné vzít v úvahu trasování balíčky než System.Diagnostics a ILogger interface usnadňuje relativně přepnout na mechanismu různých trasování, pokud se rozhodnete k tomu.

### <a name="create-interceptor-classes"></a>Vytvoření třídy zachycování

Dále vytvoříte třídy, které rozhraní Entity Framework volat pokaždé, když se chce odešle dotaz do databáze, jeden pro simulaci přechodné chyby a jeden provedete protokolování. Tyto třídy zachycování musí být odvozen z `DbCommandInterceptor` třídy. V nich můžete zapsat přepsání metody, které jsou automaticky volána, když je dotaz se pokračovalo. V těchto metodách můžete prozkoumat nebo protokolu dotaz odeslaný do databáze, a můžete změnit dotaz před odesláním do databáze nebo vracely něco k Entity Frameworku sami bez i předá dotaz do databáze.

1. Chcete-li vytvořit třídu zachycování, která se budou protokolovat každého dotazu SQL, který je odeslán do databáze, vytvořte soubor třídy *SchoolInterceptorLogging.cs* v *DAL* složky a nahraďte kód šablony s Následující kód:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

    Tento kód pro úspěšné dotazy nebo příkazy, zapíše protokol informací s informace o latenci. Pro výjimky vytvoří protokol chyb.
2. Chcete-li vytvořit třídu zachycování, která vygeneruje fiktivní přechodné chyby, když zadáte "Vyvolat" v **hledání** pole, vytvořte soubor třídy *SchoolInterceptorTransientErrors.cs* v *DAL* složky a nahraďte kód šablony následujícím kódem:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

    Tento kód jenom přepsání `ReaderExecuting` metodu, která je volána pro dotazy vracející více řádků dat. Pokud chcete zkontrolovat odolnost připojení pro jiné typy dotazů, může také přepsat `NonQueryExecuting` a `ScalarExecuting` metody, jako protokolování zachycování nepodporuje.

    Při spuštění stránky studentů a zadejte hledaný řetězec "Vyvolat", tento kód vytvoří fiktivní výjimka SQL Database pro číslo chyby 20, typ ví, že je nejčastěji přechodné. Jsou různé počty chyb aktuálně rozpoznána jako přechodné 64, 233, 10053, 10054, 10060, 10928, 10929, 40197, 40501 a 40613, ale ty se mohou změnit v nových verzích služby SQL Database.

    Vrátí kód výjimky k Entity Frameworku namísto spuštění dotazu a předá zpět výsledků. Přechodné výjimky se vrátí čtyřikrát, a potom vrátí kód na běžný postup předáním dotaz do databáze.

    Vzhledem k tomu, že všechno, co se zaznamená, budete moct zjistit, že Entity Framework pokusí o provedení dotazu čtyřikrát před nakonec úspěšná, a jediným rozdílem v aplikaci je, že trvá déle k vykreslení stránky s výsledky dotazu.

    Počet pokusů, které se bude opakovat Entity Framework je možné konfigurovat; kód určuje čtyřikrát, protože to je výchozí hodnota pro zásady spouštění SQL Database. Pokud změníte zásady spouštění, máte také změnit zde kód, který určuje, kolikrát se generují přechodné chyby. Můžete také změnit kód tak, aby se vyvolá rozhraní Entity Framework generovat další výjimky `RetryLimitExceededException` výjimky.

    Hodnotu, kterou zadáte do vyhledávacího pole bude v `command.Parameters[0]` a `command.Parameters[1]` (jeden se používá pro křestní jméno a jeden pro příjmení). Když je nalezena hodnota "% Throw %", "Vyvolat" se nahrazuje v tyto parametry "služby" tak, aby některé studenty nalezen, který se vrátil.

    Toto je jenom pohodlný způsob, jak testovat odolnost proti chybám připojení na základě změny některých vstup do uživatelského rozhraní aplikace. Můžete také napsat kód, který generuje přechodné chyby pro všechny dotazy nebo aktualizace, jak je popsáno dále v komentářích o *DbInterception.Add* metody.
3. V *Global.asax*, přidejte následující `using` příkazy:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]
4. Přidat zvýrazněné řádky a `Application_Start` metody:

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=7-8)]

    Tyto řádky kódu se, co způsobí, že váš kód zachycování má být spuštěn při Entity Framework odesílá dotazy do databáze. Všimněte si, že vzhledem k tomu, že jste vytvořili samostatné zachycování třídy pro přechodná chyba simulace a protokolování, můžete nezávisle na sobě povolit a zakázat.

    Můžete přidat pomocí sběrače `DbInterception.Add` metodu kdekoli v kódu; nemusí být v `Application_Start` metoda. Další možností je vložit tento kód ve třídě DbConfiguration, který jste vytvořili dříve ke konfiguraci zásady spouštění.

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=6-7)]

    Všude, kde vložte tento kód, dejte pozor, ke spuštění `DbInterception.Add` pro stejný zachycování více než jednou, ale zobrazí se další zachycování instance. Například pokud přidáte zachycování protokolování dvakrát, zobrazí se vám dva protokolů pro každý dotaz SQL.

    Sběrače jsou provedeny v pořadí registrace (pořadí, ve kterém `DbInterception.Add` volání metody). V závislosti na tom, co děláte v Sběrač může být důležité pořadí. Například zachycování může změnit pomocí příkazu SQL, který získá v `CommandText` vlastnost. Pokud ji změnit pomocí příkazu SQL, se zobrazí další zachycování změněné příkaz SQL, nikoli původní příkaz SQL.

    Jste napsali kód přechodná chyba simulace způsobem, který umožňuje způsobit přechodné chyby tak, že zadáte jinou hodnotu v uživatelském rozhraní. Jako alternativu můžete napsat kód zachycování vždy generovat posloupnost přechodným výjimkám bez kontroly pro konkrétní parametr hodnoty. Sběrač, pak můžete přidat pouze v případě, že chcete vygenerovat přechodné chyby. Pokud to uděláte, ale nepřidávejte zachycování až po dokončení inicializace databází. Jinými slovy proveďte nejméně jednu databázi operace, jako například dotaz na jednom ze sady entit, před zahájením generování přechodné chyby. Entity Framework provede několik dotazů během inicializace databází a nebudou provedeny v transakci, takže chyby při inicializaci by mohlo způsobit kontext k získání do nekonzistentního stavu.

## <a name="test-the-new-configuration"></a>Nová konfigurace testu

1. Stisknutím klávesy **F5** spusťte aplikaci v režimu ladění, a klikněte **studenty** kartu.
2. Podívejte se na Visual Studio **výstup** okno a zobrazit výstup trasování. Budete muset posunout nahoru za některé chyby JavaScriptu získat protokoly vytvořené vaší protokolovacího nástroje.

    Všimněte si, že vidíte skutečné SQL dotazy odeslané do databáze. Uvidíte některé počáteční dotazy a příkazy, které rozhraní Entity Framework nemá Pokud chcete začít, kontrola verze databáze a tabulky historie migrace (získáte informace o migraci v dalším kurzu). Zobrazit dotaz pro stránkování, chcete-li zjistit, kolik studenty existují, a nakonec se zobrazí dotaz, který získá data studentů.

    ![Protokolování pro normální dotaz](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)
3. V **studenty** stránky, jako řetězec hledání zadejte "Vyvolat" a klikněte na tlačítko **hledání**.

    ![Vyvolat hledaný řetězec](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

    Můžete si všimnout, že v prohlížeči vypadá, že přestane reagovat na několik sekund, zatímco rozhraní Entity Framework se opakovaně pokouší o dotazu několikrát. Prvním opakováním se stane velmi rychle, pak čekání před zvýšení před každou další zkuste to znovu. Tento proces už čekání před voláním opakováními *exponenciálního omezení rychlosti*.

    Pokud na stránce se zobrazí, zobrazení pro studenty, kteří mají "s" v jejich názvy, podívejte se na v okně Výstup a zobrazí stejný dotaz došlo k pokusu o pětkrát, nejprve čtyřikrát vrácením přechodné výjimky. U každé přechodné chyby, který píšete při generování přechodná chyba v protokolu uvidíte `SchoolInterceptorTransientErrors` třídy ("Returning přechodná chyba pro příkaz...") a zobrazí se v protokolu zapisují při `SchoolInterceptorLogging` získá výjimku.

    ![Uložit výstup protokolování zobrazující opakování](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

    Vzhledem k tomu, že zadáte hledaný řetězec, je s parametry dotazu, který vrací data studenta:

    [!code-sql[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.sql)]

    Neprotokolují hodnoty parametrů, ale můžete to udělat. Pokud chcete zobrazit hodnoty parametrů, můžete napsat kód protokolování k získání hodnot parametrů z `Parameters` vlastnost `DbCommand` objekt, který vám mít metody zachycování.

    Všimněte si, že tento test nelze opakovat, pokud aplikaci zastavte a restartujte ji. Pokud chcete mít možnost Testovat odolnost připojení více než jednou v jedné spuštění aplikace, můžete napsat kód Vynulovat čítač Chyba v `SchoolInterceptorTransientErrors`.
4. Uvidíte rozdíl strategie provádění (zásady opakování) vystaví, komentářů `SetExecutionStrategy` řádku v *SchoolConfiguration.cs*, znovu spusťte stránce studenty v režimu ladění a znovu vyhledat "Vyvolání výjimky".

    Tentokrát ladicí program se zastaví při první výjimky generované okamžitě při pokusu o provedení dotazu poprvé.

    ![Fiktivní výjimky](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)
5. Zrušením komentáře u *SetExecutionStrategy* řádku v *SchoolConfiguration.cs*.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework lze nalézt v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odolnost připojení
> * Zachycení povolených příkazů
> * Otestovat novou konfiguraci

Přejděte k dalším článku se dozvíte o Code First Migration a nasazení Azure.
> [!div class="nextstepaction"]
> [Code First Migration a nasazení v Azure](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)
