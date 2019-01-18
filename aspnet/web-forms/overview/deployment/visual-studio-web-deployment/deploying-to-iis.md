---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
title: 'Nasazení webu ASP.NET pomocí sady Visual Studio: Nasazení do testovacího prostředí | Dokumentace Microsoftu'
author: tdykstra
description: V této sérii kurzů se dozvíte, jak nasadit (publikovat) technologie ASP.NET webové aplikace do Azure App Service Web Apps nebo k poskytovateli hostingu třetích stran, podle usin...
ms.author: riande
ms.date: 01/16/2019
ms.assetid: 8bf2c4fb-4ee5-4841-bfc2-03462c1f7a7a
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
msc.type: authoredcontent
ms.openlocfilehash: d49dfad368ca4b81bb865103a99ec223a1cc66df
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396334"
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-to-test"></a>Nasazení webu ASP.NET pomocí sady Visual Studio: Nasazení do testovacího prostředí
====================
podle [Petr Dykstra](https://github.com/tdykstra)

> V této sérii kurzů se dozvíte, jak nasadit (publikovat) technologie ASP.NET webové aplikace do Azure App Service Web Apps nebo do jiného poskytovatele hostingu pomocí sady Visual Studio 2017. Informace o této sérii, naleznete v tématu [z prvního kurzu této série](introduction.md).

## <a name="overview"></a>Přehled

V tomto kurzu nasadíte webovou aplikaci ASP.NET do služby Internet Information Server (IIS) v místním počítači.

Při vývoji aplikace obecně spuštění a testování v sadě Visual Studio. Projekty webových aplikací v sadě Visual Studio 2017 použít jako vývojovému webovému serveru ve výchozím nastavení, služby IIS Express. Služba IIS Express chová podobně jako úplnou službu IIS, než vývojový Server Visual Studio (nazývaný též Cassini), která ve výchozím nastavení používá Visual Studio 2017. Ale ani vývojovému webovému serveru funguje úplně stejně jako služby IIS. V důsledku toho aplikace může spustit a testujte správně v sadě Visual Studio, ale nezdaří, pokud je nasazený do služby IIS.

Aplikaci můžete otestovat spolehlivě dvěma způsoby:

1. Nasazení aplikace do služby IIS na vašem vývojovém počítači stejným procesem, který budete později použít k nasazení do produkčního prostředí.

   Visual Studio pro použití služby IIS při spuštění projektu, ale proces nasazení, který by test můžete nakonfigurovat. Tato metoda ověří proces nasazení a spuštění vaší aplikace v rámci služby IIS.

2. Nasazení aplikace do testovacího prostředí podobné do produkčního prostředí. 
 
   Produkčním prostředí pro tyto kurzy jsou Web Apps ve službě Azure App Service. Ideální testovací prostředí je další webové aplikace vytvořené ve službě Azure. I když by se nastavit stejným způsobem jako produkční webovou aplikaci, by ho používáte jenom pro účely testování.

Možnost 2 je nejspolehlivější způsob, jak otestovat. Pokud použijete možnost 2, není nutné nutně používat možnost 1. Nicméně pokud nasazení provádíte do jiných výrobců poskytovatele, který je hostitelem možnost 2 nemusí být možné nebo může být náročné, takže v této sérii kurzů zobrazí obě metody. Pokyny pro možnost 2 je k dispozici v [nasazení do produkčního prostředí](deploying-to-production.md) kurzu.

Další informace o používání webových serverů v sadě Visual Studio najdete v tématu [webové servery v sadě Visual Studio pro webové projekty ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx).

Připomenutí: Pokud se zobrazí chybová zpráva nebo něco nefunguje tak, jak absolvovat kurz, nezapomeňte se podívat [stránka o řešení problémů](troubleshooting.md).

## <a name="download-the-contoso-university-starter-project"></a>Stáhněte si projekt starter vysoké školy Contoso

Stáhněte a nainstalujte Contoso University Visual Studio starter řešení a projektu. Toto řešení obsahuje dokončení kurzu. 

[Stáhnout počáteční projekt](http://go.microsoft.com/fwlink/p/?LinkId=282627)

## <a name="install-iis"></a>Instalace služby IIS

Chcete-li nasadit do služby IIS na vašem vývojovém počítači, zkontrolujte, že jsou nainstalované IIS a nasazení webu. Ve výchozím nastavení Visual Studio nainstaluje Web Deploy, ale služba IIS není součástí výchozí konfigurace Windows 10, Windows 8 nebo Windows 7. Pokud jste již nainstalovali IIS a výchozí fond aplikací je již nastavena na rozhraní .NET 4, přejděte k [v další části](#sqlexpress).

1. Je doporučeno, abyste používali [identitu pracovního webové platformy instalačního programu (procesu)](https://www.microsoft.com/web/downloads/platform.aspx) instalace IIS a nasazení webu. Doporučená konfigurace služby IIS, který obsahuje požadované součásti služby IIS a nasazení webu, v případě potřeby se nainstaluje identitu pracovního procesu.

   Pokud jste již nainstalovali IIS, Web Deploy nebo některý z jejich požadované součásti, identitu pracovního procesu nainstaluje jenom toho, co chybí.

   * Instalace IIS a nasazení webu pomocí instalačního programu webové platformy:
    
     ![Instalace služby IIS pomocí identitu pracovního procesu](deploying-to-iis/_static/image30.png)

     ![Nainstalujte nástroj nasazení webu pomocí identitu pracovního procesu](deploying-to-iis/_static/image31.png)

     Zobrazí zprávy, která udává, že se nainstaluje službu IIS 7. Odkaz pracuje v systému Windows 8, IIS 8 ale pro Windows 8 a novější, projděte si následující kroky, abyste měli jistotu, že je nainstalované ASP.NET 4.7:

   * Otevřít **ovládací panely** > **programy** > **programy a funkce** > **Windows zapnout nebo vypnout funkce** .

   * Rozbalte **Internetová informační služba**, **webové služby**, a **funkce pro vývoj aplikací**.
   
   * Ujistěte se, že **ASP.NET 4.7** zaškrtnuto.

     ![Vyberte ASP.NET 4.7](deploying-to-iis/_static/image1a.png)

   * Ujistěte se, že **webové služby** a **konzolu pro správu IIS** zaškrtnuto. Tím se nainstaluje službu IIS a Správce služby IIS.
    
     ![Vyberte webové služby](deploying-to-iis/_static/image24.png)    
  
   * Vyberte **OK**. Zobrazí se pole zprávy dialogové okno oznamující, že probíhá instalace.

Po instalaci služby IIS, spusťte **Správce služby IIS** abyste měli jistotu, že rozhraní .NET Framework verze 4 je přiřazeno výchozí fond aplikací.

1. Stiskněte WINDOWS + R otevřete **spustit** dialogové okno.

   (V systému Windows 8 nebo novější, zadejte "spustit" na **Start** stránky. Ve Windows 7, vyberte **spustit** z **Start** nabídky. Pokud **spustit** není v **Start** nabídky, klikněte pravým tlačítkem na hlavním panelu, vyberte **vlastnosti**, vyberte **nabídky Start** kartu, vyberte **Vlastní**a vyberte **spusťte příkaz**.)

2. Zadejte "inetmgr" a vyberte **OK**.

3. V **připojení** podokně rozbalte uzel serveru a vyberte **fondy aplikací**. V **fondy aplikací** podokně Pokud **DefaultAppPool** je přiřazen k rozhraní .NET framework verze 4 jako na následujícím obrázku, přejděte k další části.

   ![Inetmgr_showing_4.0_app_pools](deploying-to-iis/_static/image5a.png)

4. Pokud se zobrazí pouze dva fondy aplikací a obě jsou nastaveny na rozhraní .NET Framework 2.0, nainstalujte technologii ASP.NET 4 ve službě IIS.

   Pro Windows 8 nebo novější, naleznete v předchozí části pokyny a ujistěte se, že technologie ASP.NET 4.7 je nainstalována nebo naleznete v tématu [instalace technologie ASP.NET 4.5 na Windows 8 a Windows Server 2012](https://support.microsoft.com/kb/2736284). Pro Windows 7, otevřete okno příkazového řádku kliknutím pravým tlačítkem myši **příkazového řádku** v Windows **Start** nabídky a vyberete **spustit jako správce**. Spustit [aspnet\_regiis.exe](https://msdn.microsoft.com/library/k6h9cz8h.aspx) instalace technologie ASP.NET 4 ve službě IIS pomocí následujících příkazů. (V 32bitových systémech, nahraďte "Framework64" s "Rozhraní".)

   [!code-console[Main](deploying-to-iis/samples/sample1.cmd)]

   Tento příkaz vytvoří novou aplikaci, fondů pro rozhraní .NET Framework 4, ale výchozí fond aplikací zůstane nastavena jako 2.0. Nasazujete aplikaci, že cíle .NET 4 do tohoto fondu aplikací to změnit fond aplikací na rozhraní .NET 4.

5. Pokud jste zavřeli **Správce služby IIS**, znovu jej spusťte, rozbalte uzel serveru a vyberte **fondy aplikací**.

6. V **fondy aplikací** vyberte **DefaultAppPool**. V **akce** vyberte **základní nastavení**.

   ![Inetmgr_selecting_Basic_Settings_for_app_pool](deploying-to-iis/_static/image25.png)

7. V **upravit fond aplikací** dialogovém okně Změnit **verze .NET CLR** k **.NET CLR v4.0.30319**. Vyberte **OK**.

   ![Selecting_.NET_4_for_DefaultAppPool](deploying-to-iis/_static/image6a.png)

Nyní jste připraveni publikovat webovou aplikaci do služby IIS. Nejprve vytvořte však databáze pro testování.

<a id="sqlexpress"></a>

## <a name="install-sql-server-express"></a>Nainstalujte SQL Server Express

Nespouští fungují ve službě IIS, takže testovací prostředí musí mít nainstalovaný SQL Server Express LocalDB. Pokud používáte Visual Studio 2010 SQL Server Express, je již nainstalována ve výchozím nastavení. Pokud používáte Visual Studio 2012 nebo novější, nainstalujte SQL Server Express.

Nainstalovat systém SQL Server Express, stáhněte a nainstalujte ji z [Download Center: Microsoft SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express). 

Na první stránce Centrum instalace SQL serveru, vyberte **samostatná instalace nového serveru SQL Server nebo přidání funkcí do existující instalace** a postupujte podle pokynů přijímat výchozí volby. V Průvodci instalací přijměte výchozí nastavení. Další informace o možnostech instalace najdete v tématu [instalace SQL serveru pomocí Průvodce instalací (nastavení)](https://msdn.microsoft.com/library/ms143219.aspx).

## <a name="create-sql-server-express-databases-for-the-test-environment"></a>Vytvoření databáze SQL Server Express pro testovací prostředí

Aplikace Contoso University má dvě databáze: 

1. Databáze členství 
2. Databáze aplikace 

Tyto databáze můžete nasadit na dvě samostatné databáze nebo do jediné databáze. Je kombinovat díky databáze spojení mezi nimi jednodušší. 

Pokud nasazení provádíte do poskytovatele hostitelských služeb třetích stran, váš plán hostování může také získáte z důvodu kombinovat. Zprostředkovatel například může účtovat další možnosti pro více databází nebo nemusí povolit i více než jednu databázi.

V tomto kurzu nasadíte dvě databáze v testovacím prostředí a jednu databázi přípravného a produkčního prostředí.

Z **zobrazení** nabídky v sadě Visual Studio, vyberte **Průzkumníka serveru** (**Průzkumník databáze** v aplikaci Visual Web Developer). Klikněte pravým tlačítkem na **datová připojení** a vyberte **vytvořit novou databázi SQL serveru**.

![Selecting_Create_New_SQL_Server_Database](deploying-to-iis/_static/image8.png)

V **vytvořit novou databázi SQL serveru** dialogového okna zadejte ". \SQLExpress" v **název serveru** pole a "aspnet-ContosoUniversity" v **nového názvu databázového** pole. Vyberte **OK**.

![Create aspnet-ContosoUniversity](deploying-to-iis/_static/image9.png)

Postupujte stejným způsobem vytvoření nové databáze SQL serveru Express školy s názvem `ContosoUniversity`.

**Průzkumník serveru** ukazuje dvě nové databáze.

![Nové databáze v Průzkumníku serveru](deploying-to-iis/_static/image10.png)

## <a name="create-a-grant-script-for-the-new-databases"></a>Vytvoření nové databáze skriptu udělení

Při spuštění aplikace ve službě IIS na vašem vývojovém počítači aplikace používá výchozí fond aplikací přihlašovací údaje pro přístup k databázi. Ve výchozím nastavení, ale fond aplikací nemá oprávnění k otevření databáze. To znamená, že je potřeba spustit skript k udělení oprávnění. V této části vytvoříte skript a spustit jej později, abyste měli jistotu, že aplikace bude moci otevřít databáze při spuštění ve službě IIS.

V textovém editoru, zkopírujte následující příkazy SQL do nového souboru a uložte ho jako *Grant.sql*. 

[!code-sql[Main](deploying-to-iis/samples/sample2.sql)]

V sadě Visual Studio otevřete řešení vysoké školy Contoso. Klikněte pravým tlačítkem řešení (ne z projektů) a vyberte **přidat**. Vyberte **existující položku**, přejděte do *Grant.sql*a otevřete ho.

> [!NOTE]
> Tento skript je navržená fungují s SQL Server Express 2012 nebo novějším a s nastavením služby IIS v systému Windows 10, Windows 8 nebo Windows 7, jako jsou uvedeny v tomto kurzu. Pokud používáte jinou verzi SQL serveru nebo Windows, nebo pokud jste nastavili IIS počítače odlišně, může být vyžadováno změny tohoto skriptu. Další informace o skripty systému SQL Server najdete v tématu [SQL Server Books Online](https://go.microsoft.com/fwlink/?LinkId=132511).

> [!NOTE] 
> **Poznámka k zabezpečení** poskytuje tento skript `db_owner` oprávnění pro uživatele, který přistupuje k databázi v době běhu, což je budete mít v provozním prostředí. V některých případech můžete chtít určit jako uživatel, který má celé databáze schéma aktualizovat oprávnění pouze pro nasazení a zadejte jiný uživatel, který má oprávnění pouze ke čtení a zápisu dat běhu. Další informace najdete v tématu [kontroly automatické změn souboru Web.config pro migrace Code First](#reviewingmigrations) dále v tomto kurzu.

<a id="publish"></a>

## <a name="run-the-grant-script-in-the-application-database"></a>Spusťte skript udělení aplikační databáze

Můžete nakonfigurovat profil publikování pro spuštění skriptu udělení v databázi členství během nasazování vzhledem k tomu používá toto nasazení databáze [dbDacFx](https://docs.microsoft.com/iis/publish/using-web-deploy/dbdacfx-provider-for-incremental-database-publishing) zprostředkovatele. Během migrace Code First nasazení, která je, jak byste nasazovali aplikační databáze nelze spouštět skripty. To znamená, že budete muset ručně spustit skript před nasazením v databázi aplikace.

1. V sadě Visual Studio, otevřete *Grant.sql* soubor, který jste vytvořili dříve.

2. Vyberte **Connect** (Připojit). 

    ![Tlačítko pro připojení](deploying-to-iis/_static/image11.png)

3. V **připojit k serveru** dialogového okna zadejte *. \SQLExpress* jako **název serveru**. Vyberte **Connect** (Připojit).

4. V rozevíracím seznamu databáze vyberte **ContosoUniversity**. Vyberte **Provést**. 

   ![](deploying-to-iis/_static/image12.png)

Identita fondu aplikací výchozí teď má dostatečná oprávnění v databázi aplikace pro migrace Code First k vytvoření databázových tabulek při spuštění aplikace.

## <a name="publish-to-iis"></a>Publikování do služby IIS

Existuje několik způsobů, jimiž můžete nasadit do služby IIS pomocí sady Visual Studio a nasazení webu:

* Pomocí sady Visual Studio publikování jedním kliknutím.
* Publikování z příkazového řádku.
* Vytvořit balíček pro nasazení a nainstalovat pomocí Správce služby IIS. Balíček má soubor ZIP se všemi soubory a metadata jsou požadovaná k instalaci lokality ve službě IIS.
* Vytvořit balíček pro nasazení a nainstalovat pomocí příkazového řádku.

Proces, který jste provedli v předchozích kurzech k nastavení sady Visual Studio k automatizaci úloh nasazení se vztahuje na všechny tyto metody. V těchto kurzech použijete první dvě metody. Informace o používání balíčků pro nasazení najdete v tématu [nasazení webové aplikace pomocí vytvoření a instalace balíčku pro nasazení webu](https://go.microsoft.com/fwlink/p/?LinkId=282413#package) v obsahu mapy webové nasazení pro Visual Studio a ASP.NET.

Před publikováním, ujistěte se, že používáte Visual Studio v režimu správce. Pokud nevidíte **(správce)** v záhlaví okna zavřete sadu Visual Studio. V systému Windows 8 (nebo novější) **Start** stránky nebo Windows 7 **Start** nabídku, klikněte pravým tlačítkem na ikonu sady Visual Studio a vyberte **spustit jako správce**. Režim správce je pouze vyžadované pro publikování publikujete do služby IIS v místním počítači.

### <a name="create-the-publish-profile"></a>Vytvořit profil publikování

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **ContosoUniversity** projektu (ne **ContosoUniversity.DAL** projektu). Vyberte **Publikovat**. **Publikovat** se zobrazí stránka.

2. Vyberte **nový profil**. **Vyberte cíl publikování** zobrazí se dialogové okno.

3. Vyberte **služby IIS, FTP atd**. Vyberte **vytvořit profil**. **Publikovat** průvodce se zobrazí.

   ![Karta Web Průvodce profil publikování](deploying-to-iis/_static/image26.png)

4. Z **metodu publikování** rozevírací nabídky vyberte **Webdeploy**.

5. Pro **Server**, zadejte *localhost*.

6. Pro **název lokality**, zadejte *výchozí webový server/ContosoUniversity*.

7. Pro **cílovou adresu URL**, zadejte *http://localhost/ContosoUniversity*.

   **Cílovou adresu URL** nastavení není povinné. Po dokončení nasazení aplikace Visual Studio automaticky otevře výchozí prohlížeč a tuto adresu URL. Pokud nechcete, aby prohlížeč, aby po nasazení automaticky otevře, ponechte toto pole prázdné.

8. Vyberte **ověřit připojení** k ověření, že je nastavení správné a může připojit ke službě IIS na místním počítači.

   Zelená značka zaškrtnutí ověří, zda je připojení úspěšné.

   ![Publikování webových kartě připojení Průvodce](deploying-to-iis/_static/image27.png)

9. Vyberte **Další** pro přechod **nastavení** kartu.

10. **Konfigurace** rozevíracího seznamu určuje konfiguraci sestavení a nasadit. Nechejte nastavenou výchozí hodnotu **vydání**. Nebude nasazení sestavení ladicí verze v tomto kurzu.

11. Rozbalte **možnosti publikování souboru**. Vyberte **vyloučit soubory z aplikace\_složka dat**.

    V testovacím prostředí, přistupuje k aplikace, které jste vytvořili v místní SQL Server Express instance, nikoli soubory .mdf do databáze *aplikace\_Data* složky.

12. Nechte **Precompile během publikování** a **odebrat další soubory v cílovém umístění** zaškrtávací políčka prázdná.

    ![Na kartě Nastavení možností publikování souboru](deploying-to-iis/_static/image15a.png)

    Předkompilace je možnost, která je užitečné hlavně pro velké weby. Můžete ji zkrátit dobu spouštění při prvním vyžádání stránky po publikování na webu.

    Není nutné odebrat další soubory, jelikož Toto je první nasazení a nebudou všechny soubory v cílové složce ještě.

    > [!NOTE] 
    > Pokud vyberete **odebrat další soubory v cílovém umístění** pro následné nasazení do stejné lokality, ujistěte se, pozor, abyste použili funkci ve verzi preview, takže uvidíte předem soubory, které se odstraní, před nasazením. Chování je očekávané, nasazení webu se odstraní soubory na cílovém serveru, který jste odstranili ve vašem projektu. Struktura celou složku v části zdrojová a cílová složka je však porovnání; a v některých scénářích nasazení webu může odstranit soubory, které nechcete odstranit.
    > 
    > Například pokud máte webovou aplikaci do podsložky na serveru při nasazení projektu do kořenové složky, podsložky se odstraní. Může mít jeden projekt pro hlavní server v doméně contoso.com a jiného projektu pro blog na contoso.com/blog. Blog aplikace je do podsložky. Pokud vyberete **odebrat další soubory v cílovém umístění** při nasazení hlavní web blogu aplikace se odstraní.
    > 
    > Další příklad, vaše aplikace\_složka dat může být odstraněný neočekávaně. Některé databáze, jako jsou SQL Server Compact ukládat soubory databáze v aplikaci\_složku Data. Po počátečním nasazení, které nechcete zachovat kopírování souborů databáze v následné nasazení, takže můžete vybrat **vyloučit aplikace\_Data** na kartě Balení/publikování webu. Poté si, že pokud máte **odebrat další soubory v cílovém umístění** vybrané soubory databáze a aplikace\_samotné složce Data se odstraní při příštím publikování.

### <a name="configure-deployment-for-the-membership-database"></a>Konfigurace nasazení pro databázi členství

Následující postup se vztahuje na **objekt DefaultConnection** databáze v dialogových oken **databází** oddílu.

1. V **vzdálený připojovací řetězec** zadejte následující připojovací řetězec, který odkazuje na novou databázi serveru SQL Server Express členství.

   [!code-console[Main](deploying-to-iis/samples/sample3.cmd)]

   Proces nasazení umístí tento připojovací řetězec v nasazeném souboru Web.config, protože **použít tento připojovací řetězec za běhu** zaškrtnuto.

    Můžete také získat připojovací řetězec z **Průzkumníka serveru**. V **Průzkumníka serveru**, rozbalte **datová připojení** a vyberte  **&lt;machinename&gt;\sqlexpress.aspnet-ContosoUniversity** databáze, pak z **vlastnosti** okno kopírování **připojovací řetězec** hodnotu. Že připojovací řetězec bude mít jeden další nastavení, které můžete odstranit: `Pooling=False`.

2. Vyberte **aktualizace databáze**.

   To způsobí, že schéma databáze byly vytvořeny v cílové databázi během nasazení. V dalších krocích, zadejte další skripty, které je potřeba spustit: z nich se má udělit přístup k databázi na výchozí fond aplikací a nasazení dat. z nich.

3. Vyberte **konfigurovat aktualizace databáze**.
 
4. V **konfigurace aktualizací databáze** dialogu **přidat skript SQL**. Přejděte *Grant.sql* skript, který jste předtím uložili ve složce řešení.

5. Opakujte postup pro přidání *aspnet-data-dev.sql* skriptu.

   ![Konfigurace aktualizací databáze pro databázi členství](deploying-to-iis/_static/image16.png)

6. Vyberte **Zavřít**.

### <a name="configure-deployment-for-the-application-database"></a>Ke konfiguraci nasazení pro databázi aplikace

Když Visual Studio zjistí Entity Framework `DbContext` třídy, je vytvořena položka ve **databází** oddíl, který má **spustit migrace Code First** zaškrtávací políčko místo  **Aktualizace databáze** zaškrtávací políčko. Pro účely tohoto kurzu budete používat toto políčko k určení nasazení migrace Code First.

V některých případech je může použít `DbContext` databáze, ale chcete použít poskytovatele dbDacFx místo migrace pro nasazení databáze. V takovém případě naleznete v tématu [jak nasadit bez migrace Code First databázi?](https://msdn.microsoft.com/library/ee942158.aspx#deploy_code_first_without_migrations) v nejčastějších Dotazech technologie ASP.NET webové nasazení na webu MSDN.

Následující postup se vztahuje **SchoolContext** databáze v dialogových oken **databáze** části.

1. V **vzdálený připojovací řetězec** zadejte následující připojovací řetězec, který odkazuje na nové databáze aplikace SQL Server Express.

   [!code-console[Main](deploying-to-iis/samples/sample4.cmd)]

   Proces nasazení umístí tento připojovací řetězec v nasazeném souboru Web.config, protože **použít tento připojovací řetězec za běhu** zaškrtnuto.

   Můžete také získat připojovací řetězec databáze aplikace z **Průzkumníka serveru** stejně, jako jste získali připojovací řetězec databáze členství.

2. Vyberte **spustit migrace Code First (spuštěno při spuštění aplikace)**.

   Tato možnost způsobí, že proces nasazení ke konfiguraci nasazeném souboru Web.config pro zadání `MigrateDatabaseToLatestVersion` inicializátor. Databáze tento inicializátor automaticky aktualizuje na nejnovější verzi, když aplikace poprvé po nasazení získá přístup k databázi.

### <a name="configure-publish-profile-transforms"></a>Konfigurace transformace profil publikování

1. Vyberte **Zavřít**. Vyberte **Ano** při zobrazí se výzva, pokud chcete uložit změny.

2. V **Průzkumníka řešení**, rozbalte **vlastnosti**, rozbalte **PublishProfiles**.

3. Klikněte pravým tlačítkem na *CustomProfile.pubxml* a přejmenujte jej *Test.pubxml*.

4. Klikněte pravým tlačítkem na *Test.pubxml*. Vyberte **přidat konfigurační transformaci**.

   ![Přidat konfigurační transformaci nabídky](deploying-to-iis/_static/image17.png)

   Visual Studio vytvoří *Web.Test.config* transformační soubor a otevře jej.

5. V *Web.Test.config* transformovat soubor, vložte následující kód bezprostředně po otevření konfigurační značky.

   [!code-xml[Main](deploying-to-iis/samples/sample5.xml)]

    Pokud používáte testovací profil publikování, tato transformace nastaví prostředí ukazatel na "Test". "(Testovací)" v lokalitě nasazené zobrazí po nadpis H1 "University společnosti Contoso".

6. Soubor uložte a zavřete.

7. Klikněte pravým tlačítkem myši *Web.Test.config* a vyberte možnost **náhled transformace** abyste měli jistotu, že transformace kódování vytvoří očekávané změny.

    **Náhled souboru Web.config** okno zobrazuje výsledek použití i *Web.Release.config* transformuje a *Web.Test.config* transformace.

### <a name="preview-the-deployment-updates"></a>Verze Preview aktualizace nasazení

1. Otevřít **Publikovat Web** průvodce znovu (klikněte pravým tlačítkem na projekt ContosoUniversity, vyberte **publikovat**, pak **ve verzi Preview**).

2. V **ve verzi Preview** dialogu **spustit Náhled** zobrazíte seznam souborů, které budou zkopírovány. 

   ![Náhled publikování](deploying-to-iis/_static/image29.png)

   Můžete také vybrat **databáze ve verzi Preview** odkaz zobrazíte skripty, které se spustí v databázi členství. (Žádné skripty se spouštějí pro nasazení migrace Code First, takže není nutné nic ve verzi preview pro databázi aplikace.)

3. Vyberte **Publikovat**.

   Pokud aplikace Visual Studio není v režimu správce, může získat chybovou zprávu oprávnění. V takovém případě ukončit sadu Visual Studio, otevřete v režimu správce a zkuste publikování znovu.

   Pokud je v režimu správce, Visual Studio **výstup** okno sestavy úspěšné sestavení a publikování.

   ![Output_window_publish_Test](deploying-to-iis/_static/image20.png)

   Pokud jste zadali adresu URL v **cílovou adresu URL** pole na profil publikování **připojení** kartě prohlížeče se automaticky otevře Contoso University domovskou stránku v počítači spuštěný ve službě IIS.

## <a name="test-in-the-test-environment"></a>Testování v testovacím prostředí

Všimněte si, že prostředí indikátor zobrazuje "(testovací)" místo "(vývoj)", který ukazuje, že *Web.config* transformace pro ukazatel prostředí bylo úspěšné.

Spustit **Instruktoři** stránku a ověřte, že Code First naplnila databázi s instruktorem daty. Když vyberete tuto stránku, může trvat několik minut, než načíst, protože Code First vytvoří databázi a pak spustí `Seed` metody. (To neprovedli, když jste byli na domovskou stránku, protože při pokusu o přístup k databázi ještě nebyl v aplikaci.)

Vyberte **studenty** kartu ověřte, že má nasazené databáze se žádní studenti.

Vyberte **přidat studenty** z **studenty** nabídky. Přidat student a potom zobrazit nového objektu student do **studenty** stránky. Ověří, že můžete úspěšně zapisovat do databáze.

Z **kurzy** nabídce vyberte možnost **aktualizace kredity**. **Aktualizace kredity** stránka vyžaduje oprávnění správce, proto **přihlásit** zobrazí se stránka. Zadejte přihlašovací údaje účtu správce, které jste vytvořili dříve ("admin" a "devpwd"). **Aktualizace kredity** zobrazí se stránka. Ověří, že účet správce, který jste vytvořili v předchozím kurzu byla správně nasazena do testovacího prostředí.

Ověřte, že *ELMAH* složka existuje v *c:\inetpub\wwwroot\ContosoUniversity* složka se zástupný soubor v ní.

<a id="reviewingmigrations"></a>

## <a name="review-the-automatic-webconfig-changes-for-code-first-migrations"></a>Zkontrolujte automatických změn souboru Web.config pro migrace Code First

Otevřít *Web.config* souboru v nasazení aplikace na *C:\inetpub\wwwroot\ContosoUniversity* a je vidět, kde proces nasazení automaticky nakonfigurované migrace Code First pro Aktualizujte databázi na nejnovější verzi.

![](deploying-to-iis/_static/image21.png)

Proces nasazení vytvoří taky nový připojovací řetězec pro migrace Code First používat výhradně pro aktualizaci schématu databáze:

![Database_Publish připojovací řetězec](deploying-to-iis/_static/image22.png)

Tento další připojovací řetězec můžete zadat jeden uživatelský účet pro aktualizace schématu databáze a jiný uživatelský účet pro přístup k datům aplikace. Například můžete přiřadit **db\_vlastníka** úlohu migrace Code First a **db\_datareader** s **db\_datawriter nesmí být**role pro aplikaci. Toto je běžný vzor v obrany, který brání potenciálně škodlivý kód v aplikaci změny schématu databáze. (Například k tomu může dojít v úspěšném útoku prostřednictvím injektáže SQL.) Tyto kurzy nepoužívejte tento model. Pro implementaci tohoto vzoru ve vašem scénáři, proveďte tyto kroky:

1. V **Publikovat Web** průvodce v části **nastavení** kartu, zadejte připojovací řetězec, který určuje uživatel s oprávněními aktualizovat schéma celé databáze. Zrušte **použít tento připojovací řetězec za běhu** zaškrtávací políčko. V nasazeném souboru Web.config, toto řešení `DatabasePublish` připojovací řetězec.

2. Vytvoření transformace souboru Web.config pro připojovací řetězec, který chcete, aby aplikace pro použití v době běhu.

## <a name="summary"></a>Souhrn

Právě jste nasadili aplikaci do služby IIS na počítači pro vývoj a testování existuje.

![Domovské stránky v testu](deploying-to-iis/_static/image23.png)

Ověří, že proces nasazení zkopíroval obsah aplikace na správné umístění (s výjimkou souborů, které jste nechtěli nasazení) a také nasazení webu služby IIS správně nakonfigurován během nasazení. V dalším kurzu spustíte jeden další test, který vyhledá úlohu nasazení, které nebylo dosud provedeno: nastavení oprávnění pro složky *jilm ah* složky.

## <a name="more-information"></a>Další informace

Informace o spuštění služby IIS nebo IIS Express v sadě Visual Studio naleznete na následujících odkazech:

- [Přehled služby IIS Express](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) na webu IIS.net.
- [Úvod do služby IIS Express](https://weblogs.asp.net/scottgu/archive/2010/06/28/introducing-iis-express.aspx) v blogu Scotta Guthrie.
- [Webové servery v sadě Visual Studio pro projekty ASP.NET Web](https://msdn.microsoft.com/library/58wxa9w5.aspx).
- [Hlavní rozdíly mezi služby IIS a serveru ASP.NET Development Server](../../older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md) na webu ASP.NET.

Informace o problémech, které mohou nastat při spuštění aplikace v úrovni medium trust, najdete v části [hostování aplikace ASP.NET ve střední důvěryhodnosti](http://www.4guysfromrolla.com/articles/100307-1.aspx) na čtyři Guys Rolla webu.

> [!div class="step-by-step"]
> [Předchozí](project-properties.md)
> [další](setting-folder-permissions.md)
