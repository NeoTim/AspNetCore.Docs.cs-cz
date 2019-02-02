---
uid: web-api/overview/security/external-authentication-services
title: Externí ověřovací služby pomocí rozhraní ASP.NET Web API (C#) | Dokumentace Microsoftu
author: rmcmurray
description: Popisuje použití externí služby ověřování v rozhraní ASP.NET Web API.
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 3bb8eb15-b518-44f5-a67d-a27e051aedc6
msc.legacyurl: /web-api/overview/security/external-authentication-services
msc.type: authoredcontent
ms.openlocfilehash: de9b64e6c582059ec66ab352f60773f50af7b1ff
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667853"
---
# <a name="external-authentication-services-with-aspnet-web-api-c"></a>Externí ověřovací služby pomocí rozhraní ASP.NET Web API (C#)

Visual Studio 2017 a ASP.NET 4.7.2 rozšířit možnosti zabezpečení pro [jednostránkové aplikace](../../../single-page-application/index.md) (SPA) a [webového rozhraní API](../../index.md) služby pro integraci s externí ověřovací služby, které zahrnují několik Účtu OAuth/OpenID a ověřovacích služeb sociálních médií: Účty Microsoft, Twitter, Facebook nebo Google.  

### <a name="in-this-walkthrough"></a>V tomto názorném postupu

- [Pomocí externí ověřovací služby](#USING)
- [Vytvoření ukázkové webové aplikace](#SAMPLE)
- [Povolení ověřování přes síť Facebook](#FACEBOOK)
- [Povolení ověřování Google](#GOOGLE)
- [Povolení ověřování Microsoft](#MICROSOFT)
- [Povolení ověřování Twitteru](#TWITTER)
- [Další informace](#MOREINFO)

    - [Kombinování externí ověřovací služby](#COMBINE)
    - [Konfigurace služby IIS Express použít plně kvalifikovaný název domény](#FQDN)
    - [Jak získat nastavení aplikace pro ověřování Microsoft](#OBTAIN)
    - [Volitelné: Zakázat místní registrace](#DISABLE)

### <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle příkladů v tomto podrobném návodu, budete muset mít následující:

- Visual Studio 2017
- Vývojářský účet s identifikátorem aplikace a tajný klíč pro jednu z následujících ověřovací služby sociálních médií:

  - Microsoft Accounts ([https://go.microsoft.com/fwlink/?LinkID=144070](https://go.microsoft.com/fwlink/?LinkID=144070))
  - Na twitteru ([https://dev.twitter.com/](https://dev.twitter.com/))
  - Facebook ([https://developers.facebook.com/](https://developers.facebook.com/))
  - Google ([https://developers.google.com/](https://developers.google.com))

<a id="USING"></a>
## <a name="using-external-authentication-services"></a>Pomocí externí ověřovací služby

Celou řadu externí ověřovací služby, které jsou aktuálně k dispozici Nápověda na webu vývojáře ke snížení vývoj čas při vytváření nové webové aplikace. Webových uživatelů obvykle mají některé existující účty pro oblíbených webových služeb a webů sociálních médií, proto když implementuje aplikace webové služby ověřování z externí webové služby nebo webu sociálních médií, uloží dobu vývoje, který by se strávilo vytváření implementace ověřování. Použití služeb externího ověřování uloží koncoví uživatelé nebudou muset vytvořit jiný účet pro vaši webovou aplikaci a také nebudou muset pamatovat jiné uživatelské jméno a heslo.

V minulosti, vývojáři měli dvě možnosti: vytvoření vlastní implementaci ověřování, nebo zjistěte, jak do svých aplikací integrovat externí ověřovací služba. Na nejzákladnější úrovni následující diagram znázorňuje tok jednoduchého požadavku pro uživatelského agenta (webový prohlížeč), který vyžaduje informace z webové aplikace, který je nakonfigurován pro použití služby externí ověřování:

[![](external-authentication-services/_static/image2.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image1.png)

Na předchozím obrázku agent u uživatele (nebo webový prohlížeč v tomto příkladu) odešle požadavek na webovou aplikaci, který prohlížeč přesměruje na web služby externího ověřování. Uživatelský agent odešle svoje přihlašovací údaje ke službě externího ověřování, a pokud uživatelský agent se úspěšně ověřil, externí ověřovací služby přesměruje uživatelského agenta původní webové aplikace s určitou formu token, který uživatelský agent odešle do webové aplikace. Webová aplikace použije token k ověření, že uživatelský agent byl úspěšně ověřen službou externího ověřování a webové aplikace mohou použít token získáte další informace o uživatelského agenta. Po dokončení aplikace zpracování informací uživatelského agenta, webové aplikace vrátí odpovídající odpověď pro uživatelského agenta na základě nastavení autorizace.

V druhém příkladu uživatelský agent vyjedná s webovou aplikaci a externí autorizačního serveru a webová aplikace provede další komunikaci se serverem externí autorizace pro načtení Další informace o uživateli Agent:

[![](external-authentication-services/_static/image4.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image3.png)

Visual Studio 2017 a ASP.NET 4.7.2 zjednodušit integraci s externí ověřovací služby pro vývojáře tím, že poskytuje integraci u následujících služeb ověřování:

- Facebook
- Google
- Accounts Microsoft (účty Windows Live ID)
- Twitter

V příkladech v tomto návodu ukazuje, jak nakonfigurovat každou z podporovaných externí ověřovací služby pomocí nové šablony webové aplikace ASP.NET, která je dodávána se sadou Visual Studio 2017.

> [!NOTE]
> V případě potřeby budete muset přidat váš plně kvalifikovaný název domény pro nastavení pro externí ověřovací služby. Tento požadavek je založená na omezení zabezpečení u některých služeb externího ověřování, které vyžadují plně kvalifikovaný název v nastavení aplikace tak, aby odpovídala plně kvalifikovaný název, který používá vaši klienti. (Postup se liší u každé externí ověřovací služby, budete muset dokumentaci pro každou službu externí ověřování chcete zobrazit, pokud to je potřeba a jak nakonfigurovat tato nastavení.) Pokud je potřeba nakonfigurovat službu IIS Express použijte plně kvalifikovaný název domény pro testování tohoto prostředí najdete v tématu [konfigurace služby IIS Express použijte plně kvalifikovaný název domény](#FQDN) dále v tomto názorném postupu.


<a id="SAMPLE"></a>
## <a name="create-a-sample-web-application"></a>Vytvořte ukázkovou webovou aplikaci

Následující postup vás provede vytvoření ukázkové aplikace pomocí šablony webové aplikace ASP.NET a pro každou ze služeb externího ověřování později v tomto názorném postupu použijete této ukázkové aplikaci.

Spusťte Visual Studio 2017 a vyberte **nový projekt** z úvodní stránky. Nebo z **souboru** nabídce vyberte možnost **nový** a potom **projektu**.

<!-- [![](external-authentication-services/_static/image6.png "Click to Expand the Image")](external-authentication-services/_static/image5.png) -->

Když **nový projekt** dialogové okno se zobrazí, vyberte **nainstalováno** a rozbalte **Visual C#** . V části **Visual C#** vyberte **webové**. V seznamu šablon projektu vyberte **webová aplikace ASP.NET (.Net Framework)**. Zadejte název pro váš projekt a klikněte na tlačítko **OK**.

[![](external-authentication-services/_static/image71.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image71.png)

Když **nový projekt ASP.NET** jsou zobrazeny, vyberte **jednostránkové aplikace** šablonu a klikněte na tlačítko **vytvořit projekt**.

[![](external-authentication-services/_static/image72.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image72.png)

Počkejte jako Visual Studio 2017 vytvoří váš projekt.

<!-- [![](external-authentication-services/_static/image12.png "Click to Expand the Image")](external-authentication-services/_static/image11.png) -->

Po dokončení vytváření projektu sady Visual Studio 2017 otevřete *Startup.Auth.cs* soubor, který se nachází v **aplikace\_Start** složky.

Při prvním vytvoření projektu, žádné externí ověřovací služby jsou povoleny v *Startup.Auth.cs* soubor; následující obrázek znázorňuje, co váš kód může vypadat, s oddíly zvýrazněn, kam byste měli povolit externí ověřovací službu a všechny příslušné nastavení, aby bylo možné používat ověřování Accounts Microsoft, Twitter, Facebook nebo Google s vaší aplikací ASP.NET:

[!code-csharp[Main](external-authentication-services/samples/sample1.cs)]

Když stisknete klávesu F5 k sestavení a ladění webové aplikace, zobrazí se přihlašovací obrazovka, kde uvidíte, že nebyly definovány žádné externí ověřovací služby.

[![](external-authentication-services/_static/image73.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image73.png)

V následujících částech se dozvíte, jak můžete povolit každou z externí ověřovací služby, které jsou součástí technologie ASP.NET v sadě Visual Studio 2017.

<a id="FACEBOOK"></a>
## <a name="enabling-facebook-authentication"></a>Povolení ověřování přes síť Facebook

Pomocí služby Facebook ověřování musíte vytvořit účet pro vývojáře Facebooku a váš projekt bude vyžadovat ID aplikace a tajný klíč ze sítě Facebook, aby funkce. Informace o vytváření účtu sítě Facebook pro vývojáře a získání ID aplikace a tajný klíč, najdete v části [ https://go.microsoft.com/fwlink/?LinkID=252166 ](https://go.microsoft.com/fwlink/?LinkID=252166).

Po získání ID aplikace a tajný klíč, pokud chcete povolit ověřování sítě Facebook pro vaši webovou aplikaci pomocí následujících kroků:

1. Pokud váš projekt je otevřený v sadě Visual Studio 2017, otevřou *Startup.Auth.cs* souboru.

2. Vyhledejte část ověřování Facebook kódu:

    [!code-csharp[Main](external-authentication-services/samples/sample2.cs)]
3. Odeberte &quot; // &quot; znaků, které mají Odkomentujte zvýrazněné řádky kódu a pak přidejte ID aplikace a tajný klíč. Po přidání těchto parametrů můžete znovu zkompilovat váš projekt:

    [!code-csharp[Main](external-authentication-services/samples/sample3.cs)]
4. Po stisknutí klávesy F5 spusťte webovou aplikaci ve webovém prohlížeči, uvidíte, že byl definovaný jako externí ověřovací služba Facebooku:

    [![](external-authentication-services/_static/image74.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image74.png)
5. Po kliknutí **Facebook** tlačítko, prohlížeč, budete přesměrováni na přihlašovací stránku Facebooku:

    [![](external-authentication-services/_static/image22.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image21.png)
6. Po zadání vašich přihlašovacích údajů k Facebooku a klikněte na tlačítko **přihlášení**, ve webovém prohlížeči, budete přesměrováni zpět do vaší webové aplikace, které vás vyzve k **uživatelské jméno** , kterou chcete přidružit k vaší Účtu na Facebooku:

    [![](external-authentication-services/_static/image24.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image23.png)
7. Po zadání uživatelského jména a kliknutí na **zaregistrovat** tlačítko, webové aplikace se zobrazí výchozí **domovskou stránku** pro svůj účet na Facebooku:

    [![](external-authentication-services/_static/image26.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image25.png)

<a id="GOOGLE"></a>
## <a name="enabling-google-authentication"></a>Povolení ověřování Google

Pomocí služby Google ověřování musíte vytvořit účet pro vývojáře Google a váš projekt bude vyžadovat ID aplikace a tajný klíč z Googlu mohl fungovat. Informace o vytváření vývojářský účet Google a získání ID aplikace a tajný klíč, najdete v části [ https://developers.google.com ](https://developers.google.com).


Pokud chcete povolit ověřování Google pro vaši webovou aplikaci, postupujte následovně:

1. Pokud váš projekt je otevřený v sadě Visual Studio 2017, otevřou *Startup.Auth.cs* souboru.

2. Vyhledejte část kódu pro ověřování Google:

    [!code-csharp[Main](external-authentication-services/samples/sample4.cs)]
3. Odeberte &quot; // &quot; znaků, které mají Odkomentujte zvýrazněné řádky kódu a pak přidejte ID aplikace a tajný klíč. Po přidání těchto parametrů můžete znovu zkompilovat váš projekt:

    [!code-csharp[Main](external-authentication-services/samples/sample5.cs)]
4. Po stisknutí klávesy F5 spusťte webovou aplikaci ve webovém prohlížeči, uvidíte, že Google je definována jako externí ověřovací služby:

    [![](external-authentication-services/_static/image75.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image75.png)
5. Když kliknete **Google** tlačítko, prohlížeče, budete přesměrováni na přihlašovací stránku Google:

    [![](external-authentication-services/_static/image32.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image31.png)
6. Po zadání přihlašovacích údajů služby Google a klikněte na tlačítko **přihlášení**, Google vás vyzve k ověření, že vaše webová aplikace má oprávnění pro přístup k účtu Google:

    [![](external-authentication-services/_static/image34.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image33.png)
7. Po kliknutí na **přijmout**, ve webovém prohlížeči, budete přesměrováni zpět do vaší webové aplikace, které vás vyzve k **uživatelské jméno** , kterou chcete přidružit k účtu Google:

    [![](external-authentication-services/_static/image36.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image35.png)
8. Po zadání uživatelského jména a kliknutí na **zaregistrovat** tlačítko, webové aplikace se zobrazí výchozí **domovskou stránku** pro váš účet Google:

    [![](external-authentication-services/_static/image38.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image37.png)

<a id="MICROSOFT"></a>
## <a name="enabling-microsoft-authentication"></a>Povolení ověřování Microsoft

Ověřování Microsoft musíte vytvořit účet pro vývojáře a vyžaduje ID klienta a tajný kód klienta mohl fungovat. Informace o vytvoření účtu Microsoft pro vývojáře a získání ID klienta a tajný kód klienta najdete v tématu [ https://go.microsoft.com/fwlink/?LinkID=144070 ](https://go.microsoft.com/fwlink/?LinkID=144070).

Jednou jste získali váš uživatelský klíč a uživatelský tajný klíč, pokud chcete povolit ověřování Microsoft pro vaši webovou aplikaci pomocí následujících kroků:

1. Pokud váš projekt je otevřený v sadě Visual Studio 2017, otevřou *Startup.Auth.cs* souboru.

2. Vyhledejte část kódu pro ověřování Microsoft:

    [!code-csharp[Main](external-authentication-services/samples/sample6.cs)]
3. Odeberte &quot; // &quot; znaků, které mají Odkomentujte zvýrazněné řádky kódu a pak přidejte ID klienta a tajný kód klienta. Po přidání těchto parametrů můžete znovu zkompilovat váš projekt:

    [!code-csharp[Main](external-authentication-services/samples/sample7.cs)]
4. Po stisknutí klávesy F5 spusťte webovou aplikaci ve webovém prohlížeči, uvidíte, že Microsoft je definována jako externí ověřovací služby:

    [![](external-authentication-services/_static/image42.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image41.png)
5. Po kliknutí **Microsoft** tlačítko, prohlížeč, budete přesměrováni na přihlašovací stránku Microsoft:

    [![](external-authentication-services/_static/image44.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image43.png)
6. Po zadání přihlašovacích údajů společnosti Microsoft a klikněte na tlačítko **přihlášení**, budete vyzváni k ověření, že vaše webová aplikace má oprávnění pro přístup k účtu Microsoft:

    [![](external-authentication-services/_static/image46.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image45.png)
7. Po kliknutí na **Ano**, ve webovém prohlížeči, budete přesměrováni zpět do vaší webové aplikace, které vás vyzve k **uživatelské jméno** , kterou chcete přidružit k účtu Microsoft:

    [![](external-authentication-services/_static/image48.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image47.png)
8. Po zadání uživatelského jména a kliknutí na **zaregistrovat** tlačítko, webové aplikace se zobrazí výchozí **domovskou stránku** pro svůj účet Microsoft:

    [![](external-authentication-services/_static/image50.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image49.png)

<a id="TWITTER"></a>
## <a name="enabling-twitter-authentication"></a>Povolení ověřování Twitteru

Twitter ověřování musíte vytvořit účet pro vývojáře a vyžaduje uživatelský klíč a tajný klíč příjemce mohl fungovat. Informace o vytváření vývojářského účtu Twitteru a získávání vaše uživatelským klíčem a uživatelským utajením najdete v tématu [ https://go.microsoft.com/fwlink/?LinkID=252166 ](https://go.microsoft.com/fwlink/?LinkID=252166).

Jednou jste získali váš uživatelský klíč a uživatelský tajný klíč, pokud chcete povolit ověřování Twitteru pro vaši webovou aplikaci pomocí následujících kroků:

1. Pokud váš projekt je otevřený v sadě Visual Studio 2017, otevřou *Startup.Auth.cs* souboru.

2. Vyhledejte část ověřování Twitteru kódu:

    [!code-csharp[Main](external-authentication-services/samples/sample8.cs)]
3. Odeberte &quot; // &quot; znaků, které mají Odkomentujte zvýrazněné řádky kódu a pak přidejte uživatelský klíč a uživatelský tajný klíč. Po přidání těchto parametrů můžete znovu zkompilovat váš projekt:

    [!code-csharp[Main](external-authentication-services/samples/sample9.cs)]
4. Po stisknutí klávesy F5 spusťte webovou aplikaci ve webovém prohlížeči, uvidíte, že byl definovaný jako externí ověřovací služba Twitter:

    [![](external-authentication-services/_static/image54.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image53.png)
5. Po kliknutí **Twitteru** tlačítko, prohlížeč, budete přesměrováni na přihlašovací stránku Twitteru:

    [![](external-authentication-services/_static/image56.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image55.png)
6. Po zadání vaše přihlašovací údaje k Twitteru a klikněte na tlačítko **autorizovat aplikaci**, ve webovém prohlížeči, budete přesměrováni zpět do vaší webové aplikace, které vás vyzve k **uživatelské jméno** , kterou chcete přidružit k váš účet Twitteru:

    [![](external-authentication-services/_static/image58.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image57.png)
7. Po zadání uživatelského jména a kliknutí na **zaregistrovat** tlačítko, webové aplikace se zobrazí výchozí **domovskou stránku** pro váš účet Twitteru:

    [![](external-authentication-services/_static/image60.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image59.png)

<a id="MOREINFO"></a>
## <a name="additional-information"></a>Další informace

Další informace o vytváření aplikací, které používají OAuth a OpenID najdete v následující adresy URL:

- [https://go.microsoft.com/fwlink/?LinkID=252166](https://go.microsoft.com/fwlink/?LinkID=252166)
- [https://go.microsoft.com/fwlink/?LinkID=243995](https://go.microsoft.com/fwlink/?LinkID=243995)

<a id="COMBINE"></a>
### <a name="combining-external-authentication-services"></a>Kombinování externí ověřovací služby

Pro větší flexibilitu můžete definovat několik služeb externího ověřování ve stejnou dobu – to umožňuje vaší webové aplikace uživatelům k použití účtu ze všech služeb povolených externího ověřování:

[![](external-authentication-services/_static/image62.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image61.png)

<a id="FQDN"></a>
### <a name="configure-iis-express-to-use-a-fully-qualified-domain-name"></a>Konfigurace služby IIS Express pro použití plně kvalifikovaný název domény

Někteří poskytovatelé externího ověřování nepodporují testování aplikace pomocí adresy HTTP jako `http://localhost:port/`. Chcete-li tento problém obejít, můžete přidat statické mapování plně kvalifikovaný název domény (FQDN) do souboru HOSTITELŮ a nakonfigurovat možnosti projektu v sadě Visual Studio 2017 pro účely testování nebo ladění plně kvalifikovaný název domény. Chcete-li to provést, postupujte následovně:

- Přidejte statické plně kvalifikovaný název domény mapování souboru HOSTITELŮ:

  1. Otevřete příkazový řádek se zvýšenými oprávněními ve Windows.
  2. Zadejte následující příkaz:

      <kbd>notepad %WinDir%\system32\drivers\etc\hosts</kbd>
  3. Přidejte do souboru HOSTITELŮ záznam takto:

      <kbd>127.0.0.1 www.wingtiptoys.com</kbd>
  4. Uložte a zavřete soubor HOSTITELŮ.

- Konfigurace projektu Visual Studio pro použití plně kvalifikovaný název domény:

  1. Pokud váš projekt je otevřený v sadě Visual Studio 2017, klikněte na tlačítko **projektu** nabídky a pak vyberte vlastnosti projektu. Například můžete vybrat **WebApplication1 vlastnosti**.
  2. Vyberte **webové** kartu.
  3. Zadejte plně kvalifikovaný název vaší domény pro <strong>projektu adresa Url</strong>. Například, zadali byste <kbd> <http://www.wingtiptoys.com> </kbd> Pokud to bylo mapování plně kvalifikovaný název domény, který jste přidali do souboru HOSTS.

- Konfigurace služby IIS Express pro účely vaší aplikace plně kvalifikovaný název domény:

    1. Otevřete příkazový řádek se zvýšenými oprávněními ve Windows.
    2. Zadejte následující příkaz, chcete-li změnit do složky služby IIS Express:

        <kbd>CD /d &quot;%ProgramFiles%\IIS Express&quot;</kbd>
    3. Zadejte následující příkaz pro přidání plně kvalifikovaný název domény pro vaši aplikaci:

        <kbd>Appcmd.exe nastavení konfigurace-section:system.applicationHost/sites / +&quot;[name = 'WebApplication1'] .bindings. [protokol http, bindingInformation = ='*:80:www.wingtiptoys.com']&quot; /commit:apphost</kbd>

  Kde **WebApplication1** je název vašeho projektu a **bindingInformation** obsahuje číslo portu a plně kvalifikovaný název domény, které chcete použít pro testování.

<a id="OBTAIN"></a>
### <a name="how-to-obtain-your-application-settings-for-microsoft-authentication"></a>Jak získat nastavení aplikace pro ověřování Microsoft

Propojení aplikace pro Windows Live pro Microsoft Authentication je jednoduchý proces. Pokud už nejsou propojené aplikace pro Windows Live, můžete použít následující kroky:

1. Přejděte do [ https://go.microsoft.com/fwlink/?LinkID=144070 ](https://go.microsoft.com/fwlink/?LinkID=144070) a zadejte název účtu Microsoft a heslo, když se zobrazí výzva, klepněte na tlačítko **přihlášení**:

   <!--  [![](external-authentication-services/_static/image64.png "Click to Expand the Image")](external-authentication-services/_static/image63.png) -->
2. Vyberte **přidat aplikaci** a zadejte název vaší aplikace, po zobrazení výzvy a pak klikněte na tlačítko **vytvořit**:

    [![](external-authentication-services/_static/image79.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image79.png)
3. Vyberte aplikaci v rámci **název** a zobrazí se stránka Vlastnosti své aplikace.

4. Zadejte doménu přesměrování aplikace. Kopírovat **ID aplikace** a v části **tajných klíčů aplikací**vyberte **generovat heslo**. Zkopírujte heslo, které se zobrazí. ID aplikace a heslo jsou vaše ID klienta a tajný kód klienta. Vyberte **Ok** a potom **Uložit**.

    [![](external-authentication-services/_static/image77.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image77.png)

<a id="DISABLE"></a>
### <a name="optional-disable-local-registration"></a>Volitelné: Zakázat místní registrace

Aktuální funkce místní registrace technologie ASP.NET nebrání automatizovaným programům (robotům) vytváření účtů; člen například s použitím bot-ochrany před únikem informací a ověření technologií, jako například [test CAPTCHA](../../../web-pages/overview/security/16-adding-security-and-membership.md). Z tohoto důvodu byste měli odebrat místní přihlašovací formulář a registrace odkaz na přihlašovací stránce. Chcete-li to provést, otevřete  *\_Login.cshtml* stránky ve vašem projektu a pak okomentujte řádky pro panelu Místní přihlášení a odkaz na registraci. Výsledný stránka by měla vypadat jako v následující ukázce kódu:

[!code-html[Main](external-authentication-services/samples/sample10.html)]

Po nastavení panelu Místní přihlášení a odkaz na registraci byly zakázány, přihlašovací stránka zobrazí zprostředkovatele externího ověřování, které jste povolili:

[![](external-authentication-services/_static/image70.png "Kliknutím rozbalte Image")](external-authentication-services/_static/image69.png)
