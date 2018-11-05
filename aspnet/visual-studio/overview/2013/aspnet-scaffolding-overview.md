---
uid: visual-studio/overview/2013/aspnet-scaffolding-overview
title: Generování uživatelského rozhraní ASP.NET v sadě Visual Studio 2013 | Dokumentace Microsoftu
author: Rick-Anderson
description: Generování uživatelského rozhraní technologie ASP.NET je nová funkce, která je zahrnutá v sadě Visual Studio 2013.
ms.author: riande
ms.date: 04/09/2014
ms.assetid: a41ec9d4-8287-4f31-9e2a-460e7b7f04be
msc.legacyurl: /visual-studio/overview/2013/aspnet-scaffolding-overview
msc.type: authoredcontent
ms.openlocfilehash: 4246a52ad1d10da04a2a214f9dba6a935a9e9e72
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020972"
---
<a name="aspnet-scaffolding-in-visual-studio-2013"></a>Vygenerování uživatelského rozhraní ASP.NET v sadě Visual Studio 2013
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Generování uživatelského rozhraní technologie ASP.NET je nová funkce, která je zahrnutá v sadě Visual Studio 2013.


## <a name="overview"></a>Přehled

ASP.NET generování uživatelského rozhraní je architektura generování kódu pro webové aplikace ASP.NET. Visual Studio 2013 obsahuje generátory předinstalované kódu pro projekty MVC a webového rozhraní API. Generování uživatelského rozhraní do svého projektu přidat, pokud chcete rychle přidat kód, který komunikuje s datovými modely. Pomocí generování uživatelského rozhraní můžete snížit množství doba k rozvoji standardních datových operací ve vašem projektu.

Ve výchozím nastavení Visual Studio 2013 nepodporuje generování kódu pro projekt webových formulářů, ale generování uživatelského rozhraní můžete použít s webovými formuláři do projektu přidat závislosti MVC nebo instalaci rozšíření. Oba přístupy jsou uvedeny níže.

Visual Studio 2013 Update 2 (aktuálně RC) poskytuje možnost rozšíření ASP.NET generování uživatelského rozhraní pro splnění požadavků vašeho scénáře. Pomocí této funkce můžete vytvořit šablonu přizpůsobené generování uživatelského rozhraní a přidejte ho do dialogového okna Přidat vygenerované uživatelské rozhraní nového. V rámci vlastní šablony zadejte kód, který se generuje při přidání vygenerované položky. Další informace najdete v tématu [vytváří generátor vlastní pro Visual Studio](https://go.microsoft.com/fwlink/p/?LinkId=395029).

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat ASP.NET generování uživatelského rozhraní, musíte mít:

- Microsoft Visual Studio 2013
- Webové nástroje pro vývojáře (součást výchozí instalace sady Visual Studio 2013)
- ASP.NET Web Frameworks and Tools 2013 (součást výchozí instalace sady Visual Studio 2013)

## <a name="add-a-scaffolded-item-to-mvc-or-web-api"></a>Přidání vygenerované položky MVC nebo webového rozhraní API

Chcete-li přidat vygenerované uživatelské rozhraní, klikněte pravým tlačítkem na projekt nebo složku v rámci projektu a vyberte **přidat** – **novou vygenerovanou položku**, jak je znázorněno na následujícím obrázku.

![Přidat vygenerované uživatelské rozhraní položky](aspnet-scaffolding-overview/_static/image1.png)

Z **přidat vygenerované uživatelské rozhraní** okna, vyberte typ vygenerované uživatelské rozhraní pro přidání.

![Vyberte typ vygenerované uživatelské rozhraní](aspnet-scaffolding-overview/_static/image2.png)

**Přidat kontroler** okno poskytuje možnost vybrat možnosti pro vytvoření kontroleru, včetně toho, jestli chcete používat nové asynchronní funkce z Entity Framework 6.

![Přidání kontroleru](aspnet-scaffolding-overview/_static/image3.png)

Příslušných tříd a stránky jsou vytvářeny pro váš scénář. Například následující obrázek ukazuje kontroler MVC a zobrazení, které byly vytvořené pomocí generování uživatelského rozhraní pro třídu modelu s názvem videa.

![Vytvořené soubory](aspnet-scaffolding-overview/_static/image4.png)

## <a name="add-a-scaffolded-item-to-web-forms"></a>Přidání vygenerované položky do webových formulářů

Pokud chcete přidat generování uživatelského rozhraní, který generuje kód webových formulářů, musíte nainstalovat rozšíření sady Visual Studio nebo přidat závislosti MVC. Oba přístupy jsou uvedeny níže, ale je potřeba jenom proveďte jednu z těchto přístupů.

### <a name="web-forms-scaffolding-extension"></a>Webové formuláře, generování uživatelského rozhraní rozšíření

Můžete nainstalovat rozšíření sady Visual Studio, který vám umožní používat generování uživatelského rozhraní se projekt webových formulářů. V sadě Visual Studio, vyberte **nástroje** a potom **rozšíření a aktualizace**. Z tohoto dialogového okna Vyhledat Galerie sady Visual Studio pro **generování uživatelského rozhraní webových formulářů**.

![instalace generování uživatelského rozhraní webových formulářů](aspnet-scaffolding-overview/_static/image5.png)

Další informace najdete v tématu [generování uživatelského rozhraní webových formulářů](https://go.microsoft.com/fwlink/p/?LinkId=396478).

### <a name="mvc-dependencies"></a>Závislosti MVC

Chcete-li přidat závislosti MVC, **přidat** - **novou vygenerovanou položku**. V okně Přidat vygenerované uživatelské rozhraní vyberte **závislosti MVC**, jak je znázorněno níže.

![Přidat závislosti MVC](aspnet-scaffolding-overview/_static/image6.png)

Existují dvě možnosti pro generování uživatelského rozhraní MVC; Minimální a úplné. Pokud vyberete minimální, pouze balíčky NuGet a odkazy pro architekturu ASP.NET MVC se přidají do vašeho projektu. Pokud vyberete možnost Úplná minimální závislosti jsou přidány, a také požadované soubory obsahu pro projekt MVC. Snadno používat generování uživatelského rozhraní, vyberte úplný závislosti.

![Vyberte úplný závislosti](aspnet-scaffolding-overview/_static/image7.png)

Po přidání závislostí, uvidíte **readme.txt** souboru. Pečlivě postupujte podle pokynů v tomto souboru a ujistěte se, že projekt pracuje správně.

Po dokončení kroků v souboru readme.txt můžete přidat nová vygenerovaná položka, jak je znázorněno v předchozí části o MVC a webového rozhraní API. Automaticky generované zobrazení a kontroler bude fungovat správně v rámci svého projektu.

## <a name="tutorials"></a>Kurzy

Vytvoření vlastní generátor najdete v tématu [vytváří generátor vlastní pro Visual Studio](https://go.microsoft.com/fwlink/p/?LinkId=395029).

Přizpůsobit generované soubory, přečtěte si článek [přizpůsobení generované soubory z tohoto dialogového okna novou vygenerovanou položku](https://blogs.msdn.com/b/webdev/archive/2013/12/26/how-to-customize-the-generated-files-from-the-new-scaffolded-item-dialog.aspx).

Příklad použití generování uživatelského rozhraní s **Database First vývoj**, naleznete v tématu [EF Database First s ASP.NET MVC](../../../mvc/overview/getting-started/database-first-development/setting-up-database.md).

Příklad použití generování uživatelského rozhraní v **MVC** projektu naleznete v tématu [Začínáme s ASP.NET MVC 5](../../../mvc/overview/getting-started/introduction/getting-started.md).

Příklad použití generování uživatelského rozhraní v **webového rozhraní API** projektu naleznete v tématu [vytvořit rozhraní REST API se směrováním atributů ve webovém rozhraní API 2](../../../web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing.md).
