---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: Práce s protokolem SSL ve webovém rozhraní API | Dokumentace Microsoftu
author: MikeWasson
description: Ukazuje, jak používat protokol SSL s ASP.NET Web API, včetně používání certifikátů SSL klienta.
ms.author: riande
ms.date: 02/22/2019
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 69c0d217f605096d968435c062ee9931f8dff75f
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744102"
---
<a name="working-with-ssl-in-web-api"></a>Práce s protokolem SSL ve webovém rozhraní API
====================
podle [Mike Wasson](https://github.com/MikeWasson)

Několik společných schémat ověřování nejsou zabezpečené přes standardní protokol HTTP. Konkrétně se základní ověřování a ověřování pomocí formulářů odesílat nezašifrované přihlašovací údaje. Zabezpečení, tato schémata ověřování *musí* používat protokol SSL. Kromě toho certifikáty SSL klienta slouží k ověřování klientů.

## <a name="enabling-ssl-on-the-server"></a>Povolení protokolu SSL na serveru

Nastavení protokolu SSL ve službě IIS 7 nebo novější:

- Vytvořit nebo získat certifikát. Pro účely testování můžete vytvořit certifikát podepsaný svým držitelem.
- Přidáte vazbu HTTPS.

Podrobnosti najdete v tématu [postupy nastavení protokolu SSL ve službě IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

Pro místní testování, můžete povolit SSL ve službě IIS Express ze sady Visual Studio. V okně Vlastnosti nastavte **povolený protokol SSL** k **True**. Poznamenejte si hodnotu **adresa URL protokolu SSL**; pomocí této adresy URL pro testování připojení prostřednictvím protokolu HTTPS.

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a>Vynucování SSL v Kontroleru webového rozhraní API

Pokud máte vazbu HTTP i HTTPS, klienti můžou stále používat HTTP pro přístup k webu. Může povolit některé prostředky je k dispozici prostřednictvím protokolu HTTP, zatímco jiné prostředky vyžadují protokol SSL. V takovém případě použijte filtr akce Pokud chcete vyžadovat protokol SSL k chráněným prostředkům. Následující kód ukazuje filtr ověřování webové rozhraní API, která kontroluje pro protokol SSL:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

Přidejte tento filtr pro všechny akce webového rozhraní API, které vyžadují protokol SSL:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a>Klientské certifikáty SSL

Protokol SSL zajišťuje ověřování pomocí certifikátů infrastruktury veřejných klíčů. Na serveru, musíte zadat certifikát, který se ověřuje serveru do klienta. Je méně běžné pro klienta, jak poskytnout certifikát na server, ale to je jednou z možností pro ověřování klientů. Použití klientských certifikátů pomocí protokolu SSL, potřebujete způsob, jak distribuovat certifikáty podepsané svým uživatelům. Pro mnoho typů aplikací neměl by to být vhodné uživatelské prostředí, ale v některých prostředích (například organizace) může být vhodná.

| Výhody | Nevýhody |
| --- | --- |
| -Certificate přihlašovací údaje jsou silnější než uživatelského jména a hesla. -SSL poskytuje kompletní zabezpečený kanál s ověřováním, zprávu, integritu a šifrování zpráv. | -Je nutné získat a správu certifikátů infrastruktury veřejných KLÍČŮ. -Klientská platforma musí podporovat klientské certifikáty SSL. |

Pokud chcete nakonfigurovat službu IIS tak, aby přijímal klientské certifikáty, otevřete Správce služby IIS a proveďte následující kroky:

1. Klikněte na uzel serveru ve stromovém zobrazení.
2. Dvakrát klikněte **nastavení SSL** funkce v prostředním podokně.
3. V části **klientské certifikáty**, vyberte jednu z těchto možností: 

    - **Přijměte**: Služba IIS bude přijímat certifikát od klienta, ale nevyžaduje, aby jeden.
    - **Vyžadovat**: V nástroji vyžadují certifikát klienta. (Chcete-li povolit tuto možnost, musíte také vybrat "Požadovat protokol SSL")

V souboru ApplicationHost.config můžete také nastavit tyto možnosti:

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

**SslNegotiateCert** příznak znamená, že služba IIS bude přijímat certifikát od klienta, ale nevyžaduje, aby jeden (odpovídá možnosti "Přijmout" ve Správci služby IIS). Chcete-li v nástroji vyžadují certifikát, nastavte **SslRequireCert** příznak. Pro účely testování můžete také nastavit tyto možnosti v rámci služby IIS Express, v místní hostitele aplikace. Konfigurační soubor umístěný ve "Documents\IISExpress\config".

### <a name="creating-a-client-certificate-for-testing"></a>Vytváří se klientský certifikát pro účely testování

Pro účely testování můžete použít [MakeCert.exe](/windows/desktop/SecCrypto/makecert) k vytvoření klientského certifikátu. Nejprve vytvořte testovací kořenové autority:

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

Použití nástroje MakeCert vás vyzve k zadání hesla privátního klíče.

V dalším kroku přidejte certifikát do testu, které ukládají serveru "důvěryhodné kořenové certifikační autority", následujícím způsobem:

1. Otevřete konzolu MMC.
2. V části **souboru**vyberte **Přidat/odebrat modul Snap-In**.
3. Vyberte **účet počítače**.
4. Vyberte **místního počítače** a dokončete průvodce.
5. V navigačním podokně rozbalte uzel "Důvěryhodné kořenové certifikační autority".
6. Na **akce** nabídky, přejděte k **všechny úkoly**a potom klikněte na tlačítko **Import** spusťte Průvodce importem certifikátu.
7. Přejděte do souboru certifikátu, TempCA.cer.
8. Klikněte na tlačítko **otevřít**, pak klikněte na tlačítko **Další** a dokončete průvodce. (Budete vyzváni znovu zadat heslo.)

Teď vytvořte klientský certifikát, který je podepsaný první certifikát:

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a>Pomocí klientských certifikátů ve webovém rozhraní API

Na straně serveru, můžete získat certifikát klienta voláním [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) na zprávy s požadavkem. Metoda vrátí hodnotu null, pokud neexistuje žádný certifikát klienta. V opačném případě vrátí **X509Certificate2** instance. Pomocí tohoto objektu můžete získat informace z certifikátu, jako je vydavatel a předmět. Tyto informace pak můžete použít pro ověřování a autorizace.

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]
