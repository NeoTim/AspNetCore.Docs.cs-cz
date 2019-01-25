---
uid: whitepapers/aspnet-web-deployment-content-map
title: Nasazení webu ASP.NET – doporučené zdroje informací | Dokumentace Microsoftu
author: rick-anderson
description: Toto téma obsahuje odkazy na dokumentaci (publikovat) technologie ASP.NET zdroje informací o tom, jak nasadit webové aplikace do služby IIS pomocí Visual Studio 2010, Visual Web De...
ms.author: riande
ms.date: 03/14/2014
ms.assetid: 58b583cd-c4ab-47a3-8527-8c92c298c91f
msc.legacyurl: /whitepapers/aspnet-web-deployment-content-map
msc.type: content
ms.openlocfilehash: 6df0c9d2f38ad1d39abd62787c600ef80da8e8e0
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836218"
---
<a name="aspnet-web-deployment---recommended-resources"></a>Nasazení webu ASP.NET – doporučené zdroje informací
====================
> Toto téma obsahuje odkazy na dokumentaci (publikovat) technologie ASP.NET zdroje informací o tom, jak nasadit webové aplikace do služby IIS s využitím sady Visual Studio 2010, Visual Web Developer 2010 a novější verze.
> 
> Pokud znáte skvělé blogový příspěvek, [stackoverflow](http://stackoverflow.com) vlákna nebo odkaz, který může být užitečné, [nám pošlete e-mailu](mailto:aspnetue@microsoft.com?subject=Deployment%20Content%20Map) s odkazem.
> 
> > [!NOTE] 
> > 
> > Mnohé z těchto prostředků popisují nasazení funkce, které jsou k dispozici pouze v případě, že instalujete nejnovější verzi [aktualizace Visual Studio Web publikovat](https://go.microsoft.com/fwlink/?LinkID=208120). Některé funkce jsou k dispozici pouze v sadě Visual Studio 2012 nebo Visual Studio 2013.


Toto téma obsahuje následující oddíly:

- [Možnosti nasazení pro webové projekty](#understanding)
- [Vyhledání poskytovatele pro aplikaci ASP.NET hostingu](#findinghosting)
- [Nasazení webové aplikace ze sady Visual Studio](#fromvs)
- [Nasazení webové aplikace pomocí vytvoření a instalace balíčku pro nasazení webu](#package)
- [Nasazení webové aplikace s využitím procesu kontinuální integrace (CI)](#ci)
- [Chcete-li změnit nastavení v cílovém souboru Web.config nebo app.config souboru během nasazování pomocí transformace Web.config](#transforms)
- [Chcete-li změnit nastavení v cílové webové aplikace během nasazení pomocí nasazení webu parametrů](#webdeployparms)
- [Během nasazení zajišťuje, že aplikace je offline](#appoffline)
- [Nasazení do databáze nebo změny do databáze jako součást nasazení webových aplikací](#databasewithweb)
- [Nasazení databáze odděleně od nasazení webových aplikací](#databaseseparate)
- [Nasazení webové aplikace, který používá aplikace ASP.NET službami, jako je členství a profilace](#aspnetmembership)
- [Předkompilace pro nasazení](#precompiling)
- [Nasazení webové aplikace sítě intranet](#intranet)
- [Automatizaci nejčastějších úloh nasazení, které nejsou automatizované úprav](#automating)
- [Konfigurace webových serverů, aby vývojáři můžete nasadit webové aplikace k nim pomocí nasazení webu](#configuringservers)
- [Konfigurace serverů pro poskytovatele hostingu](#hostingprovider)
- [Řešení potíží s chybami nasazení](#troubleshooting)
- [Získat pomoc s otázkou konkrétní nasazení](#gettinghelp)
- [Další prostředky](#additional)


<a id="understanding"></a>


## <a name="understanding-deployment-options-for-web-projects"></a>Možnosti nasazení pro webové projekty

- [Přehled nasazení pro Visual Studio a ASP.NET Web](https://msdn.microsoft.com/library/dd394698.aspx) (MSDN).
- [Nasazení webu Windows Azure](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Popisuje možnosti a odkazy na prostředky pro nasazení webových projektů na Windows weby Azure, včetně [průběžné doručování](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) (automatizované z [správy zdrojového kódu](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control.md)) a také pomocí sady Visual Studio.
- [Visual Studio 2012 – vylepšení webového publikování](../visual-studio/overview/2012/visual-studio-2012-web-publishing-improvements.md) (Video Scotta Hanselmana,).
- [Přehled Post pro nasazení webu ve VS 2010](http://vishaljoshi.blogspot.com/2009/09/overview-post-for-web-deployment-in-vs.html) (blog Vishal Joshi). Starší blogový příspěvek, ale některé z prostředků Visual Studio 2010 obsahuje odkazy na informace, které je stále relevantní pro sadu Visual Studio 2012.


<a id="findinghosting"></a>


## <a name="finding-hosting-providers-for-an-aspnet-application"></a>Vyhledání poskytovatele pro aplikaci ASP.NET hostingu

- [ASP.NET Hosting](https://asp.net/hosting)


<a id="fromvs"></a>


## <a name="deploying-a-web-application-from-visual-studio"></a>Nasazení webové aplikace ze sady Visual Studio

- [Nasazení webu Windows Azure](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Popisuje možnosti a poskytuje odkazy na zdroje informací pro webové projekty nasazení do modelu weby Windows Azure. Obsahuje části o nasazení ze sady Visual Studio.
- [Nasazení webu ASP.NET pomocí sady Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). 12 – část série kurzů, ukazuje, jak nasadit webové aplikace s databází SQL serveru. Pro databázi nasazení používá zprostředkovatele dbDacFx i migrace Entity Framework Code First. Obsahuje také informace o [transformace souboru Web.config](../web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations.md), [nasazení jednotlivých souborů](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update.md#specificfiles), [příkazového řádku nasazení](../web-forms/overview/deployment/visual-studio-web-deployment/command-line-deployment.md), a [postupy Přizpůsobení webu aplikace Visual Studio kanálu publikování úpravou souborů .pubxml](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files.md). Platí pro všechny webové projekty ASP.NET, včetně webové formuláře, MVC a webového rozhraní API).
- [Postupy: Nasazení webového projektu pomocí publikování jedním kliknutím v sadě Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) (referenční informace pro Průvodce publikování webu v aplikaci Visual Studio.)
- [Nasazení webové aplikace ASP.NET s SQL serverem Compact pomocí sady Visual Studio](../web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12.md). Toto je starší verze **nasazení webu ASP.NET pomocí sady Visual Studio** uvedených v horní části tohoto oddílu. Užitečné hlavně teď informace o tom, jak nasadit SQL Server Compact databází a postupu při migraci z SQL Server Compact na plnou verzi systému SQL Server.
- [.NET vícevrstvé aplikace pomocí úložiště tabulky, fronty a objekty BLOB](https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36) (webu Microsoft Azure). 5 část třídílné série kurzů, ukazuje, jak vytvořit projekt MVC a nasadit ho do cloudové služby Windows Azure.


<a id="package"></a>
## <a name="deploying-a-web-application-by-creating-and-installing-a-web-deployment-package"></a>Nasazení webové aplikace pomocí vytvoření a instalace balíčku pro nasazení webu

- [Postupy: Vytvoření balíčku pro nasazení webu v sadě Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx) (MSDN).
- [Postupy: Instalace balíčku pro nasazení pomocí souboru deploy.cmd vytvořené pomocí sady Visual Studio](https://msdn.microsoft.com/library/ff356104.aspx) (MSDN).
- [Pomocí balíčku pro nasazení webu nasadit do služby IIS na poli dev a na hostitele třetích stran](http://sedodream.com/2011/11/08/UsingAWebDeployPackageToDeployToIISOnTheDevBoxAndToAThirdPartyHost.aspx) (blog Sayed Hashimi). Tom, jak pomocí Správce služby IIS nainstalovat balíček pro nasazení ve službě IIS na místním počítači a na hostitelského společnosti, která podporuje správce služby IIS pro vzdálenou správu.
- [Vytváření webové nasazení balíčku ze sady Visual Studio 2010](https://www.iis.net/learn/publish/using-web-deploy/building-a-web-deploy-package-from-visual-studio-2010) (IIS.NET webové stránky). Obsahuje pokyny pro vytvoření balíčku příkazového řádku a instalaci.
- [Balíček po publikování kamkoli](http://sedodream.com/2012/03/14/PackageWebUpdatedAndVideoBelow.aspx) (blog Sayed Hashimi). Zavádí balíčku NuGet, který automatizuje proces transformace souboru Web.config pro více cílového prostředí tak, aby jeden balíček můžete nasadit několik serverů. Viz také [PackageWeb video](https://www.youtube.com/watch?v=-LvUJFI8CzM) podle Sayed Hashimi.

Další informace najdete v článku následující části.


<a id="ci"></a>


## <a name="deploying-a-web-application-using-a-continuous-integration-ci-process"></a>Nasazení webové aplikace s využitím procesu kontinuální integrace (CI)

- [Průběžná integrace a průběžné doručování (vytváření skutečných cloudových aplikací pomocí Windows Azure).](../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery.md) Elektronická kniha kapitoly, který představuje průběžnou integraci a průběžné doručování.
- [Nasazení webu Windows Azure](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy). Popisuje možnosti a odkazy na prostředky pro nasazení webových projektů do modelu weby Windows Azure. Obsahuje oddíl, o automatizaci nasazení ze správy zdrojového kódu.
- [Nasazení webové aplikace v podnikových scénářích](../web-forms/overview/deployment/deploying-web-applications-in-enterprise-scenarios/deploying-web-applications-in-enterprise-scenarios.md). 40 část třídílné série kurzů, ukazuje, jak automatizovat nasazení v procesu CI pomocí sady Visual Studio 2010 a Team Foundation Server 2010.
- [V modulu Microsoft buildu: Pomocí nástroje MSBuild a Team Foundation Build tak, že Sayed Hashimi a William Bartholomew](http://msbuildbook.com). Tím je kniha, není webový prostředek, ale je základní Průvodce pro učit, jak nakonfigurovat nástroj MSBuild pro průběžné integrace.
- [Balíček rozšíření nástroje MSBuild](https://github.com/mikefourie/MSBuildExtensionPack). Zahrnuje úlohy nasazení.
- [Team Foundation Příručka pro přizpůsobení sestavení](https://aka.ms/vsarsolutions). Dokumentace podle ALM Rangers o nastavení serveru Team Foundation Server zahrnuje nasazení webu a zahrnuje kurzy a videa.
- [Transformace SlowCheetah XML ze serveru CI](http://sedodream.com/2011/12/12/SlowCheetahXMLTransformsFromACIServer.aspx) (blog Sayed Hashimi). Vysvětluje způsob používání SlowCheetah Visual Studio add-in pro transformaci app.config a další soubory XML.

Viz také [zajišťuje, že aplikace je offline během nasazování](aspnet-web-deployment-content-map.md#appoffline) později na této stránce.


<a id="transforms"></a>


## <a name="using-webconfig-transformations-to-change-settings-in-the-destination-webconfig-file-or-appconfig-file-during-deployment"></a>Chcete-li změnit nastavení v cílovém souboru Web.config nebo app.config souboru během nasazování pomocí transformace Web.config

- [Transformace souboru Web.config](../web-forms/overview/deployment/visual-studio-web-deployment/web-config-transformations.md).
- [Syntaxe transformace souboru Web.config pro projekt nasazení webu pomocí sady Visual Studio](https://msdn.microsoft.com/library/dd465326.aspx) (MSDN).
- [Web Tools 2012.2 – transformace souboru web.config](https://www.youtube.com/watch?v=HdPK8mxpKEI) (video na YouTube podle Sayed Hashimi). Ukazuje, jak nastavit a zobrazit jejich náhled transformace souboru Web.config.
- [Jak zakážu transformace Web.config](https://msdn.microsoft.com/library/ee942158.aspx#disable_web_config_transformation) (MSDN).
- [Kdy použít parametry nasazení webu místo transformace Web.config?](https://msdn.microsoft.com/library/ee942158.aspx#web_deploy_parameters) (MSDN).
- [XDT (transformaci dokumentů XML), vydáno na serveru codeplex.com](https://blogs.msdn.com/b/webdev/archive/2013/04/23/xdt-xml-document-transform-released-on-codeplex-com.aspx) (.NET Web Development and Tools blog). Oznamuje dostupnost zdrojového kódu pro modul transformace souboru Web.config a uvádí některé nástroje, které ji používají.
- [Windows Azure Web Sites: Jak aplikace řetězce a připojovacích řetězců](https://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (blog Microsoft Azure). Alternativa k souboru Web.config transformuje, pokud je vaše cílové prostředí webů Windows Azure a že chcete transformovat `appSettings` nebo `connectionStrings`.


<a id="webdeployparms"></a>


## <a name="using-web-deploy-parameters-to-change-settings-in-the-destination-web-application-during-deployment"></a>Chcete-li změnit nastavení v cílové webové aplikace během nasazení pomocí nasazení webu parametrů

- [Postupy: Nasazení webu pomocí parametrů v balíčku pro nasazení webu](https://msdn.microsoft.com/library/ff398068.aspx) (MSDN).
- [MSDeploy: Postup aktualizace nastavení publikování aplikace založené na profilu publikování](http://sedodream.com/2013/03/02/MSDeployHowToUpdateAppSettingsOnPublishBasedOnThePublishProfile.aspx) (blog Sayed Hashimi). Ukazuje, jak integrovat nasazení webu parametry do sady Visual Studio publikační profily.
- [Webu nasadit Parametrizace](https://www.iis.net/learn/publish/using-web-deploy/web-deploy-parameterization) (IIS.NET webové stránky).
- [Webu nasadit Parametrizace v akci](http://vishaljoshi.blogspot.com/2010/07/web-deploy-parameterization-in-action.html) (blog Vishal Joshi).
- [Webové nasazení Parametrizace vs. Transformace souboru Web.config](http://vishaljoshi.blogspot.com/2010/06/parameterization-vs-webconfig.html) (blog Vishal Joshi).
- [Windows Azure Web Sites: Jak aplikace řetězce a připojovacích řetězců](https://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (blog Microsoft Azure). Alternativa k webové nasazení parametry, pokud je vaše cílové prostředí webů Windows Azure a vy chcete parametrizovat `appSettings` nebo `connectionStrings`.


<a id="appoffline"></a>


## <a name="making-sure-an-application-is-off-line-during-deployment"></a>Během nasazení zajišťuje, že aplikace je offline

- [Nasazení webu ASP.NET pomocí sady Visual Studio: Nasazení aktualizace kódu](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update.md). V části **nastavit aplikaci offline během nasazení.**
- [Přepnutí aplikace do offline režimu před publikováním](https://www.iis.net/learn/publish/deploying-application-packages/taking-an-application-offline-before-publishing) (webu IIS.net). Tento článek vysvětluje funkce integrovaná do nasazení webu 3.0, který automatizuje zpracování aplikace\_offline.htm souboru. Tato funkce nefunguje s vlastní aplikací\_offline.htm soubory.
- [Jak využít vaši webovou aplikaci do offline režimu během publikování](http://sedodream.com/2012/01/08/HowToTakeYourWebAppOfflineDuringPublishing.aspx) (blog Sayed Hashimi). Jak automatizovat proces pomocí vlastní aplikaci\_offline.htm souboru.
- [Web publikování aktualizací pro aplikaci v režimu offline a usechecksum](https://blogs.msdn.com/b/webdev/archive/2013/10/30/web-publishing-updates-for-app-offline-and-usechecksum.aspx) (vývoj pro Web Microsoft blogu). Další možnost používání aplikace automatizace\_offline.htm souboru.
- [Webu nasadit ve verzi RTW 3.5](https://blogs.iis.net/msdeploy/archive/2013/07/09/webdeploy-3-5-rtw.aspx) (webu IIS.net). Nová funkce v 3.5 webové nasazení vlastní aplikace\_offline.htm soubory.


<a id="databasewithweb"></a>


## <a name="deploying-a-database-or-changes-to-a-database-as-part-of-web-application-deployment"></a>Nasazení do databáze nebo změny do databáze jako součást nasazení webových aplikací

- [Konfigurace nasazení databáze v sadě Visual Studio](https://msdn.microsoft.com/library/dd394698.aspx#dbdeployment) (MSDN). Přehled možností pro nasazení databáze se webový projekt.
- [Nasazení webu ASP.NET pomocí sady Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). 12 – část série kurzů, ukazuje nasazení databáze pomocí zprostředkovatele dbDacFx a migrace Entity Framework Code First.
- [Postupy: Nasazení webového projektu pomocí jedním kliknutím publikovat v sadě Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) (MSDN).
- [Nasaďte zabezpečené aplikace ASP.NET MVC 5 s Membership, OAuth a SQL Database na web Windows Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Dlouhé kurz, který sestaví a nasadí aplikaci, která používá jeden Server SQL databáze i pro členství a aplikace data.
- [Nasazení webové aplikace ASP.NET s SQL serverem Compact pomocí sady Visual Studio](../web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12.md). 12 část třídílné série kurzů, ukazuje, jak nasadit SQL Server Compact databází a postupu při migraci z SQL Server Compact na plnou verzi systému SQL Server.

Podívejte se také nasazení webové aplikace z hlediska vytváření a instalace balíčku pro nasazení webu a nasazení webové aplikace s využitím průběžné integrace (CI) procesu dříve na této stránce.


<a id="databaseseparate"></a>


## <a name="deploying-a-database-separately-from-web-application-deployment"></a>Nasazení databáze odděleně od nasazení webových aplikací

- [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686(v=vs.103).aspx) (MSDN).
- [Vkládání dat do databázový projekt SQL Server](https://blogs.msdn.com/b/ssdt/archive/2012/02/02/including-data-in-an-sql-server-database-project.aspx) (blog týmu SQL Server Data Tools). Jak nasadit schéma a data při nasazení databáze.
- [Nasazení databáze na Windows Azure](https://docs.microsoft.com/azure/sql-database/sql-database-cloud-migrate) (webu Microsoft Azure)
- [Migrace databází do služby Windows Azure SQL Database (dřív SQL Azure)](https://msdn.microsoft.com/library/windowsazure/ee730904.aspx) (MSDN).
- [Migrace databáze SQL Azure pomocí SSDT](https://blogs.msdn.com/b/ssdt/archive/2012/04/19/migrating-a-database-to-sql-azure-using-ssdt.aspx) (blog týmu SQL Server Data Tools).
- [Migrace datově orientovaných aplikací do Windows Azure](https://msdn.microsoft.com/library/jj156154.aspx) (MSDN).
- [Migrace databáze systému SQL Server do služby Windows Azure SQL Database](https://msdn.microsoft.com/library/windowsazure/jj156160.aspx) (MSDN).


<a id="aspnetmembership"></a>


## <a name="deploying-a-web-application-that-uses-aspnet-application-services-such-as-membership-and-profiling"></a>Nasazení webové aplikace, který používá aplikace ASP.NET službami, jako je členství a profilace

- [Nasaďte zabezpečené aplikace ASP.NET MVC 5 s Membership, OAuth a SQL Database na web Windows Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data). Dlouhé kurz, který sestaví a nasadí aplikaci, která používá jeden Server SQL databáze i pro členství a aplikace data.
- [ASP.NET Identity](https://asp.net/identity/). Prostředky pro ASP.NET Identity.
- [Nasazení webu ASP.NET pomocí sady Visual Studio](../web-forms/overview/deployment/visual-studio-web-deployment/introduction.md). 12 – část série kurzů, ukazuje, jak nasadit členské databáze ASP.NET.
- [Konfigurace webu, který používá aplikační služby](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/configuring-a-website-that-uses-application-services-cs.md). Pro webový server projekty ale platí také pro projekty webových aplikací.
- [Uživatelé a role na provozním webu](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/users-and-roles-on-the-production-website-cs.md). Pro webový server projekty ale platí také pro projekty webových aplikací.


<a id="precompiling"></a>


## <a name="precompiling-for-deployment"></a>Předkompilace pro nasazení

- [Předkompilace přehled rozhraní ASP.NET Web Application Project](https://msdn.microsoft.com/library/aa983464.aspx) (MSDN).
- [Balení/publikování karta Web, vlastnosti projektu](https://msdn.microsoft.com/library/dd410108.aspx) (MSDN).
- [Pokročilé předkompilovat dialogové okno nastavení](https://msdn.microsoft.com/library/hh475319.aspx) (MSDN).


<a id="intranet"></a>


## <a name="deploying-an-intranet-web-application"></a>Nasazení webové aplikace sítě intranet

- [Ověřování organizace možnost místní (služby AD FS) pomocí technologie ASP.NET v sadě Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/) (Blog autorem je Vittorio Bertocci.).
- [Jak vytvořit intranetový server pomocí technologie ASP.NET MVC](https://msdn.microsoft.com/library/gg703322(VS.98).aspx) (MSDN). Starší writen návod pro sadu Visual Studio 2010, neodráží hlavní změny v šablonách projektů intranetu zavedena v sadě Visual Studio 2013.


<a id="automating"></a>


## <a name="automating-common-deployment-tasks-that-are-not-automated-out-of-the-box"></a>Automatizaci nejčastějších úloh nasazení, které nejsou automatizované úprav

- [Nasazení webu ASP.NET pomocí sady Visual Studio: Nasazení dalších souborů](../web-forms/overview/deployment/visual-studio-web-deployment/deploying-extra-files.md).
- [Nastavení oprávnění pro složky pro publikování webu](http://sedodream.com/2011/11/08/SettingFolderPermissionsOnWebPublish.aspx) (blog Sayed Hashimi).
- [Soubor cílů zahrnout nastavení registru pro webový projekt balíček rozšíření](https://blogs.msdn.com/webdevtools/archive/2010/02/09/how-to-extend-target-file-to-include-registry-settings-for-web-project-package.aspx) (blog nástroje pro vývoj pro Web).
- [Rozšíření transformace XML (Web.config)](http://sedodream.com/2010/09/09/ExtendingXMLWebconfigConfigTransformation.aspx) (blog Sayed Hashimi). Ukazuje postup vytvoření vlastních transformací XDT.
- [Web nástroj pro nasazení (MSDeploy) vlastní poskytovatele vzít 1](http://sedodream.com/2010/03/11/WebDeploymentToolMSDeployCustomProviderTake1.aspx) (blog Sayed Hashimi). Ukazuje, jak vytvořit vlastní poskytovatel nasazení webu.
- [Zabalení a nasazení komponent COM](https://blogs.msdn.com/webdevtools/archive/2010/03/03/how-to-package-and-deploy-com-component.aspx) (blog nástroje pro vývoj pro Web).
- [Tom, jak zabalit sestavení .NET](https://blogs.msdn.com/webdevtools/archive/2010/02/19/how-to-package-com-component.aspx) (blog nástroje pro vývoj pro Web). Postup nasazení sestavení do mezipaměti GAC.
- [Skript si všechno – inicializace Your Windows virtuální počítač Azure pro vaše webového serveru pomocí služby IIS, Web Deploy a další věci](http://www.tugberkugurlu.com/archive/script-out-everything-initialize-your-windows-azure-vm-for-your-web-server-with-iis-web-deploy-and-other-stuff) (blog Tugberk Ugurlu).


<a id="configuringservers"></a>


## <a name="configuring-web-servers-so-that-developers-can-deploy-web-applications-to-them-using-web-deploy"></a>Konfigurace webových serverů, aby vývojáři můžete nasadit webové aplikace k nim pomocí nasazení webu

- [Instalace a konfigurace nasazení webu pro správce a bez oprávnění správce nasazení](https://www.iis.net/learn/install/installing-publishing-technologies/installing-and-configuring-web-deploy) (webu IIS.net).


<a id="hostingprovider"></a>


## <a name="configuring-servers-for-a-hosting-provider"></a>Konfigurace serverů pro poskytovatele hostingu

- [Microsoft ASP.NET 4 Průvodce nasazením hostování](https://go.microsoft.com/fwlink/?LinkId=191365) (Microsoft Download Center).
- [Generování souboru profilu XML](https://www.iis.net/learn/web-hosting/joining-the-web-hosting-gallery/generate-a-profile-xml-file) (webu IIS.net).


<a id="troubleshooting"></a>


## <a name="troubleshooting-deployment-problems"></a>Řešení potíží s chybami nasazení

- [Řešení potíží s weby Windows Azure v sadě Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio) (webu Microsoft Azure).
- [Nasazení webu ASP.NET pomocí sady Visual Studio: Řešení potíží s](../web-forms/overview/deployment/visual-studio-web-deployment/troubleshooting.md).
- [Odstraňování běžných problémů s webovým nasazení](https://www.iis.net/learn/publish/troubleshooting-web-deploy/troubleshooting-common-problems-with-web-deploy).
- [Webu nasadit kódy chyb](https://www.iis.net/learn/publish/troubleshooting-web-deploy/web-deploy-error-codes) (webu IIS.net).
- [Nejčastější dotazy k nasazení pro Visual Studio a ASP.NET Web](https://msdn.microsoft.com/library/ee942158.aspx) (MSDN).
- [Základní rozdíly mezi službou IIS a serveru ASP.NET Development Server](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md).
- [Nejčastější rozdíly mezi vývojovou a provozní konfigurací](../web-forms/overview/older-versions-getting-started/deploying-web-site-projects/common-configuration-differences-between-development-and-production-cs.md).
- [Hostování aplikací ASP.NET na úrovni Medium Trust](http://www.4guysfromrolla.com/articles/100307-1.aspx) (4 Guys webu Rolla).


<a id="gettinghelp"></a>


## <a name="getting-help-with-a-specific-deployment-question"></a>Získat pomoc s otázkou konkrétní nasazení

- [Fórum ASP.NET konfigurace a nasazení](https://forums.asp.net/26.aspx/1?Configuration and Deployment).
- [StackOverflow.com](http://www.StackOverflow.com).


<a id="additional"></a>


## <a name="additional-resources"></a>Další prostředky

Tato část obsahuje odkazy na další zdroje, které jsou užitečné pro dostávat další informace o tom, jak používat nástroje pro nasazení sady Visual Studio a služby IIS.

Těchto blozích často obsahují informace o nasazení webu pomocí sady Visual Studio:

- [Webové nástroje pro vývoj v blogu Microsoft](https://blogs.msdn.com/b/webdevtools/).
- [Blog SAYED Hashimi](http://www.sedodream.com/).

Následující zdroje poskytují dokumentaci k nasazení webu, rozhraní služby IIS, která sadě Visual Studio používá k provedení úlohy nasazení projektu webové aplikace. Můžete klást otázky o nasazení webu v [fóra nástroje Web Deployment Tool](https://go.microsoft.com/fwlink/?LinkId=149411) na webu IIS.net.

- [Představení webu nasadit](https://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy).
- [Instalace a konfigurace webové nasazení](https://www.iis.net/learn/install/installing-publishing-technologies/installing-and-configuring-web-deploy).
- [Skripty Powershellu pro automatizaci webových nasadit nastavení](https://www.iis.net/learn/publish/using-web-deploy/powershell-scripts-for-automating-web-deploy-setup).
- [Webové nástroje pro nasazení](https://go.microsoft.com/fwlink/?LinkId=151481). Nejvyšší úrovně tabulky uzlu obsah pro nasazení webu dokumentace na webu TechNet. Zahrnuje užitečné referenční informace, ale většina následující článek knihovny TechNet stránek se neaktualizovaly let.
- [Namespace Microsoft.Web.Deployment](https://go.microsoft.com/fwlink/?LinkId=148630). Dokumentace k rozhraní API ještě není aktualizovaný od verze 1.0.
- [Blog týmu pro nasazení webové aplikace Microsoft](https://blogs.iis.net/msdeploy/default.aspx).
- [Publikovat na webu IIS.net kartu](https://www.iis.net/learn/publish).
