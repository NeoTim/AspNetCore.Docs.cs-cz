---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
title: Konfigurace webového serveru pro webové nasazení publikování (Obslužná rutina nasazení webu) | Dokumentace Microsoftu
author: jrjlee
description: Toto téma popisuje postup konfigurace webového serveru Internetové informační služby (IIS) pro podporu publikování na webu a nasazení pomocí Han nasazení webové služby IIS...
ms.author: riande
ms.date: 01/29/2017
ms.assetid: 90ebf911-1c46-4470-b876-1335bd0f590f
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
msc.type: authoredcontent
ms.openlocfilehash: cf18a8860d34daa23f61e3dde13c2c79c6c0d4a5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667320"
---
# <a name="configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler"></a>Konfigurace webového serveru pro publikování nasazeného webu (obslužná rutina nasazení webu)

[Stáhnout PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Toto téma popisuje postup konfigurace webového serveru Internetové informační služby (IIS) pro podporu publikování na webu a nasazení pomocí rutiny nasazení webové služby IIS.
> 
> Při práci s nasazení webu 2.0 nebo novější, existují tři hlavní přístupy, které můžete použít k získání aplikací nebo webů na webovém serveru. Můžete:
> 
> - Použití *Webdeploy služba vzdáleného agenta*. Tento přístup vyžaduje méně konfiguraci webového serveru, ale je potřeba zadat přihlašovací údaje správce místním serveru, aby se nenasazujte nic k serveru.
> - Použití *obslužná rutina nasazení webu*. Tento přístup je mnohem složitější a vyžaduje další úsilí počáteční nastavení webového serveru. Ale při použití tohoto přístupu můžete nakonfigurovat služby IIS umožňuje uživatelům bez oprávnění správce k provedení nasazení. Obslužné rutiny nasazení webu je pouze k dispozici ve službě IIS verze 7 nebo novější.
> - Použití *offline nasazení*. Tento přístup vyžaduje nejmenší míru konfigurace webového serveru, ale správce serveru musíte ručně zkopírovat webový balíček na server a importujte ho pomocí Správce služby IIS.
> 
> Další informace o klíčové funkce, výhody a nevýhody těchto přístupů, naleznete v tématu [výběr právo přístupu k nasazení webu](choosing-the-right-approach-to-web-deployment.md).


Ano, pokud chcete povolit uživatelům bez oprávnění správce pro nasazení obsahu pro konkrétní weby služby IIS. Tento přístup je často žádoucí v tyto druhy scénářů:

- Pracovní nebo produkční prostředí, které je nepravděpodobné, že mají přístup k přihlašovací údaje správce serveru účet osoby nebo služby, která aktivuje vzdálené nasazení.
- Hostované prostředí, ve které chcete vzdáleným uživatelům umožnit aktualizovat své weby, aniž by jim poskytne plnou kontrolu nad webových serverů (nebo přístup k webovým stránkám její).

Scénáře vývoje nebo testování, nebo menší organizace, pomocí přihlašovacích údajů správce serveru nasazení obsahu je často nižší sporné. V těchto scénářích konfigurace webových serverů pro podporu nasazení s použitím [webové nasazení služby vzdálený Agent](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md) nabízí jednodušší přístup.

## <a name="task-overview"></a>Přehled úloh

Pokud chcete nakonfigurovat webový server moct přijmout a nasazení webových balíčků ze vzdáleného počítače pomocí obslužné rutiny webu nasadit přístup, bude nutné:

- Vytvořte nebo zvolte účet uživatele domény ("bez oprávnění správce uživatele") jehož přihlašovací údaje k provedení nasazení použijete.
- Nainstalujte službu IIS 7.5, včetně služby webové správy a základní ověřovací modul.
- Nainstalujte nástroj nasazení webu 2.1 nebo novější.
- Konfigurace služby webové správy, aby povoloval vzdálená připojení a spuštění služby.
- Můžete vytvořte web služby IIS pro hostování nasazeným obsahem.
- Udělení oprávnění uživatele bez oprávnění správce na vašem webu ve Správci služby IIS.
- Ujistěte se, že pravidla delegování Povolit službě a přidat a změnit obsah webu pomocí účtu uživatele bez oprávnění správce služby webové správy.
- Nakonfigurujte všechny brány firewall umožňuje příchozí připojení na port 8172.

Pro hostování ukázkové řešení ContactManager konkrétně, bude také potřeba:

- Instalace rozhraní .NET Framework 4.0.
- Instalace technologie ASP.NET MVC 3.

Postup pro každý z těchto postupů se zobrazí v tomto tématu. Úlohy a názorné postupy v tomto tématu se předpokládá, že začínáte s čistou server sestavení s Windows serverem 2016. Než budete pokračovat, ujistěte se, že:

- Windows Server 2016
- Server není připojený k doméně.
- Server má statickou IP adresu.

> [!NOTE]
> Další informace o připojení počítače k doméně najdete v tématu [připojení počítače k doméně a protokolování na](https://technet.microsoft.com/library/cc725618(v=WS.10).aspx). Další informace o konfiguraci statických IP adres najdete v tématu [nakonfigurujte statickou IP adresu](https://technet.microsoft.com/library/cc754203(v=ws.10).aspx).


## <a name="install-products-and-components"></a>Nainstalovat produkty a komponent

Tato část vás provede s instalací požadovaných produktů a komponenty na webovém serveru. Než začnete, je vhodné spuštěním služby Windows Update tak, aby byl váš server plně aktuálním stavu.

V takovém případě musíte nainstalovat tyto věci:

- **Doporučená konfigurace služby IIS 7**. Díky tomu **webového serveru (IIS)** role na webovém serveru a instalaci sadu modulů IIS a komponenty, které potřebujete k hostování aplikace ASP.NET.
- **IIS: Služba správy**. Tím se nainstaluje služby webové správy (WMSvc) ve službě IIS. Tato služba umožňuje vzdálenou správu služby IIS weby a zpřístupňuje koncový bod obslužné rutiny webu nasadit na klienty.
- **IIS: Základní ověřování**. Tím se nainstaluje modul základní ověření služby IIS. Díky tomu je služby webové správy (WMSvc) ověřit přihlašovací údaje, které zadáte.
- **Webové nástroje pro nasazení 2.1 nebo novější**. Tím se nainstaluje Web Deploy (a její podkladové spustitelný soubor MSDeploy.exe) na serveru. V rámci tohoto procesu se nainstaluje obslužné rutiny nasazení webu a integruje služby webové správy.
- **.NET Framework 4.0**. To je potřeba ke spouštění aplikací, které byly vytvořeny v této verzi rozhraní .NET Framework.
- **ASP.NET MVC 3**. Tím se nainstaluje sestavení, je potřeba spouštět aplikace MVC 3.

> [!NOTE]
> Tento návod popisuje použití instalačního programu webové platformy nainstalovat a nakonfigurovat různé součásti. I když není nutné používat instalačního programu webové platformy, zjednodušuje proces instalace automaticky zjišťuje závislosti a zajištění vždycky toho nejnovější verze produktu. Další informace najdete v tématu [instalačního programu webové platformy Microsoft](https://go.microsoft.com/?linkid=9805118).


**Chcete-li nainstalovat požadované produkty a komponenty**

1. Stáhněte a nainstalujte [instalačního programu webové platformy](https://go.microsoft.com/?linkid=9805118).
2. Po dokončení instalace, automaticky se spustí instalace webové platformy.

    > [!NOTE]
    > Instalace webové platformy teď můžete spustit kdykoli **Start** nabídky. K tomu, na **Start** nabídky, klikněte na tlačítko **všechny programy**a potom klikněte na tlačítko **instalačního programu webové platformy Microsoft**.
3. V horní části **instalačního programu webové platformy** okna, klikněte na tlačítko **produkty**.
4. Na levé straně okna, v navigačním podokně klikněte na tlačítko **architektury**.
5. V **rozhraní Microsoft .NET Framework 4** řádek, pokud rozhraní .NET Framework není nainstalovaná, klikněte na tlačítko **přidat**.

    > [!NOTE]
    > Možná jste již nainstalovali .NET Framework 4.0 prostřednictvím služby Windows Update. Pokud už je nainstalován produkt nebo komponenty, instalačního programu webové platformy se označit to tak, že nahradíte **přidat** tlačítko s textem **nainstalováno**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image1.png)
6. V **ASP.NET MVC 3 (Visual Studio 2010)** řádku, klikněte na tlačítko **přidat**.
7. V navigačním podokně klikněte na tlačítko **Server**.
8. V **IIS 7 doporučená konfigurace** řádku, klikněte na tlačítko **přidat**.
9. V **2.1 nástroj Web Deployment** řádku, klikněte na tlačítko **přidat**.
10. V **služby IIS: Základní ověřování** řádku, klikněte na tlačítko **přidat**.
11. V **služby IIS: Služba správy** řádku, klikněte na tlačítko **přidat**.
12. Klikněte na tlačítko **nainstalovat**. Instalace webové platformy zobrazí seznam produktů&#x2014;spolu s případnými přidružené závislosti&#x2014;k instalaci a zobrazí výzvu k potvrzení licenčních podmínek.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image2.png)
13. Přečtěte si licenční podmínky a pokud souhlasíte s podmínkami, klikněte na tlačítko **souhlasím**.
14. Po dokončení instalace klikněte na tlačítko **Dokončit**a pak zavřete **instalačního programu webové platformy** okna.

Pokud jste nainstalovali .NET Framework 4.0, před instalací služby IIS, budete potřebovat ke spuštění [ASP.NET IIS Registration Tool](https://msdn.microsoft.com/library/k6h9cz8h(v=VS.100).aspx) (aspnet\_regiis.exe) na nejnovější verzi technologie ASP.NET pro službu IIS zaregistrovat. Pokud to neuděláte, zjistíte, že služba IIS bude poskytovat statický obsah (jako jsou soubory HTML) bez problémů, ale vrátí **HTTP Chyba 404.0 – nenalezeno** při pokusu o procházení obsahu ASP.NET. Následující postup slouží k zajištění, že je zaregistrované technologii ASP.NET 4.0.

**Chcete-li službu IIS zaregistrovat technologii ASP.NET 4.0**

1. Klikněte na tlačítko **Start**a pak zadejte **příkazový řádek**.
2. Ve výsledcích hledání klikněte pravým tlačítkem na **příkazového řádku**a potom klikněte na tlačítko **spustit jako správce**.
3. V okně příkazového řádku, přejděte **%WINDIR%\Microsoft.NET\Framework\v4.0.30319** adresáře.
4. Zadejte následující příkaz a stiskněte klávesu Enter:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample1.cmd)]
5. Pokud máte v plánu hostování 64-bit webových aplikací v libovolném bodě, byste měli také zaregistrovat 64bitovou verzi technologie ASP.NET se službou IIS. Chcete-li to provést, v okně příkazového řádku, přejděte na **%WINDIR%\Microsoft.NET\Framework64\v4.0.30319** adresáře.
6. Zadejte následující příkaz a stiskněte klávesu Enter:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample2.cmd)]

Dobrým postupem je Windows Update znovu použijte v tuto chvíli ke stažení a instalaci dostupných aktualizací pro nové produkty a komponenty, které jste nainstalovali.

## <a name="configure-the-web-management-service"></a>Konfigurace služby webové správy

Teď, když jste nainstalovali všechno, co potřebujete, dalším krokem je konfigurace služby webové správy ve službě IIS. Na vysoké úrovni budete potřebovat k dokončení těchto úloh:

- Povolte základní ověřování na úrovni serveru.
- Konfigurace služby webové správy tak, aby přijímal vzdálená připojení.
- Spuštění služby webové správy.
- Zkontrolujte, že požadovaná pravidla delegování služby webové správy jsou na místě.

**Konfigurace služby webové správy**

1. Na **Start** nabídky, přejděte k **nástroje pro správu**a potom klikněte na tlačítko **Správce Internetové informační služby (IIS)**.
2. Ve Správci služby IIS v **připojení** podokně klikněte na uzel serveru (například **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image3.png)
3. V prostředním podokně v části **IIS**, dvakrát klikněte na panel **ověřování**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image20.png)
4. Klikněte pravým tlačítkem na **základní ověřování**a potom klikněte na tlačítko **povolit**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image5.png)
5. V **připojení** podokně klikněte na uzel serveru znovu se vraťte do nejvyšší úrovně nastavení.
6. V prostředním podokně v části **správu**, dvakrát klikněte na panel **služba správy**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image6.png)
7. V prostředním podokně vyberte **povolit vzdálená připojení**.

    > [!NOTE]
    > Pokud služby webové správy je již spuštěn, bude nutné ji nejprve zastavit.
8. V **akce** podokně klikněte na tlačítko **Start** spuštění služby webové správy.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image7.png)
9. Pokud budete vyzváni k uložení nastavení, klikněte na tlačítko **Ano**.

    > [!NOTE]
    > Můžete také nakonfigurovat automatické spouštění služby. Chcete-li to provést, otevřete konzolu služby, klikněte pravým tlačítkem na **služby webové správy**a potom klikněte na tlačítko **vlastnosti**. V **typ spouštění** rozevíracího seznamu vyberte **automatické**a potom klikněte na tlačítko **OK**.
10. V **připojení** podokně klikněte na uzel serveru znovu se vraťte do nejvyšší úrovně nastavení.
11. V prostředním podokně v části **správu**, dvakrát klikněte na panel **delegování řízení služby**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image8.png)
12. Ověřte, že v prostředním podokně obsahuje sadu pravidel.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image9.png)

    Tato pravidla umožňují Autorizovaní uživatelé služby webové správy k použití různých zprostředkovatelů pro nasazení webu. Například pro službu IIS prostřednictvím obslužné rutiny nasazení webu nasadit webové aplikace a obsah, musí být pravidlo delegování, který umožňuje všechny ověřené uživatele služby webové správy k použití **contentPath** a **iisApp**  poskytovatelů (poslední pravidlo, které se zobrazí na snímku obrazovky).

    Pokud jste nainstalovali produkty a komponenty v pořadí, které jsou popsané v tomto tématu, měli nejnovější verzi nástroje nasazení webu automaticky přidat všechna pravidla potřebná delegování služby webové správy. Pokud na stránce delegování řízení služby nezobrazuje všechna pravidla, budete muset vytvořit sami. Pokyny, jak to udělat, najdete v části [konfigurace webová obslužná rutina nasazení](https://go.microsoft.com/?linkid=9805124).
13. V **připojení** podokně klikněte na uzel serveru znovu se vraťte do nejvyšší úrovně nastavení.

## <a name="create-and-configure-an-iis-website"></a>Vytvoření a konfigurace webu služby IIS

Před nasazením webového obsahu na serveru, budete muset vytvořit a nakonfigurovat web služby IIS pro hostování daného obsahu. Nástroj nasazení webu nasadit jenom webových balíčků na existující web služby IIS; na webu jej nelze vytvořit za vás. Je také potřeba trochu další konfigurace, umožňuje váš účet bez oprávnění správce pro vzdálené nasazení obsahu. Na vysoké úrovni budete potřebovat k dokončení těchto úloh:

- Vytvořte složku v systému souborů pro hostování vašeho obsahu.
- Vytvoření webu služby IIS k poskytování obsahu a přidružit ho k místní složce.
- Udělit oprávnění k identitě fondu aplikací v místním adresáři čtení.
- Udělte nezbytná oprávnění k účtu domény, který se nasadí webová aplikace služby IIS.

I když není nic zastavení jste od nasazení obsahu na výchozí web ve službě IIS, tento přístup není doporučen pro ostatní formáty vyjma testu nebo ukázkové scénáře. Pokud chcete simulovat produkční prostředí, měli byste vytvořit nový web služby IIS s nastavením, které jsou specifické pro požadavky vaší aplikace.

**Vytvoření webu služby IIS**

1. V místním systému souborů, vytvořte složku pro uložení obsahu (například **C:\DemoSite**).
2. Na **Start** nabídky, přejděte k **nástroje pro správu**a potom klikněte na tlačítko **Správce Internetové informační služby (IIS)**.
3. Ve Správci služby IIS v **připojení** podokně rozbalte uzel serveru (například **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image10.png)
4. Klikněte pravým tlačítkem myši **lokality** uzel a potom klikněte na **přidat web**.
5. V **název lokality** zadejte název webu služby IIS (například **DemoSite**).
6. V **fyzická cesta** zadejte (nebo vyhledejte) cestu k místní složce (třeba **C:\DemoSite**).
7. V **Port** zadejte číslo portu, na kterém chcete hostovat web (například **85**).

    > [!NOTE]
    > Jsou čísla standardní port 80 pro protokol HTTP a 443 pro protokol HTTPS. Nicméně pokud hostujete tohoto webu na portu 80, musíte zastavit výchozí web pro přístup k webu.
8. Nechte **název hostitele** pole prázdné, pokud chcete nakonfigurovat záznam systému DNS (Domain Name) pro web a potom klikněte na tlačítko **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image11.png)

    > [!NOTE]
    > V produkčním prostředí budete pravděpodobně chtít hostovat svůj web na portu 80 a nakonfigurovat hlavičku hostitele, společně s odpovídající záznamy DNS. Další informace o konfiguraci hlavičky hostitele ve službě IIS 7, najdete v části [konfigurace hlavičku hostitele pro webový server (IIS 7)](https://technet.microsoft.com/library/cc753195(WS.10).aspx). Další informace o roli serveru DNS ve Windows serveru najdete v tématu [přehled serveru DNS](https://technet.microsoft.com/en-gb/library/cc770392.aspx) a [DNS Server](https://technet.microsoft.com/windowsserver/dd448607).
9. V **akce** podokně v části **upravit web**, klikněte na tlačítko **vazby**.
10. V **vazby webu** dialogové okno, klikněte na tlačítko **přidat**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image12.png)
11. V **přidat vazbu webu** dialogové okno, nastavte **IP adresu** a **Port** tak, aby odpovídaly vaší existující konfiguraci lokality.
12. V **název hostitele** zadejte název webového serveru (například **STAGEWEB1**) a potom klikněte na tlačítko **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image13.png)

    > [!NOTE]
    > První vazba webu umožňuje přístup k serveru místně s použitím IP adresy a portu nebo `http://localhost:85`. Druhá vazba webu umožňuje přístup k webu z jiných počítačů v doméně pomocí názvu počítače (například http://stageweb1:85).
13. V **vazby webu** dialogové okno, klikněte na tlačítko **Zavřít**.
14. V **připojení** podokně klikněte na tlačítko **fondy aplikací**.
15. V **fondy aplikací** podokně klikněte pravým tlačítkem na název vašeho fondu aplikací a klikněte na **základní nastavení**. Ve výchozím nastavení, bude název fondu aplikací odpovídat názvu vašeho webu (například **DemoSite**).
16. V **verze .NET CLR** seznamu vyberte **.NET CLR v4.0.30319**a potom klikněte na tlačítko **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image21.png)

    > [!NOTE]
    > Ukázkové řešení vyžaduje rozhraní .NET Framework 4.0. Toto není povinné pro nasazení webu obecně.

V pořadí pro váš web k poskytování obsahu identita fondu aplikací musí mít oprávnění ke čtení na místní složku, která ukládá obsah. Ve službě IIS 7.5 fondy aplikací s identitou fondu jedinečné ve výchozím nastavení spouští (na rozdíl od předchozích verzí služby IIS, kde by fondy aplikací obvykle běží účtem Network Service). Identita fondu aplikací není skutečný účet a není uveden na všechny seznamy uživatelů nebo skupin&#x2014;místo toho se vytvoří v dynamicky při spuštění fondu aplikací. Každá identita fondu aplikací se přidá do místní **IIS\_IUSRS** skupiny zabezpečení jako skrytá položka.

Udělení oprávnění identitě fondu aplikací pro soubor nebo složku, máte dvě možnosti:

- Přiřazení oprávnění pro identitu fondu aplikací přímo, pomocí formátu <strong>fondu aplikací služby IIS\</ strong ><em>[název fondu aplikací]</em>(například <strong>IIS AppPool\DemoSite</strong>).
- Přidělování oprávnění k **IIS\_IUSRS** skupiny.

Nejběžnější přístup je k přiřazení oprávnění pro místní **IIS\_IUSRS** skupiny, protože tento přístup umožňuje změnit fondy aplikací bez nutnosti měnit oprávnění systému souborů. Následující postup používá tento přístup na základě skupin.

> [!NOTE]
> Další informace o identity fondu aplikací ve službě IIS 7.5, naleznete v tématu [identity fondu aplikací součásti](https://go.microsoft.com/?linkid=9805123).


**Jak nakonfigurovat oprávnění složky pro web služby IIS**

1. V Průzkumníku Windows přejděte do umístění vaší místní složky.
2. Klikněte pravým tlačítkem na složku a potom klikněte na tlačítko **vlastnosti**.
3. Na **zabezpečení** klikněte na tlačítko **upravit**a potom klikněte na tlačítko **přidat**.
4. Klikněte na tlačítko **umístění**. V **umístění** dialogové okno, vyberte na místním serveru a potom klikněte na tlačítko **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image15.png)
5. V **vybrat uživatele nebo skupiny** dialogovém okně **IIS\_IUSRS**, klikněte na tlačítko **Kontrola názvů**a potom klikněte na tlačítko **OK**.
6. V <strong>oprávnění pro</strong><em>[název složky]</em> dialogové okno, Všimněte si, že byla přiřazena nová skupina <strong>čtení &amp; provést</strong>, <strong>vypsat složky obsah</strong>, a <strong>čtení</strong> oprávnění ve výchozím nastavení. Nechte beze změny a klikněte na tlačítko <strong>OK</strong>.
7. Klikněte na tlačítko <strong>OK</strong> zavřete <em>[název složky]</em><strong>vlastnosti</strong> dialogové okno.

Jako poslední úlohy musí udělit příslušná oprávnění pro uživatele bez oprávnění správce, jehož přihlašovací údaje, které použijete k nasazení obsahu. Tento uživatel vyžaduje oprávnění pro vzdálené nasazení obsahu na váš web.

**Konfigurace služby IIS webu oprávnění pro uživatele bez oprávnění správce domény**

1. Ve Správci služby IIS v **připojení** podokně klikněte pravým tlačítkem na uzel webu (například **DemoSite**), přejděte na **nasazení**a potom klikněte na tlačítko **webové konfigurace Nasazení publikování**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image16.png)
2. V **konfigurace nasazení publikování na webu** dialogovém okně vpravo od **vyberte uživatele a udělit oprávnění pro publikování** seznamu, klikněte na tlačítko se třemi tečkami.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image17.png)
3. V **povolit uživatele** dialogového okna zadejte doménu a uživatelské jméno účtu, který chcete použít k nasazení obsahu a potom klikněte na tlačítko **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image18.png)
4. V **konfigurace nasazení publikování na webu** dialogové okno, klikněte na tlačítko **nastavení**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image19.png)

    > [!NOTE]
    > Tato operace provádí dva klíčové funkce v jednom kroku. Nejprve uděluje oprávnění uživatele k úpravě webu vzdáleně přes službu webové správy podle pravidel delegování, kterou můžete prozkoumat v předchozí části. Za druhé udělí uživateli úplné řízení zdrojové složce pro web, který umožňuje uživateli přidat, upravit a nastavit oprávnění pro obsah webu.
5. V **konfigurace nasazení publikování na webu** dialogové okno, klikněte na tlačítko **Zavřít**.

## <a name="configure-firewall-exceptions"></a>Konfigurace výjimek brány Firewall

Ve výchozím nastavení webová služba správy služby IIS naslouchá na TCP port 8172. Pokud je brána Windows Firewall zapnutá na vašem webovém serveru, budete muset vytvořit nové příchozí pravidlo pro povolení provozu protokolu TCP na port 8172 (ve výchozím nastavení v bráně Windows Firewall je povolen veškerý odchozí provoz). Pokud používáte bránu firewall jiného dodavatele, budete muset vytvořit pravidla pro povolení provozu.

| Směr | Z portu | Port | Typ portu |
| --- | --- | --- | --- |
| Příchozí | Jakýkoli | 8172 | TCP |
| Odchozí | 8172 | Jakýkoli | TCP |
  

Další informace o konfiguraci pravidel brány Windows Firewall, najdete v části [konfigurace pravidel brány Firewall](https://technet.microsoft.com/library/dd448559(WS.10).aspx). Brány firewall třetích stran najdete v dokumentaci k produktu.

## <a name="conclusion"></a>Závěr

Webový server by měl nyní být připraveni přijmout vzdáleného nasazení do obslužné rutiny webu nasadit prostřednictvím služby webové správy. Před dalším pokusem o nasazení webové aplikace na server, můžete chtít zkontrolovat tyto klíčové body:

- Povolili jste základní ověřování na úrovni serveru ve službě IIS?
- Povolili jste vzdálená připojení k webové službě správy?
- Začali jste se už služby webové správy?
- Existují správu pravidla delegování služby na místě?
- Má identita fondu aplikací přístup pro čtení ke zdrojové složce pro váš web?
- Má účet uživatele bez oprávnění správce ve službě IIS oprávnění na úrovni webu?
- Brána firewall umožňuje příchozí připojení k serveru na portu TCP 8172?

## <a name="further-reading"></a>Další čtení

Informace o tom, jak nakonfigurovat vlastní soubory projektu Microsoft Build Engine (MSBuild) k nasazení webových balíčků do obslužné rutiny nasazení webu najdete v tématu [konfigurace vlastnosti nasazení pro cílové prostředí](configuring-deployment-properties-for-a-target-environment.md).

> [!div class="step-by-step"]
> [Předchozí](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)
> [další](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md)
