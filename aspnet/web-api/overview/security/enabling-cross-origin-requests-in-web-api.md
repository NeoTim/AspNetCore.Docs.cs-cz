---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: Povolení žádostí nepůvodního zdroje v rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: MikeWasson
description: Ukazuje, jak k podpoře sdílení prostředků mezi zdroji (CORS) v rozhraní ASP.NET Web API.
ms.author: riande
ms.date: 01/29/2019
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 97a0027194b019b09e220493dcb593e682027fe3
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428444"
---
<a name="enable-cross-origin-requests-in-aspnet-web-api-2"></a>Povolení žádostí nepůvodního v ASP.NET Web API 2
====================
podle [Mike Wasson](https://github.com/MikeWasson)

> Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení je volána *zásada stejného zdroje*a brání škodlivým webům ve čtení citlivých dat z jiné lokality. Ale v některých případech můžete chtít nechat ostatních lokalit volání webového rozhraní API.
>
> [Mezi sdílení zdrojů původu](http://www.w3.org/TR/cors/) (CORS) je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje. Pomocí CORS, server explicitně můžou některé požadavky cross-origin zatímco jiné odmítnout. CORS je bezpečnější a flexibilnější, než starší techniky, jako [JSONP](http://en.wikipedia.org/wiki/JSONP). Tento kurz ukazuje postupy při povolení CORS v aplikace webového rozhraní API.
>
> ## <a name="software-used-in-the-tutorial"></a>V tomto kurzu použili softwaru
>
> - [Visual Studio](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - Web API 2.2

## <a name="introduction"></a>Úvod

Tento kurz ukazuje, že podpora CORS v rozhraní ASP.NET Web API. Začneme tím, že vytvoříte dva projekty ASP.NET – jeden volané "Webová služba", který je hostitelem kontroler Web API, a ostatní volané "WebClient", která volá webové služby. Protože jsou dvě aplikace hostované v různých doménách, je požadavek AJAX z webový klient webové služby žádosti nepůvodního zdroje.

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a>Co je "stejné zdroj"?

Dvě adresy URL mají stejný původ, pokud mají stejné schémata, hostitele a porty. ([RFC 6454](http://tools.ietf.org/html/rfc6454))

Tyto dvě adresy URL mají stejného původu:

- `http://example.com/foo.html`
- `http://example.com/bar.html`

Tyto adresy URL mají různé zdroje než ta předchozí dvě:

- `http://example.net` -Jinou doménu
- `http://example.com:9000/foo.html` -Jiný port
- `https://example.com/foo.html` -Jiné schéma
- `http://www.example.com/foo.html` – Různé subdomény

> [!NOTE]
> Aplikace Internet Explorer nebere v úvahu port při porovnání zdrojů.

## <a name="create-the-webservice-project"></a>Vytvoření projektu webové služby

> [!NOTE]
> Této části se předpokládá, že už víte, jak vytvořit projekty webového rozhraní API. Pokud ne, přečtěte si téma [Začínáme s rozhraním ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).

1. Spusťte sadu Visual Studio a vytvořte nový **webová aplikace ASP.NET (.NET Framework)** projektu.
2. V **nová webová aplikace ASP.NET** dialogové okno, vyberte **prázdný** šablony projektu. V části **přidat složky a základní odkazy pro**, vyberte **webového rozhraní API** zaškrtávací políčko.

   ![ASP.NET dialogové okno nového projektu v sadě Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog.png)

3. Přidat kontroler Web API s názvem `TestController` následujícím kódem:

   [!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

4. Můžete spustit aplikaci místně nebo nasazení do Azure. (Pro snímky obrazovky v tomto kurzu, aplikace nasadí do Azure App Service Web Apps.) Pokud chcete ověřit, že je pracovní webové rozhraní API, přejděte na `http://hostname/api/test/`, kde *název hostitele* je doména, kam jste nasadili aplikaci. Měli byste vidět text odpovědi &quot;získat: Testovací zpráva&quot;.

   ![Webové prohlížeče zobrazující zkušební zprávy](enabling-cross-origin-requests-in-web-api/_static/image4.png)

## <a name="create-the-webclient-project"></a>Vytvoření projektu WebClient

1. Vytvořte další **webová aplikace ASP.NET (.NET Framework)** projektu a vyberte **MVC** šablony projektu. Volitelně vyberte **změna ověřování** > **bez ověřování**. Pro účely tohoto kurzu nepotřebujete ověřování.

   ![Šablona MVC v dialogu Nový projekt ASP.NET v sadě Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog-mvc.png)

2. V **Průzkumníka řešení**, otevřete soubor *Views/Home/Index.cshtml*. Nahraďte kód v tomto souboru následujícím kódem:

   [!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

   Pro *serviceUrl* proměnné, použijte identifikátor URI aplikace webové služby.

3. Místní spuštění aplikace WebClient nebo ji publikovat na jiný web.

Po kliknutí na tlačítko "Vyzkoušet" požadavek AJAX se odešle do aplikace webové služby pomocí protokolu HTTP metody uvedené v rozevíracím seznamu (GET, POST a PUT). To umožňuje prozkoumat různé požadavky cross-origin. Aplikace webové služby v současné době nepodporuje CORS, takže pokud kliknete na tlačítko, zobrazí se vám chyba.

![Chyba "Zkuste to" v prohlížeči](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> Pokud sledujete přenos pomocí protokolu HTTP v nástroji, jako jsou [Fiddler](https://www.telerik.com/fiddler), uvidíte, že do prohlížeče odeslat požadavek na získání a úspěšného vykonání požadavku, ale volání jazyka AJAX vrátí chybu. Je důležité pochopit, že zásada stejného zdroje nezabraňuje prohlížeče z *odesílání* požadavku. Místo toho brání aplikaci v zobrazení *odpovědi*.

![Ladicí program webové aplikaci Fiddler zobrazují webové požadavky](enabling-cross-origin-requests-in-web-api/_static/image8.png)

## <a name="enable-cors"></a>Povolení CORS

Nyní Pojďme povolení CORS v app webové služby. Nejprve přidejte balíček CORS NuGet. V aplikaci Visual Studio z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**. V okně konzoly Správce balíčků zadejte následující příkaz:

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

Tento příkaz nainstaluje nejnovější balíček a aktualizuje všechny závislosti, včetně základní webové rozhraní API knihovny. Použití `-Version` příznak pro cílení na konkrétní verzi. Vyžaduje balíček CORS webového rozhraní API 2.0 nebo novější.

Otevřete soubor *aplikace\_Start/WebApiConfig.cs*. Přidejte následující kód, který **WebApiConfig.Register** metody:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

V dalším kroku přidejte **[EnableCors]** atribut `TestController` třídy:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

Pro *zdroje* parametrů, použijte identifikátor URI, kam jste nasadili webový klient aplikace. Díky tomu požadavky cross-origin z WebClient, při stále Probíhá zakazování všech ostatních požadavků mezi doménami. Později, můžu budete popisují parametry **[EnableCors]** podrobněji.

Nezahrnují dopředné lomítko na konci *zdroje* adresy URL.

Znovu nasaďte aktualizovanou aplikaci webové služby. Není nutné aktualizovat WebClient. Nyní požadavek AJAX z WebClient uspěli. Metody GET a PUT, POST všechny povolené.

![Webové prohlížeče zobrazující úspěšné testovací zpráva](enabling-cross-origin-requests-in-web-api/_static/image9.png)

## <a name="how-cors-works"></a>Jak funguje CORS

Tato část popisuje, co se stane, že v požadavku CORS, na úrovni zprávy HTTP. Je důležité pochopit, jak funguje CORS, takže můžete nakonfigurovat **[EnableCors]** atribut správně a odstraňování potíží, pokud věci nefungují podle očekávání.

Specifikace CORS zavádí několik nové hlavičky protokolu HTTP, které umožňují požadavky cross-origin. Pokud je prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky cross-origin; nemusíte dělat nic zvláštního v kódu jazyka JavaScript.

Tady je příklad žádosti nepůvodního zdroje. Hlavička "Origin" dává domény, lokality, která odeslala žádost.

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

Pokud server umožňuje, aby žádosti, nastaví hlavičky Access-Control-Allow-Origin. Hodnotu této hlavičky odpovídá hlavička počátečního nebo je hodnota zástupného znaku "\*", což znamená, že jakýkoli původ je povolen.

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

Pokud odpověď neobsahuje hlavičky Access-Control-Allow-Origin, požadavek AJAX selže. Konkrétně v prohlížeči zakazuje požadavku. I v případě, že server vrátí úspěšné odpovědi, prohlížeč není zpřístupnit odpověď do klientské aplikace.

**Předběžných požadavků**

U některých požadavků CORS prohlížeč odešle požadavek další, nazývá "předběžný požadavek", předtím, než odešle skutečnou žádost pro prostředek.

Prohlížeči můžete přeskočit předběžný požadavek, pokud jsou splněny následující podmínky:

- Metoda žádosti je GET, HEAD nebo POST, *a*
- Aplikace nemá nastaven záhlaví požadavku než přijmout, Accept-Language, jazyka obsahu Content-Type nebo poslední-Event-ID, *a*
- Hlavička Content-Type (Pokud nastavit) je jedním z následujících akcí:

    - Application/x--www-form-urlencoded
    - multipart/formuláře data
    - text/plain

Pravidlo o hlavičky požadavku se vztahuje na hlavičky, které aplikace nastaví voláním **setRequestHeader** na **XMLHttpRequest** objektu. (Specifikaci CORS volá tyto "Autor hlavičky žádosti".) Toto pravidlo se nevztahuje na záhlaví *prohlížeče* můžete nastavit, jako je například uživatelského agenta, hostitele nebo Content-Length.

Tady je příklad předběžný požadavek:

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

Přípravné požadavek používá metodu HTTP OPTIONS. Obsahuje dva speciálními záhlavími:

- Access-Control-Request-Method: Metoda protokolu HTTP, který se použije pro aktuálního požadavku.
- Access-Control-Request-Headers: Seznam hlaviček požadavku, který *aplikace* nastavit u aktuálního požadavku. (Znovu, nezahrnuje hlavičky, které nastaví prohlížeče.)

Tady je příklad odpovědi, za předpokladu, že server umožňuje žádosti:

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

Odpověď obsahuje hlavičku přístup – ovládací prvek-Allow-Methods, který obsahuje seznam povolených metod a volitelně hlavičky Access-Control-povolit-Headers, která zobrazuje povolené hlavičky. Pokud je předběžný požadavek úspěšné, prohlížeč odesílá skutečnou žádost, jak je popsáno výše.

Nástroje se běžně používá k testování koncových bodů s předběžné požadavky OPTIONS (například [Fiddler](https://www.telerik.com/fiddler) a [Postman](https://www.getpostman.com/)) Neodesílat požadované záhlaví možnosti ve výchozím nastavení. Ujistěte se, že `Access-Control-Request-Method` a `Access-Control-Request-Headers` záhlaví se posílají se požadavek a hlavičky možnosti komunikace s aplikací prostřednictvím služby IIS.

Pro konfiguraci IIS a aplikace ASP.NET pro příjem a zpracování požadavků na možnost povolit, přidejte následující konfiguraci na aplikaci *web.config* soubor `<system.webServer><handlers>` části:

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

Odebrání `OPTIONSVerbHandler` zabrání zpracování možnosti žádostí služby IIS. Nahrazení `ExtensionlessUrlHandler-Integrated-4.0` umožňuje možnosti požadavky k dosažení aplikace, protože výchozí registraci modulu umožňuje pouze požadavky GET, HEAD, POST a ladění pomocí adresy URL bez přípony.

## <a name="scope-rules-for-enablecors"></a>Pravidla rozsahu pro [EnableCors]

CORS můžete povolit každou akci, na kontroler nebo globálně pro všechny kontrolery rozhraní Web API ve vaší aplikaci.

**Za akci**

Chcete-li povolit CORS pro jednu akci, nastavte **[EnableCors]** atributu na metodu akce. Následující příklad umožňuje použití CORS pro `GetItem` pouze metody.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

**Na kontroler**

Pokud nastavíte **[EnableCors]** na třídu kontroleru se vztahuje na všechny akce v kontroleru. K zákazu sdílení CORS pro akci, přidejte **[DisableCors]** atribut na akci. Následující příklad umožňuje použití CORS pro každou metodu s výjimkou `PutItem`.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

**Globálně**

Povolení CORS pro všechny řadiče webové rozhraní API ve vaší aplikaci, předejte **EnableCorsAttribute** instance na **EnableCors** metody:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

Pokud jste nastavili atribut na více než jednoho oboru, je pořadí podle priority:

1. Akce
2. Kontroler
3. Globální

## <a name="set-the-allowed-origins"></a>Nastavte povolené zdroje

*Zdroje* parametr **[EnableCors]** atribut určuje, jaké zdroje jsou povoleny pro přístup k prostředku. Hodnota je čárkou oddělený seznam Povolené zdroje.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

Můžete také použít zástupný znak hodnotu "\*" požadavky všechny původy.

Pečlivě zvažte předtím, než žádosti z původu. To znamená, že doslova všechny weby mohl provádět volání AJAX do webového rozhraní API.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

## <a name="set-the-allowed-http-methods"></a>Nastavte povolené metody HTTP

*Metody* parametr **[EnableCors]** atribut určuje, jaké metody HTTP je povolen přístup k prostředku. Pokud chcete povolit všechny metody, použijte hodnotu zástupný znak "\*". Následující příklad umožňuje pouze požadavky GET a POST.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

## <a name="set-the-allowed-request-headers"></a>Nastavit hlavičku povolené žádosti

V tomto článku je popsáno dříve jak předběžný požadavek může obsahovat hlavičku Access-Control-Request-Headers, výpis hlavičky protokolu HTTP, nastavte aplikací (takzvaný ", vytvářet hlavičky žádosti"). *Záhlaví* parametr **[EnableCors]** atribut určuje, která autorovi požadavku záhlaví jsou povolena. Chcete-li povolit všechny hlavičky, nastavte *záhlaví* na "\*". Na seznamu povolených IP adres konkrétní záhlaví, nastavte *záhlaví* do seznamu Povolené hlavičky oddělené čárkami:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

Prohlížeče však nejsou zcela konzistentní v tom, jak nastavují Access-Control-Request-Headers. Například Chrome aktuálně obsahuje "zdroj". FireFox nezahrnuje hlavičky standardních, jako je například "Přijmout", i v případě, že aplikace nastaví ve skriptu.

Pokud nastavíte *záhlaví* pro nic jiného než "\*", měli byste zahrnout alespoň "přijímat", "content-type" a "zdroj" a navíc jakékoli vlastní hlavičky, které chcete podporovat.

## <a name="set-the-allowed-response-headers"></a>Nastavit hlavičky odpovědi povolené

Ve výchozím prohlížeči nezveřejňuje všechny hlavičky odpovědí do aplikace. Hlavičky odpovědi, které jsou k dispozici ve výchozím nastavení jsou:

- Cache-Control
- Jazyk obsahu
- Typ obsahu
- Vypršení platnosti
- Datum poslední změny
- Direktiva pragma

Specifikace CORS volá tyto [hlavičky odpovědi jednoduché](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header). Chcete-li zpřístupnit dalších hlaviček pro aplikaci, nastavte *exposedHeaders* parametr **[EnableCors]**.

V následujícím příkladu, kontroler společnosti `Get` metoda nastaví vlastní hlavičku s názvem "X-Custom-Header". Ve výchozím nastavení nebude prohlížeč vystavit toto záhlaví v žádosti nepůvodního zdroje. Pokud chcete zpřístupnit záhlaví, patří "X-Custom-Header" v *exposedHeaders*.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

## <a name="pass-credentials-in-cross-origin-requests"></a>Přihlašovací údaje předávat požadavky cross-origin

Přihlašovací údaje vyžadují speciální zacházení v požadavku CORS. Ve výchozím prohlížeči neodešle žádné přihlašovací údaje se žádostí nepůvodního zdroje. Přihlašovací údaje zahrnují soubory cookie, jakož i schémat ověřování protokolu HTTP. Odesílá pověření s žádostí nepůvodního zdroje, musíte nastavit klienta **XMLHttpRequest.withCredentials** na hodnotu true.

Pomocí **XMLHttpRequest** přímo:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

V jQuery:

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

Na serveru navíc musíte povolit přihlašovací údaje. Chcete-li povolit pověření nepůvodního zdroje v rozhraní Web API, nastavte **SupportsCredentials** vlastnost na hodnotu true na **[EnableCors]** atribut:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

Pokud je tato vlastnost hodnotu true, odpověď HTTP bude obsahovat hlavičku přístup – ovládací prvek-Allow-Credentials. Tato hlavička sděluje prohlížeči, že server umožňuje přihlašovací údaje pro žádosti nepůvodního zdroje.

Pokud prohlížeč odesílá pověření, ale odpověď neobsahuje platnou hlavičku přístup – ovládací prvek-Allow-Credentials, prohlížeči nebude vystavení odpovědi do aplikace a požadavek AJAX selže.

Buďte opatrní při nastavení **SupportsCredentials** na hodnotu true, protože to znamená, že web v jiné doméně můžete poslat pověření přihlášeného uživatele webové rozhraní API jménem uživatele, aniž by uživatel znal. Specifikace CORS také uvádí nastavení *zdroje* k &quot; \* &quot; je neplatný Pokud **SupportsCredentials** má hodnotu true.

## <a name="custom-cors-policy-providers"></a>Vlastní zprostředkovatelé zásad CORS

**[EnableCors]** atribut implementuje **ICorsPolicyProvider** rozhraní. Můžete zadat vlastní implementaci tak, že vytvoříte třídu, která je odvozena z **atribut** a implementuje **ICorsProlicyProvider**.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

Teď můžete použít atribut jakéhokoli místa, že vložíte **[EnableCors]**.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

Vlastní zprostředkovatel zásad CORS například může číst nastavení z konfiguračního souboru.

Jako alternativu k použití atributů, můžete zaregistrovat **ICorsPolicyProviderFactory** objekt, který vytvoří **ICorsPolicyProvider** objekty.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

Chcete-li nastavit **ICorsPolicyProviderFactory**, volání **SetCorsPolicyProviderFactory** rozšiřující metoda při spuštění, následujícím způsobem:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

## <a name="browser-support"></a>Podpora prohlížeče

Balíček CORS webového rozhraní API je technologie na straně serveru. Také musí podporovat CORS webového prohlížeče. Naštěstí aktuální verze všech nejpoužívanějších prohlížečích obsahují [podporu CORS](http://caniuse.com/cors).
