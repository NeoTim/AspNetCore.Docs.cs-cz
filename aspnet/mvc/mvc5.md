---
uid: mvc/mvc5
title: ASP.NET MVC 5 | Dokumentace Microsoftu
author: rick-anderson
description: ASP.NET MVC 5 ASP.NET MVC 5 je architektura určená k vytváření aplikací škálovatelná webů založené na standardech pomocí zavedených návrhových postupů a sílu AS...
ms.author: riande
ms.date: 10/11/2018
ms.assetid: f79fbf7f-59e5-4279-a832-c1a0294630f4
msc.legacyurl: /mvc/mvc5
msc.type: content
ms.openlocfilehash: 14fcf863a4ef5f6c9180cdf9e7b632ccdb1ebcb0
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410466"
---
<a name="aspnet-mvc-5"></a>ASP.NET MVC 5
====================
## <a name="whats-new-in-aspnet-mvc-5"></a>Co je nového v architektuře ASP.NET MVC 5

### <a name="one-aspnet"></a>One ASP.NET

Šablon projektu Web MVC bez problémů integrovat prostředí One ASP.NET. Můžete přizpůsobit projekt MVC a konfigurace ověřování pomocí Průvodce vytvořením projektu One ASP.NET. Úvodní kurz k ASP.NET MVC 5 najdete za [Začínáme s ASP.NET MVC 5](overview/getting-started/introduction/getting-started.md).

Informace o upgradu projektů MVC 4 do MVC 5 najdete v tématu [pokyny k upgradu ASP.NET MVC 4 a projektem webového rozhraní API technologie ASP.NET MVC 5 a webovým rozhraním API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md).

### <a name="aspnet-identity"></a>ASP.NET Identity

Použití ASP.NET Identity pro ověřování a správě identit se aktualizovaly šablon projektu MVC. Kurz ověřování Facebooku nebo Googlu a členství v nové rozhraní API najdete tady [vytvoření aplikace ASP.NET MVC 5 pomocí Facebooku a Google OAuth2 nebo OpenID Sign-on](overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) a [nasazení aplikace ASP.NET MVC zabezpečení s Membership, OAuth a SQL Database na web Windows Azure](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data).

### <a name="bootstrap"></a>Bootstrap

Šablona projektu MVC byl aktualizován na použití [Bootstrap](http://getbootstrap.com/) k poskytování elegantní a rychle reagující vzhled a chování, které můžete snadno přizpůsobit. Další informace najdete v tématu [Bootstrap v šablony webových projektů sady Visual Studio](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap).

### <a name="authentication-filters"></a>Filtry ověřování

[Filtry ověřování](http://www.dotnetcurry.com/showarticle.aspx?ID=957) nový typ filtru v architektuře ASP.NET MVC, která spustí před filtry autorizace v kanálu ASP.NET MVC a umožňují zadat ověřovací logiky akcích, jsou na řadič, nebo globálně pro všechny řadiče. Filtry ověřování zpracovat přihlašovací údaje v požadavku a zadejte odpovídající objekt zabezpečení. Filtry ověřování můžete také přidat výzev ověřování v reakci na neoprávněné požadavky. Zobrazit [filtry ověřování ASP.NET MVC 5](http://www.dotnetcurry.com/showarticle.aspx?ID=957), [filtry ověřování v architektuře ASP.NET MVC 5](http://theshravan.net/blog/authentication-filters-in-asp-net-mvc-5/).

### <a name="filter-overrides"></a>Přepisy filtru

Nyní můžete přepsat, jaké filtry se použijí metody dané akce nebo kontroleru zadáním [přepsání filtru](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5). Přepsání filtry zadat sadu typy filtrů, které by se neměl spouštět pro daný obor (akce nebo kontroleru). To umožňuje konfigurovat filtry, které platí globálně, ale potom vyloučit určité globální filtry z použití na konkrétní akce nebo řadiče. Zobrazit [nové přepisy filtru funkce v ASP.NET MVC 5 a ASP.NET Web API 2](https://weblogs.asp.net/imranbaloch/archive/2013/09/25/new-filter-overrides-in-asp-net-mvc-5-and-asp-net-web-api-2.aspx), [jak používat funkce technologie ASP.NET MVC 5 filtru přepsání](http://hackwebwith.net/how-to-use-the-asp-net-mvc-5-filter-overrides-feature/), a [přepisy filtru v ASP.NET MVC 5](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5)

### <a name="attribute-routing"></a>Směrování atributů

ASP.NET MVC teď podporuje [směrováním atributů](https://blogs.msdn.com/b/webdev/archive/2013/10/17/attribute-routing-in-asp-net-mvc-5.aspx), díky příspěvek podle Tim McCall, autora [AttributeRouting](https://github.com/mccalltd/AttributeRouting). Se směrováním atributů můžete zadat trasy anotací akce a kontrolery.

## <a name="new-web-project-experience"></a>Nové prostředí webového projektu

Visual Studio rozšířené možnosti vytváření nových webových projektů, spouští se v sadě Visual Studio 2013. V **nový webový projekt ASP.NET** dialogového okna můžete vybrat typ projektu chcete, nakonfigurovat libovolnou kombinaci technologie (webové formuláře, MVC, webové rozhraní API), nakonfigurujte možnosti ověřování, přidat podporu Dockeru a přidejte projekt testu jednotek.

![Nový projekt ASP.NET](mvc5/_static/new-aspnet-web-app-dialog.png)

Dialogové okno umožňuje změnit výchozí nastavení ověřování pro celou řadu šablon. Například při vytváření projektu aplikace webových formulářů ASP.NET můžete vybrat některý z následujících možností:

- Bez ověřování
- Jednotlivých uživatelských účtů (členství technologie ASP.NET nebo sociální zprostředkovatele přihlášení)
- Pracovní nebo školní účty (Active Directory v aplikaci internet)
- Ověřování Windows (Active Directory v intranetu aplikace)

![Možnosti ověřování](mvc5/_static/change-authentication-dialog.png)

Další informace o procesu pro vytváření webových projektů, naleznete v tématu [vytváření webových projektů ASP.NET v sadě Visual Studio](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md). Další informace o možnostech ověřování najdete v tématu [ASP.NET Identity](../identity/overview/index.md).

<a id="scaffold"></a>
### <a name="aspnet-scaffolding"></a>ASP.NET generování uživatelského rozhraní

ASP.NET generování uživatelského rozhraní je architektura generování kódu pro webové aplikace ASP.NET. To umožňuje snadno přidat do projektu, který komunikuje s datovým modelem často používaný kód.

Ve verzích sady Visual Studio před 2013 generování uživatelského rozhraní omezovala na projekty ASP.NET MVC. Spouští se v sadě Visual Studio 2013, můžete použít generování uživatelského rozhraní pro libovolný projekt ASP.NET, včetně webových formulářů. Visual Studio pro projekt webových formulářů aktuálně nepodporuje generování stránek, ale můžete pořád používat generování uživatelského rozhraní s webovými formuláři tak, že přidáte k projektu závislosti MVC. Podpora pro generování stránky pro webové formuláře bude přidána v budoucí verzi.

Při používání generování uživatelského rozhraní, všechny požadované závislosti jsou nainstalovány v projektu. Například pokud začínat projekt webových formulářů ASP.NET a poté použijte generování uživatelského rozhraní pro přidání Kontroleru webového rozhraní API, požadované balíčky NuGet a odkazy jsou přidány do projektu automaticky.

Chcete-li přidat generování uživatelského rozhraní MVC do projektu webových formulářů, přidejte **novou vygenerovanou položku** a vyberte **závislosti MVC 5** v dialogovém okně. Existují dvě možnosti pro generování uživatelského rozhraní MVC; **Minimální závislosti** a **úplné závislosti**. Pokud vyberete **minimální závislosti**, pouze balíčky NuGet a odkazy pro architekturu ASP.NET MVC jsou přidány do projektu. Pokud vyberete **úplné závislosti**, minimální závislosti jsou přidány a požadované soubory obsahu pro projekt MVC.

![Přidat dialog vygenerované uživatelské rozhraní v sadě Visual Studio](overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/add-scaffold.png)

Podporu pro generování uživatelského rozhraní řadiče asynchronní používá asynchronní funkce z Entity Framework 6.

Další informace a podrobné pokyny najdete v tématu [generování uživatelského rozhraní ASP.NET: Přehled](../visual-studio/overview/2013/aspnet-scaffolding-overview.md).

### <a name="get-help-and-report-issues"></a>Získání nápovědy a hlásit problémy

- [Známé problémy a rozbíjející změny seznamu](../visual-studio/overview/2013/release-notes.md#knownissues)
- Získejte pomoc a diskutovat o ASP.NET MVC 5 v [fóra](https://forums.asp.net/1146.aspx)
- [Nahlásit chybu v ASP.NET MVC 5](https://github.com/aspnet/AspNetWebStack/issues)
- [Vytvořit žádost o funkci](http://aspnet.uservoice.com/forums/41201-asp-net-mvc)

### <a name="upgrade-from-aspnet-mvc-4"></a>Upgrade z architektury ASP.NET MVC 4

Zobrazit [postupu při upgradu ASP.NET MVC 4 a webového rozhraní API projektu ASP.NET MVC 5 a webového rozhraní API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md)
