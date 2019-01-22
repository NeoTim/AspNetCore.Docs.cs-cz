---
uid: web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
title: Začínáme s rozhraním ASP.NET Web API 2 (C#)
author: MikeWasson
description: Protokol HTTP není jen pro poskytovat webové stránky. Je také výkonnou platformu pro vytváření rozhraní API, která zpřístupňují služby a data. HTTP je jednoduchý, flexibilní a ubiq...
ms.author: riande
ms.date: 11/28/2017
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
msc.type: authoredcontent
ms.openlocfilehash: 7bec95af4532535f0d620bfe6862958907466874
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444256"
---
<a name="get-started-with-aspnet-web-api-2-c"></a>Začínáme s rozhraním ASP.NET Web API 2 (C#)
====================
podle [Mike Wasson](https://github.com/MikeWasson)

[Stáhnout dokončený projekt](https://code.msdn.microsoft.com/Sample-code-of-Getting-c56ccb28)

Protokol HTTP není jen pro poskytovat webové stránky. HTTP je také výkonnou platformu pro vytváření rozhraní API, která zpřístupňují služby a data. HTTP je snadné, flexibilní a všudypřítomná. Téměř jakoukoli platformu, která si můžete představit obsahuje knihovny HTTP, takže služeb HTTP můžete oslovit širokou škálu klientů, včetně prohlížečů, mobilní zařízení a tradičních desktopových aplikací.

Rozhraní ASP.NET Web API je architektura určená k vytváření webových rozhraní API jako nadstavby rozhraní .NET Framework. V tomto kurzu použijete rozhraní ASP.NET Web API k vytvoření webového rozhraní API, který vrátí seznam produktů.

## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
- Webové rozhraní API 2

Zobrazit [vytvoření webového rozhraní API pomocí ASP.NET Core a Visual Studio pro Windows](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api) pro novější verze tohoto kurzu.

## <a name="create-a-web-api-project"></a>Vytvořte projekt webového rozhraní API

V tomto kurzu použijete rozhraní ASP.NET Web API k vytvoření webového rozhraní API, který vrátí seznam produktů. Front-endové webové stránky používá jQuery pro zobrazení výsledků.

![](tutorial-your-first-web-api/_static/image1.png)

Spusťte sadu Visual Studio a vyberte **nový projekt** z **Start** stránky. Nebo z **souboru** nabídce vyberte možnost **nový** a potom **projektu**.

V **šablony** vyberte **nainstalované šablony** a rozbalte **Visual C#** uzlu. V části **Visual C#** vyberte **webové**. V seznamu šablon projektu vyberte **webová aplikace ASP.NET**. Pojmenujte projekt "ProductsApp" a klikněte na tlačítko **OK**.

![](tutorial-your-first-web-api/_static/image2.png)

V **nový projekt ASP.NET** dialogového okna, vyberte **prázdný** šablony. V části &quot;přidat složky a základní odkazy pro&quot;, zkontrolujte **webového rozhraní API**. Klikněte na **OK**.

![](tutorial-your-first-web-api/_static/image3.png)

> [!NOTE]
> Můžete také vytvořit projekt webového rozhraní API pomocí &quot;webového rozhraní API&quot; šablony. Šablona webového rozhraní API používá rozhraní ASP.NET MVC pro zajištění stránek nápovědy rozhraní API. Používám prázdnou šablonu pro účely tohoto kurzu, protože chci ukázat webového rozhraní API bez MVC. Obecně platí nemusíte vědět, ASP.NET MVC pro použití webového rozhraní API.


## <a name="adding-a-model"></a>Přidání modelu

A *modelu* je objekt, který představuje data ve vaší aplikaci. Rozhraní ASP.NET Web API můžete automaticky serializovat modelu JSON, XML nebo jiném formátu a pak napište serializovaná data do datové části zprávy s odpovědí HTTP. Za předpokladu, může klienta číst formát serializace, může deserializovat objekt. Většina klientů můžete analyzovat, XML nebo JSON. Kromě toho klienta můžete určit, jaký formát chce nastavením hlavičky Accept ve zprávě požadavku HTTP.

Začněme vytvořením jednoduchého modelu, který představuje produkt.

Pokud již není Průzkumník řešení viditelný, klikněte na tlačítko **zobrazení** nabídky a vybereme **Průzkumníka řešení**. V Průzkumníku řešení klikněte pravým tlačítkem na složku modely. V místní nabídce vyberte **přidat** vyberte **třídy**.

![](tutorial-your-first-web-api/_static/image4.png)

Název třídy &quot;produktu&quot;. Přidejte následující vlastnosti pro `Product` třídy.

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample1.cs)]

## <a name="adding-a-controller"></a>Přidání kontroleru

V rozhraní Web API *řadič* je objekt, který zpracovává požadavky HTTP. Přidáme kontroler, který může vrátit seznam produktů nebo jednoho produktu určeným ID.

> [!NOTE]
> Pokud jste použili technologie ASP.NET MVC, jste obeznámeni s řadiči. Kontrolerů webového rozhraní API jsou podobné řadiče MVC, ale dědit **objektu ApiController** místo na třídě **řadič** třídy.

V **Průzkumníka řešení**, klikněte pravým tlačítkem na složku řadiče. Vyberte **přidat** a pak vyberte **řadič**.

![](tutorial-your-first-web-api/_static/image5.png)

V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **Kontroleru webového rozhraní API – prázdný**. Klikněte na **Přidat**.

![](tutorial-your-first-web-api/_static/image6.png)

V **přidat kontroler** dialogového okna, názvu kontroleru &quot;ProductsController&quot;. Klikněte na **Přidat**.

![](tutorial-your-first-web-api/_static/image7.png)

Základní kostry aplikace vytvoří soubor s názvem ProductsController.cs ve složce řadiče.

![](tutorial-your-first-web-api/_static/image8.png)

> [!NOTE]
> Není nutné převést vaše řadiče do složky s názvem řadiče. Název složky je jenom pohodlný způsob, jak uspořádat zdrojové soubory.


Pokud tento soubor ještě není otevřený, klikněte dvakrát na soubor otevřete. Nahraďte kód v tomto souboru následujícím kódem:

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample2.cs)]

Pro zjednodušení tento příklad produktech ukládají do pole s pevnou uvnitř třídy kontroleru. V reálné aplikaci, by samozřejmě dotaz na databázi nebo použít jiný zdroj externí data.

Kontroler definuje dvě metody, které vracejí produkty:

- `GetAllProducts` Metoda vrátí celý seznam produktů jako **IEnumerable&lt;produktu&gt;**  typu.
- `GetProduct` Metoda vyhledá jeden produkt pomocí jeho ID.

A to je vše! Máte pracovní webové rozhraní API. Každá metoda v řadiči odpovídá jedné nebo více identifikátorů URI:

| Metoda kontroleru | Identifikátor URI |
| --- | --- |
| GetAllProducts | / api/produkty |
| GetProduct | / webové rozhraníAPI/produkty/*id* |

Pro `GetProduct` metody, *id* v identifikátoru URI je zástupný symbol. Například pokud chcete získat produkt s ID 5, identifikátor URI je `api/products/5`.

Další informace o jak směruje požadavky HTTP pro metody kontroleru webového rozhraní API najdete v tématu [směrování v rozhraní ASP.NET Web API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md).

## <a name="calling-the-web-api-with-javascript-and-jquery"></a>Volání webového rozhraní API s použitím jazyka Javascript a jQuery

V této části přidáme stránku HTML, který používá AJAX, aby volala webové rozhraní API. Použijeme jQuery provádět volání jazyka AJAX a také aktualizovat na stránce s výsledky.

V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat**a pak vyberte **nová položka**.

![](tutorial-your-first-web-api/_static/image9.png)

V **přidat novou položku** dialogového okna, vyberte **webové** pod uzlem **Visual C#** a pak vyberte **stránku HTML** položky. Pojmenujte stránku &quot;index.html&quot;.

![](tutorial-your-first-web-api/_static/image10.png)

Všechno, co je v tomto souboru nahraďte následujícím kódem:

[!code-html[Main](tutorial-your-first-web-api/samples/sample3.html)]

Existuje několik způsobů, jak získat jQuery. V tomto příkladu byl použit [Microsoft Ajax CDN](../../../ajax/cdn/overview.md). Můžete také stáhnout z [ http://jquery.com/ ](http://jquery.com/)a technologie ASP.NET "Webového rozhraní API" jQuery také zahrnuje šablony projektu.

### <a name="getting-a-list-of-products"></a>Načítá se seznam produktů

Pokud chcete získat seznam produktů, odeslat požadavek HTTP GET na &quot;/api/produkty&quot;.

JQuery [getJSON](http://api.jquery.com/jQuery.getJSON/) funkce odešle požadavek AJAX. Odpověď obsahuje pole objektů JSON. `done` Funkce určuje zpětné volání, která je volána, pokud je žádost úspěšná. Při zpětném volání aktualizujeme modelu DOM se informace o produktu.

[!code-html[Main](tutorial-your-first-web-api/samples/sample4.html)]

### <a name="getting-a-product-by-id"></a>Získání produktu podle ID

Získat produktů podle ID, odeslat požadavek HTTP GET na &quot;/webové rozhraní API/produkty/*id*&quot;, kde *id* je ID produktu.

[!code-javascript[Main](tutorial-your-first-web-api/samples/sample5.js)]

Stále říkáme `getJSON` odeslat požadavek AJAX, ale tentokrát klademe ID v identifikátoru URI požadavku. Odpověď z této žádosti se JSON s reprezentací provedených jednoho produktu.

## <a name="running-the-application"></a>Spuštění aplikace

Stisknutím klávesy F5 spusťte ladění aplikace. Webové stránce by měl vypadat nějak takto:

![](tutorial-your-first-web-api/_static/image11.png)

K získání produktu podle ID, zadejte ID a klikněte na tlačítko Hledat:

![](tutorial-your-first-web-api/_static/image12.png)

Pokud zadáte neplatné ID, server vrátí chybu HTTP:

![](tutorial-your-first-web-api/_static/image13.png)

## <a name="using-f12-to-view-the-http-request-and-response"></a>Chcete-li zobrazit požadavků HTTP a odpovědí pomocí F12

Při práci se službou HTTP, může být velmi užitečná k naleznete požadavek HTTP a zprávy s požadavkem. Můžete to provést pomocí vývojářských nástrojů F12 v aplikaci Internet Explorer 9. Z aplikace Internet Explorer 9, stiskněte klávesu **F12** otevřete nástroje. Klikněte na tlačítko **sítě** kartu a stiskněte klávesu **spustit zachytávání**. Nyní přejděte zpět na webovou stránku a stiskněte klávesu **F5** k opětovnému načtení webové stránky. Aplikace Internet Explorer zaznamená přenos pomocí protokolu HTTP mezi prohlížečem a webový server. Souhrnné zobrazení se zobrazuje veškerý síťový provoz pro stránku:

![](tutorial-your-first-web-api/_static/image14.png)

Vyhledejte položku pro relativní identifikátor URI "produktů s rozhraním api / /". Vyberte tuto položku a klikněte na tlačítko **přejít na podrobnou zobrazení**. V zobrazení podrobností jsou karty zobrazíte hlaviček žádostí a odpovědí a úřadů. Například, pokud kliknete **hlavičky požadavku** kartě, zobrazí se, že klient vyžádal &quot;application/json&quot; v hlavičce Accept.

![](tutorial-your-first-web-api/_static/image15.png)

Pokud kliknete na kartě tělo odpovědi, uvidíte, jak byl seznam produktů serializovat do formátu JSON. Jiné prohlížeče mají podobné funkce. Další užitečné nástroje je [Fiddler](http://www.fiddler2.com/fiddler2/), webový ladicí proxy server. Můžete Fiddleru zobrazíte provoz protokolu HTTP a také k vytváření žádostí HTTP, která poskytuje plnou kontrolu nad hlavičky protokolu HTTP v požadavku.

## <a name="see-this-app-running-on-azure"></a>Zobrazit tuto aplikaci běžící v Azure

Chcete zobrazit dokončené web spuštěný jako živou webovou aplikaci? Kompletní verze aplikace můžete nasadit ke svému účtu Azure jednoduše kliknutím na následující tlačítko.

[![](https://azuredeploy.net/deploybutton.png)](https://deploy.azure.com/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/WebAPI-ProductsApp#/form/setup)

Potřebujete účet Azure k nasazení tohoto řešení do Azure. Pokud ještě nemáte účet, máte následující možnosti:

- [Zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.
- [Aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

## <a name="next-steps"></a>Další kroky

- Kompletní příklad, který podporuje akce POST, PUT a DELETE a zapisuje do databáze služby protokolu HTTP, naleznete v tématu [pomocí webového rozhraní API 2 s Entity Framework 6](../data/using-web-api-with-entity-framework/part-1.md).
- Další informace o vytváření plynulé a rychlost reakce webové aplikace založené na službě HTTP v tématu [jednostránkové aplikace ASP.NET](../../../single-page-application/index.md).
- Informace o tom, jak nasadit webový projekt sady Visual Studio do služby Azure App Service najdete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).
